# unitscan

Rare-mob scanner with a flashy alert for **WotLK 3.3.5a**.

Backport of [shirsig / tablegrapes unitscan](https://github.com/Sattva-108/unitscan-TBC-2.4.3-WoTLK-3.3.5) by Sattva, with corpse ignore so dead rares do not keep alerting.

## Install

Download the release zip and extract it into `Interface\AddOns` so you have:

`Interface\AddOns\unitscan\unitscan.toc`

Then `/reload`.

## Commands

| Command | Effect |
| --- | --- |
| `/unitscan` | Toggle the GUI (User List + Stock List) |
| `/unitscan clear` | Clear 30-minute dead ignore so those rares scan again |

## Alerts

When a scanned name is in range, the screen flashes and **Unit Found!** appears.

- Left click the popup to target
- Right click to close
- Click the skull to ignore that corpse for 30 minutes
- Targeting or killing a dead rare also adds the 30-minute ignore

Custom names in `unitscan_targets` are saved. Dead ignore is saved in `unitscan_dead` (per account). Enable/Disable in the GUI is a separate permanent skip (`unitscan_ignored`) and is not cleared by `/unitscan clear`.

## Credit

shirsig, tablegrapes, Sattva (3.3.5 backport), Macumba (frFR rares).
