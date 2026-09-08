# ZPause T4

**Synced co-op pause for World at War Zombies (Plutonium T4)**

by Xep

[**Download the latest release**](https://github.com/Xeptix/ZPauseT4/releases/latest)

A port of [ZPause](https://github.com/Xeptix/ZPause), the Black Ops II pause mod, to
World at War — by way of the [Black Ops 1 port](https://github.com/Xeptix/ZPauseT5),
which is far closer to this engine. Same design, same settings, same version numbering:
v1.3 here is feature equal to v1.3 there.

Any player can pause. Any player can unpause. The state lives on `level`, so it's
identical for everyone — there's no per-client state that can desync.

- Zombies stop where they are, and the spawner slows to a crawl
- Players are locked and can't be hurt
- Powerup timers, effect countdowns and bleedout all hold
- Resumes on a 3‑2‑1 countdown with a short grace period

---

## Requirements

Plutonium T4 (World at War), zombies. No other mods or dependencies.

**Only the host needs this file.** Every part of ZPause runs on the host and reaches
everyone else as ordinary server-to-client traffic. Players joining your game install
nothing.

---

## Install

Copy the **`Plutonium`** folder from the download into:

```
%localappdata%
```

It mirrors your existing `%localappdata%\Plutonium` exactly, so Windows will ask whether
to merge — say yes. The only thing it replaces is an older `zpause.gsc`.

That puts the file here:

```
%localappdata%\Plutonium\storage\t4\raw\scripts\sp\zpause.gsc
```

**`sp`, not `zm`** — World at War zombies runs on the singleplayer script tree. That's the
same folder Plutonium's own scripts live in.

### Or run the installer

`install.bat` in the download does the same copy for you. It lists what it's about to
install, asks once, and copies — no deletes, no downloads, nothing else touched. Extract
the zip first and run it from the extracted folder; running it from inside Windows' zip
viewer won't work.

It's optional. Dragging the `Plutonium` folder across yourself is identical.


There's no mod-folder version. Plutonium's `mods` folder and its in-game Mods menu are
Black Ops II features; T4 has neither.

You don't need to restart the game to reload a script — just end the current game and
start a new one.

---

## Usage

| Action | Input |
|---|---|
| Pause / unpause | hold **crouch + melee** together for ~0.3s |
| Vote yes, while a vote is open | the same combo |
| Vote no, while a vote is open | hold **aim + melee** |

Crouching *or* prone counts, by any binding — the script reads your stance rather than a
key, so it doesn't matter which of the crouch binds you use.

The combo keeps working while you're frozen: `freezecontrols()` blocks movement and
weapon use, but button state still reaches the server. That's what lets a frozen player
resume.

**There are no chat commands.** World at War has no `say` callback for a script to bind
to, so everything is on the combos.

**Fewer combos than the other ports.** World at War has no `jumpbuttonpressed()` or
`throwbuttonpressed()` at all, so anything using jump or grenade is unavailable here.
What's left: `crouch_melee` (default), `use_melee`, `ads_melee`, `ads_use`.

### While you're down

You can't crouch from the floor, and once you've bled out the engine stops delivering
those buttons at all. Downed and spectating players switch to:

| Action | Input |
|---|---|
| Pause / unpause / vote yes | hold **use + aim** |
| Vote no | hold **use + fire** |

The HUD shows a `while down:` line whenever anybody is in that state.

---

## Voting

Off by default. `zp_vote 1` and a pause has to carry the room instead of any one player
stopping the game.

Calling a vote is the same action as pausing. The vote runs 30 seconds and everyone gets
a tally: the count, the clock, and every player with how they voted.

The bar is whichever is higher, `zp_vote_min` or `zp_vote_percent` of the players in the
game, then clamped to how many are actually present — so a lobby can't set a threshold
nobody there can clear, and solo play skips the vote entirely.

A vote ends the moment it's decided either way. Disconnects take their vote with them. A
failed vote locks out the next one briefly so it can't be spammed. Resuming doesn't need
a vote by default, so one AFK player can't strand everyone — `zp_vote_unpause 1` if you
want both directions voted.

---

## Configuration

Every setting is also a dvar of the same name, created with its default on load:

```bash
zp_countdown 5
```

The config is re-read whenever a pause is requested, so a change takes effect on the
**next pause** — no map restart needed.

| Dvar | Default | What it does |
|---|---|---|
| `zp_button_combo` | `1` | Enable the button combos. |
| `zp_combo` | `crouch_melee` | Pause combo: `crouch_melee`, `use_melee`, `ads_melee`, `ads_use`. |
| `zp_button_hold_time` | `0.3` | How long a combo must be held. |
| `zp_combo_dead` | `use_ads` | Combo used while downed or spectating. `""` = no button in that state. |
| `zp_vote_no_combo_dead` | `use_attack` | The same, for a no vote. |
| `zp_vote` | `0` | Put pauses to a vote. |
| `zp_vote_min` | `2` | Minimum yes votes, whatever the player count. |
| `zp_vote_percent` | `51` | Percent of players who must vote yes. |
| `zp_vote_time` | `30` | Seconds a vote stays open. |
| `zp_vote_unpause` | `0` | Resuming needs a vote too. |
| `zp_vote_hold` | `0` | Freeze the game while the vote runs, and resume if it fails. |
| `zp_vote_initiator_yes` | `1` | Whoever called the vote counts as a yes. |
| `zp_vote_lockout` | `10` | Seconds before another vote can be called after one fails. |
| `zp_vote_alive_only` | `1` | Leave bled-out spectators out of the threshold and the count. |
| `zp_vote_hud` | `1` | Show the vote tally on screen. |
| `zp_vote_show_voters` | `1` | List each player and how they voted. |
| `zp_vote_result_time` | `2` | Seconds the result stands on the tally afterwards. |
| `zp_vote_no_combo` | `ads_melee` | Combo for a no vote. |
| `zp_countdown` | `3` | Seconds of 3‑2‑1 before play resumes. |
| `zp_grace` | `2` | Seconds of invulnerability after resuming. |
| `zp_cooldown` | `2` | Minimum seconds between toggles. |
| `zp_max_pause_time` | `0` | Auto-resume after N seconds. `0` = unlimited. |
| `zp_drift_guard` | `1` | Snap back any AI that still manages to move. |
| `zp_spawn_delay` | `3` | Seconds of spawn delay per live zombie while paused. `0` = don't touch the spawner. |
| `zp_spawn_delay_max` | `60` | Cap on that delay. |
| `zp_stop_anims` | `1` | Cut scripted animations, so zombies can't finish tearing a barrier through the pause. |
| `zp_godmode` | `1` | Make players invulnerable while paused. |
| `zp_control_guard` | `1` | Re-apply the player freeze every tick. |
| `zp_freeze_bleedout` | `1` | Stop downed players bleeding out. |
| `zp_freeze_powerups` | `1` | Stop ground powerups timing out. |
| `zp_freeze_effects` | `1` | Hold the powerup effect timers. |
| `zp_silence_zombies` | `1` | Stop zombies growling while paused. |
| `zp_show_hint` | `1` | Tell players how to pause when they spawn. |
| `zp_hud_timer` | `1` | Show who paused and how long it's been. |
| `zp_hud_position` | `center` | Where the pause banner sits: `top`, `center`, `middle`, `bottom`, `left`, `right`. |
| `zp_vote_hud_position` | `top` | Where the vote tally sits. |
| `zp_hud_glow` | `1` | Black glow behind the HUD text. |
| `zp_hud_binds` | `1` | Draw combos as each player's bound buttons instead of words. |
| `zp_hud_panel` | `0` | Black slab behind the whole block. |
| `zp_hud_panel_alpha` | `0.45` | How opaque that slab is. |
| `zp_hud_panel_width` | `340` | How wide it is, in HUD units. |
| `zp_blackout` | `0` | Black out everyone's screen while paused (anti-scouting). |
| `zp_blur` | `1` | Blur everyone's screen while paused. |
| `zp_blur_amount` | `1.5` | Blur strength. |
| `zp_pause_sound` | `box_poof` | Played when the game is paused. `""` = silent. |
| `zp_countdown_sound` | `cha_ching` | Played on each countdown tick. |
| `zp_resume_sound` | `perks_power_on` | Played when play resumes. |

### Sounds on Nacht der Untoten

World at War drops the `zmb_` prefix Black Ops added, so the aliases are `box_poof`
rather than `zmb_box_poof`.

Map coverage is the catch. Nacht der Untoten has no perks and no magic box, and carries
almost none of the alias set the other three maps do. Only `cha_ching` is present on all
four, which is why it's the countdown tick.

`box_poof` and `perks_power_on` fit their moments far better and are on Verrückt, Shi No
Numa and Der Riese — but may be silent on Nacht. If you play it and want them audible
everywhere, set both to `cha_ching`.

### The pause clock is in minutes

`zp_hud_timer` reports in minutes — `under a minute`, `3 minutes`, `over an hour` —
rather than a live mm:ss. There's no HUD timer element on this engine, so a clock has to
be text, and every distinct string costs a configstring. A ticking second counter burns
one a second until the pool runs dry and drops the server. Minutes bound the set to about
sixty strings, all reused.

---

## How it works

**Black Ops II ships a working full-game pause** — it's what runs during a host
migration — and the original ZPause is built on that recipe. World at War has no host
migration in zombies and no `disablezombies()` builtin, so the engine-level AI freeze
isn't available. It's done in script instead:

- **The AI enforcer** holds `ignoreall`, pins every goal to where the zombie is standing,
  and snaps back anything that drifts. On Black Ops II this is a safety net around the
  engine freeze; here it *is* the freeze.
- **`zp_stop_anims`** cancels scripted animations, because a zombie tearing a barrier is
  driven by its animation rather than by pathing. Everything cancelled is released again
  on resume, or the zombie would stand there for the rest of the game.
- **The spawner is slowed, not gated.** See below.
- **The stuck-zombie watchdog.** `round_spawn_failsafe()` kills any zombie that hasn't
  moved 24 units in 30 seconds, and a paused zombie trips it every time. ZPause keeps the
  barrier-chunk timestamp fresh, which the watchdog honours, so it loops harmlessly.
- **Ground powerups.** `powerup_timeout()` is a plain `wait()` chain, so the thread is cut
  and restarted on resume.
- **Powerup effects.** Every timed powerup keeps an `_on` flag and a `_time` countdown;
  pinning the countdowns holds the on-screen timers.
- **Bleedout** is pinned so a downed player doesn't bleed out.

### The spawner

Black Ops 1 and II both close the `spawn_zombies` flag, which is what their spawn loop
blocks on. **World at War has no such flag**, and `round_spawning()` copies the round's
budget into a local before the loop starts, so that's out of reach too.

This matters: `level.zombie_total` is the round's whole allocation and it decrements per
spawn, so an ungated pause could drain an entire round into a frozen crowd at the windows
and hand it all back at once.

The one live lever is the spawn delay, which that loop re-reads every pass. It can't stop
spawning outright — a plain `wait()` can't be interrupted, so whatever delay is in flight
when you resume has to run out first. So the delay **scales with how many zombies are
already up**:

| Zombies up | Delay while paused |
|---|---|
| 1 | 5s |
| 10 | 30s |
| 20 | 60s (capped) |

Few zombies about and it stays short — little is spawning anyway and the resume is
prompt. A big horde already up and it stretches to a minute, which is exactly when nobody
wants more of them and nobody notices the gap.

---

## Notes

- **Each map carries its own scripts.** World at War has no shared zombies core — Nacht,
  Verrückt, Shi No Numa and Der Riese each have their own copy. Behaviour was checked
  against Der Riese, the most complete; Nacht predates a lot of the system.
- **Zombies stand still rather than freeze solid.** Their animation is cancelled rather
  than frozen; a true animation freeze isn't reachable from server-side GSC.
- **Not held:** the magic box close timer, trap durations and Easter egg step timers.

---

## Ports

| Game | Repo |
|---|---|
| Black Ops III (T7) | [ZPauseT7](https://github.com/Xeptix/ZPauseT7) |
| Black Ops II (T6) | [ZPause](https://github.com/Xeptix/ZPause) |
| Black Ops (T5) | [ZPauseT5](https://github.com/Xeptix/ZPauseT5) |
| World at War (T4) | ZPauseT4 — you are here |

Versions are kept in step: the same version number means the same feature set, allowing
for what each engine can actually do.

**All three in one download.** The
[Treyarch Bundle](https://github.com/Xeptix/ZPause/releases/latest) is laid out in
Plutonium's storage folder structure — drop it into `%localappdata%\Plutonium`, say yes to
the merge, and it installs whichever of the three games you have. Delete the folders for
the ones you don't.

---

## Changelog

### v1.3

First release. Feature equal to ZPause v1.3, except where the engine doesn't allow it:

- **No chat commands** — World at War has no `say` callback.
- **Fewer combos** — no jump or grenade button on this engine.
- **The spawner is slowed rather than gated**, and `zp_spawn_delay` /
  `zp_spawn_delay_max` are new here as a result.
- **No match clock hold** — World at War zombies has no match timer.
- **The pause clock is minute-granular** rather than live mm:ss.

---

## Credits

- **Xep** — author
- **Treyarch** — `_zombiemode.gsc`
- **[plutoniummod/t4-scripts](https://github.com/plutoniummod/t4-scripts)** — stock T4
  script reference
