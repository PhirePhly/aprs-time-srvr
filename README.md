# APRS Time Server
APRS daemon providing APRS Time Protocol services

This daemon implements a new proposed protocol called the APRS Time Protocol.
This service is a very low quality time source of last resort, should local
traditional time sources such as NTP or GPS not be available or needed.
The APRS Time Protocol boils down to sending an APRS message to an APRS Time
Server, which immediately replies with the current time.
The canonical server for the APRS Time Protocol will respond to the alias "TIME"

Use of this service depends on an APRS station being able to send and receive
APRS messages, and a near-by bidirectional RF-gate to gate the station's query
to the Internet and gate the response back to the local RF LAN.
Using APRS as the transport and the packet exchange depending on both
being gatewayed to and from the APRS-IS means that the received time fix
will be of VERY low quality. 

This service should be thought of as a very low quality version of something
like SNTP, where devices query a remote server once at initialization or at
long intervals to set their own internal Real-Time Clocks (RTCs).
Many RF-gates deliberately introduce several seconds of latency and jitter
when gating packets back to local RF, so acheiving an accuracy better than
30 seconds should be considered a success.

The APRS Time Protocol should be considered a replacement for users manually
setting the time on devices, not for NTP or GPS time fixes.

## Usage

To request a timestamp from the APRS Time Server, send an APRS message to the
alias TIME with one of the following commands:
* ISO - Request the current time formatted in accordance with ISO8601/RFC3339
* UNIX - Request the current time as the number of seconds since Unix Epoch
* APPROX [FORMAT] - Dither the returned timestamp by a random value in the
range of [-5,5] minutes from the current actual time. This can be useful when
large fleets of stations are configured to perform the same action at
identical clock times but it isn't actually desirable to have them syncronized.

Points to note:
* Timestamps will never be expressed to higher resolution than whole seconds.
* The query commands are not case-sensitive.
* Responses will be un-numbered APRS messages, since the APRS Time Server
will only send reponses to time queries once. If a response is not received,
standard APRS message retry algorithms should be followed from the requesting
station.

## Possible Applications

The APRS Time Protocol would be a useful source of time for devices such as:
* Mountaintop digipeaters without Internet or GPS connectivity
* Rooftop weather stations
* APRS controllers attached to repeater controllers to control their RTCs
* Portable APRS clients using single board computers lacking real time clocks
(i.e. The Raspberry Pi)

Conceivable features made possible by digipeaters having a reliable source of
time include:
* Reduce beaconing rates/distances during rush hours
* Increase the rate of announcement beacons prior to the monthly club meeting
* Weather stations could zero their counter for the 'P' "rainfall since
midnight" parameter in the weather report packet

## Examples

Station EXAMPL-1 sends a message to TIME requesting an ISO-formatted timestamp
```
EXAMPL-1>APRS::TIME     :ISO {001
TIME>APRS::EXAMPL-1 :2016-10-11T06:13:31-00:00
TIME>APRS::EXAMPL-1 :ack001
```
Note that aprstimed will generally send ISO 8601 timestamps with the local 
offset from UTC marked as unknown (by way of the -00:00 at the end).
Converting from UTC to local time is left to the client.
Clients must implement full RFC3339 ISO 8601 parsers in expectation that
aprstimed or alternative implementations may later opt to respond to APRS
Time Queries with timestamps converted to local time based on the requesting
station's location.

```
EXAMPL-2>APRS::TIME     :APPROX ISO {008
TIME>APRS::EXAMPL-2 :2016-10-11T06:12:05-00:00
TIME>APRS::EXAMPL-2 :ack008
```
By requesting an approximate time, the TIME server subtracted a random 1m26s
from the actual time before sending EXAMPL-2 a timefix packet.

Station EXAMPL-3 instead prefers to receive a Unix Epoch timestamp
```
EXAMPL-3>APRS::TIME     :UNIX
TIME>APRS::EXAMPL-3 :1476166411
```
EXAMPL-3 chose not to use a numbered APRS message, since receiving an
APRS Timestamp is in itself sufficient acknowledgement of the "UNIX" message.

## Issues / Bugs

Should APRS Time Protocol support a "Day of the Week" query and response so
clients don't need to maintain a full Julian calendar library to identify what
day it currently is?

Is there such a derth of RF-gates in the wild that bidirectional -IS traffic 
is as untenable as often foretold?

Do any APRS messaging clients have issues handling un-numbered APRS messages?

Should APRS Time Protocol worry about timefix replies getting delayed long
enough to be mistaken as a response to subsequent queries? Message retries
are indistinguishable anyways, so I don't think this problem is even solvable.

## Support

Development on the APRS Time Server takes place online at:
https://github.com/PhirePhly/aprs-time-srvr

For support, contact Kenneth Finnegan <kennethfinnegan2007@gmail.com>

