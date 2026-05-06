# ZMK Studio Messages

This repository contains the message definitions used to interract with ZMK Studio enabled devices.

## Meteorite fork

This fork adds `proto/zmk/meteorite.proto` and wires it into `studio.proto` as subsystem tag `6`.
The schema is used by the Meteorite40 editor to read and write Meteorite custom config through
ZMK Studio RPC instead of a separate transport protocol.
It also extends `core.GetDeviceInfoResponse` with a backward-compatible `capabilities` string list.
Meteorite-enabled firmware reports `meteorite.config`, and the editor only calls the Meteorite
subsystem when that capability is present.

Keep `upstream` pointed at `zmkfirmware/zmk-studio-messages` and rebase this fork when upstream
adds new Studio message tags. Do not reuse tag `6` for another local subsystem.

## TODO

* Document transport protocol used with these messages
* Release/versioning strategy
