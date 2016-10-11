# APRS Time Server
Auto-responder daemon for APRS providing time services 

The aprstimed daemon provides a basic network time service to the global APRS
network. This is done by listening for an APRS message directed at the
callsign of aprstimed, and immediately responding with the current time.
Use of this service depends on an APRS station being able to send and receive
APRS messages, and a near-by bidirectional RF-gate to gate the station's query
to the Internet and gate the response back to the local RF LAN.

This service should be thought of as a very low quality version of something
like SNTP, where devices query a remote server once at initialization or at
long intervals to set their own internal Real-Time Clocks (RTCs).
Many RF-gates deliberately introduce several seconds of latency and/or jitter
when gating packets back to local RF, so higher quality time sources such as
NTP or GPS should always be prefered over time coming from APRS.

Aprstimed supports two formats of time interchange:
* The human-readable ISO 8601 profile as defined in RFC3339
* Unix timestamp as an integer number of seconds since Unix Epoch

## Usage

To request a timestamp from the APRS Time Server, send an APRS message to the
alias TIME with one of the following commands:
* ISO - Request the current time formatted in accordance with ISO8601/RFC3339
* UNIX - Request the current time as the number of seconds since Unix Epoch
* APROX [FORMAT] - 

Points to note:
* Timestamps will never be expressed to higher resolution than whole seconds.
* The query commands are not case-sensitive.
* Responses will be un-numbered APRS messages, since the APRS Time Server
will only send reponses to time queries once. If a response is not received,
standard APRS message retry algorithms should be followed.

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

Station EXAMPL-2 instead prefers to receive a Unix Epoch timestamp
```
EXAMPL-2>APRS::TIME     :UNIX {002
TIME>APRS::EXAMPL-2 :1476166411
TIME>APRS::EXAMPL-2 :ack002
```

## Support

Development on the APRS Time Server takes place online at:
https://github.com/PhirePhly/aprs-time-srvr

For support, contact Kenneth Finnegan <kennethfinnegan2007@gmail.com>

