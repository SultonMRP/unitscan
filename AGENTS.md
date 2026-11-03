# unitscan (WotLK 3.3.5a)

Rare-mob scanner. Client interface `30300`. Almost all behavior lives in `unitscan.lua`. `unitscan.toc` lists saved variables. Do not treat a creature name as a unit id.

## How a find works

`unitscan.target(name)` calls `TargetUnit(name)` on a timer (`unitscan_defaults.CHECK_INTERVAL`, default 0.3s). If that name is in range, the client blocks the protected call and fires `ADDON_ACTION_FORBIDDEN`. That event is the only "in range" signal. It fires for a living rare and for a corpse. The scan list is zone rares in `rare_spawns` plus names in `unitscan_targets`.

A hit that is not on the dead list plays the sound, flashes the screen, and shows `unitscan_button` ("Unit Found!"). Left click targets that name. Right click closes the popup. The popup can be ctrl-dragged.

## Dead rares

Corpses must not keep alerting. Detection that the unit is dead is unreliable from the scan alone, so ignore is explicit and then remembered.

A name is ignored for 30 minutes (`DEAD_IGNORE_SECONDS`) when:

- The skull on the popup is clicked.
- The player targets that rare and it is already dead (`UnitIsDead` / `UnitIsDeadOrGhost` / `UnitIsCorpse` on `"target"` only).
- The player kills the rare they currently have targeted (`UNIT_DIED` / `UNIT_DESTROYED` while that name is the target, or `UNIT_HEALTH` on `"target"` once the corpse checks are true).

`UnitHealth <= 0` alone must not mark a fresh target dead. A newly targeted live mob can report 0 health for a moment.

Ignore storage is the saved variable `unitscan_dead`: uppercase name to server time (`GetServerTime`, else `time()`). Do not refresh the timestamp while the name is already stored. A 1-second ticker and login both drop expired entries. Expiry prints `NAME tracking now enabled`. Adding a name prints `dead NAME added to ignore` and closes the popup when that popup is the same name.

`/unitscan clear` clears `unitscan_dead` and the in-memory dead state (`known_state`, `recent_deaths`, `found`) so those rares scan again immediately.

The skull button `unitscan_dead` matches the close button: 32×32, scale 0.8, `BOTTOMLEFT` offset `-5, -5`, hit insets 8. The icon is `Interface\TargetingFrame\UI-RaidTargetingIcon_8`, inset 7px so it matches the X.

## Two different ignore lists

- `unitscan_dead` — 30-minute corpse ignore. Skull, dead target, kill, `/unitscan clear`.
- `unitscan_ignored` — permanent skip (GUI Enable/Disable, red name). `/unitscan clear` does not touch this.

## Saved variables

`unitscanDB`, `unitscan_defaults`, `unitscan_targets`, `unitscan_ignored`, `unitscan_dead`.

`found` is memory only. It stops a repeat alert for a name that is still in range. It is not why a corpse alerts.

File-scope `unitscan_dead = unitscan_dead or {}` is the default. Saved variables load after the file runs and before `ADDON_LOADED`. Do not assign `unitscan_dead = {}` again in `LOAD` or the saved list is wiped. `unitscan.LOAD` only fills a nil table and releases expired names.

## Do not

- Replace the `TargetUnit` scan with nameplates. A missing nameplate is not proof of a corpse, and that check silenced live rares.
- Call `UnitIsDead(name)`, `UnitExists(name)`, or `UnitHealth(name)` with the rare's name. On 3.3.5 those functions take `"target"`, `"mouseover"`, or `"focus"`.
- Extend the 30-minute timer every time the corpse is seen again.
- Raid-mark or retarget as part of ignore. Ignore only stores the name, prints, and hides the popup.

## Commands

`/unitscan` toggles the GUI (User List + Stock List). `/unitscan add` adds the current target to the User List (prints `no valid target` if none). `/unitscan remove` removes the current target from the User List (prints `target not tracked` if it is not on that list). `/unitscan noDR` toggles standby in dungeons and raids. `/unitscan clear` clears the 30-minute dead ignore. Custom names are added/removed in the User List. Stock rares are Enable/Disable only (red when disabled).
