---
autogen: This file was automatically generated by kernel-doc-text-to-markdown.py
kernel_version: 3.16.7-ckt4-ev3dev1
source_file: drivers/lego/sensors/lego_sensor_class.c
title: LEGO Sensor Class
---

The `lego-sensor` class provides a uniform interface for using most of the
sensors available for the EV3. The various underlying device drivers will
create a `lego-sensor` device for interacting with the sensors.

Sensors are primarily controlled by setting the `mode` and monitored by
reading the `value<N>` attributes. Values can be converted to floating point
if needed by `value<N>` / 10.0 ^ `decimals`.

### Identifying sensors

Since the name of the `sensor<N>` device node does not correspond to the port
that a sensor is plugged in to, you must look at the port_name attribute if
you need to know which port a sensor is plugged in to. However, if you don't
have more than one sensor of each type, you can just look for a matching
`name`. Then it will not matter which port a sensor is plugged in to - your
program will still work. In the case of I2C sensors, you may also want to
check the `address` since it is possible to have more than one I2C sensor
connected to a single input port at a time.

### sysfs Attributes

Sensors can be found at `/sys/class/lego-sensor/sensor<N>`, where `<N>` is
incremented each time a sensor is loaded (it is not related to which port
the sensor is plugged in to).

`address` (read-only)
: Present only for I2C devices. Returns the address of the sensor.

`bin_data` (read/write)
: Reading the file will give the same values in the `value<N>` attributes.
Use `bin_data_format` and `num_values` to determine how to interpret
the data. Writing will write data to the sensor (I2C, UART and NXT
Color sensors only).

`bin_data_format` (read-only)
: Returns the format of the values in `bin_data` for the current mode.
Possible values are:

    - `u8`: Unsigned 8-bit integer (byte)
    - `s8`: Signed 8-bit integer (sbyte)
    - `u16`: Unsigned 16-bit integer (ushort)
    - `s16`: Signed 16-bit integer (short)
    - `s16_be`: Signed 16-bit integer, big endian
    - `s32`: Signed 32-bit integer (int)
    - `float`: IEEE 754 32-bit floating point (float)

`command` (write-only)
: Sends a command to the sensor.

`commands` (read-only)
: Returns a space separated list of the valid commands for the sensor.
Returns -EOPNOTSUPP if no commands are supported.

`decimals` (read-only)
: Returns the number of decimal places for the values in the `value<N>`
attributes of the current mode.

`driver_name` (read-only)
: Returns the name of the sensor device/driver. See the list of [supported
sensors] for a complete list of drivers.

`fw_version` (read-only)
: Returns the firmware version of the sensor if available. Currently only
I2C/NXT sensors support this.

`mode` (read/write)
: Returns the current mode. Writing one of the values returned by `modes`
sets the sensor to that mode.

`modes` (read-only)
: Returns a space separated list of the valid modes for the sensor.

`num_values` (read-only)
: Returns the number of `value<N>` attributes that will return a valid value
for the current mode.

`poll_ms` (read/write)
: Returns the polling period of the sensor in milliseconds. Writing sets the
polling period. Setting to 0 disables polling. Minimum value is hard
coded as 50 msec. Returns -EOPNOTSUPP if changing polling is not supported.
Currently only I2C/NXT sensors support changing the polling period.

`port_name` (read-only)
: Returns the name of the port that the sensor is connected to.

`units` (read-only)
: Returns the units of the measured value for the current mode. May return
empty string"

`value<N>` (read-only)
: Returns the value or values measured by the sensor. Check `num_values` to
see how many values there are. Values with N >= num_values will return an
error. The values are fixed point numbers, so check `decimals` to see if
you need to divide to get the actual value.

### Events

In addition to the usual "add" and "remove" events, the kernel "change"
event is emitted when `mode` or `poll_ms` is changed. The `value<N>`
attributes change too rapidly to be handled this way and therefore do not
trigger any uevents.

[nxt-i2c-sensor]: ../nxt-i2c-sensor
[supported sensors]: /docs/sensors#supported-sensors

