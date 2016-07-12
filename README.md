xComfort gateway
================

This code implements communication with the Eaton xComfort CKOZ-00/14
Communication stick (CI stick).  The CI stick needs to be added into
the network with CKOZ-00/13.

xComfort is a wireless European home automation system, using the
868,3MHz band.  The system is regrettably closed source.  This code
was reverse engineered from a variety of sources, plus some initial
inspiration from <https://github.com/mifi/libxcomfort>.

This code has been tested with and recognizes at least the following
messages:

 * MSG_ON
 * MSG_OFF
 * MSG_UP_PRESSED
 * MSG_UP_RELEASED
 * MSG_DOWN_PRESSED
 * MSG_DOWN_RELEASED
 * MSG_STATUS

Furthermore, it can send on/off/dim% messages to devices.  Messages
for controlling eg. shutters is outlined, but untested.

The code has been written without any kind of documentation from
Eaton, and may not follow their specifications.

The CKOZ-00/14 doesn't expose or know which devices hide behind the
datapoints.  It's the user's responsibility to send correct messages
to the datapoints; this code does no validation of messages sent.
However, the devices appear to ignore messages they don't understand.
MRF can export a datapoint to device map, but I have not added support
for that, as the benefits were limited.

A simple application for forwarding events to and from an MQTT server is
provided.  This can be used eg. to interface an xComfort installation with
[homebridge-mqttswitch](https://github.com/ilcato/homebridge-mqttswitch)
or [IFTTT](https://ifttt.com/), with a little imagination.  The
application subscribes to the topics:

    "+/set/dimmer" (accepts values from 0-100)
    "+/set/switch" (accepts true or false)

and publishes on the topics:

    "[datapoint number]/get/dimmer" (value from 0-100)
    "[datapoint number]/get/switch" (true or false)

Sending `true` to topic `1/set/switch` will send a message to
datapoint 1 to turn on.  This will work for both switches and dimmers.
Sending the value `50` to `1/set/dimmer` will send a message to
datapoint 1 to set 50% dimming.  This will work only for dimmers.

Likewise, `1/get/dimmer` and `1/get/switch` will be set to the value
reported by the dimmer/switch, if and when datapoint 1 reports
changes.  Status reports are not routed in the xComfort network, so if
your CI stick is not able to reach all devices, these status messages
will be lost.

_WARNING: The firmware "RF V2.08 - USB V2.05" is buggy and will read
status reports from dimmers incorrectly as always off.  This is
resolved in the later "RF V2.10 - USB V2.05" firmware._

Copyright 2016 Karl Anders Øygard. All rights reserved.
Use of this source code is governed by a BSD-style license that can be
found in the LICENSE file.
