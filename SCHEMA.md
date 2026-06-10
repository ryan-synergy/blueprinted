# Blueprinted Takeoff JSON — Schema

The `*-takeoff.json` export is a complete, structured description of an existing
Savant system, recovered from its RacePoint Blueprint config. Everything in it
describes the **old system as programmed** — treat it as requirements + reference
for designing a replacement, and verify physical details on the site walk.

## Top level

| Key | Type | Description |
|---|---|---|
| `generator` | string | Always `"Blueprinted"`. |
| `converted` | string | Date this takeoff was generated (`YYYY-MM-DD`). |
| `client` | string | Client name, cleaned from the config filename. |
| `savantConfig` | string | Original Blueprint config name (with version/date suffixes). |
| `system` | object | Host hardware, software version, and licensing — see below. |
| `configSaved` | string | When the config was last saved on the host (`YYYY-MM-DD`). |
| `note` | string | Reminder that this is the old system, not verified hardware. |
| `counts` | object | System totals — see below. |
| `rooms` | array | One object per AV room — see below. |
| `rack` | array | Every head-end / shared component — see below. |
| `rackSourceCounts` | object | Source counts by type, e.g. `{"Apple TV": 2, "DirecTV": 3}`. Confirm on walk. |
| `cameras` | array | `{location, type}` — type is `"PTZ"` or `"Turret"`. |
| `nonAvZones` | array | Savant zone names that had no AV (lighting/relays/cameras only) — not rooms. |
| `connections` | array | The wiring graph as programmed — see below. |

## `system`

Critical for upgrade planning: host model tells you if hardware must be replaced,
Blueprint version tells you how far behind the software is, and the host UID is
what Savant licensing is keyed to.

| Key | Type | Description |
|---|---|---|
| `name` | string | The system/host name as programmed (e.g. `"Adams Anaheim Host"`). |
| `hostModel` | string | Host hardware, e.g. `"Savant Pro Host"`, `"Savant Series 3 Host"`. |
| `hostUid` | string | Host chassis UID — what Savant licenses are tied to. |
| `blueprintVersion` | string | Blueprint/da Vinci version that last saved the config (e.g. `"10.6.1"`). |
| `blueprintBuild` | string | Build number of that version. |
| `originallyCreatedWith` | string | Version the config was first created in (e.g. `"daVinci5.0 (21)"`) — shows system age. |
| `originallyCreatedDate` | string | Original creation date. |
| `programmer` | string | Document author / last editor from Blueprint. |
| `blueprintLicense` | string | Dealer Blueprint registration key. |
| `advisories` | array | Heuristic flags, e.g. legacy host or old software version. |

## `counts`

| Key | Description |
|---|---|
| `theaterRooms` | Rooms named/typed as theater. |
| `surroundZonesLocalAVR` | Non-theater surround rooms with a local AVR. |
| `twoChHouseAmp` | Stereo rooms fed from rack amplifiers. |
| `twoChLocalAmp` | Stereo rooms with their own in-room amp/receiver. |
| `tvOnlyRooms` | TV with no distributed audio. |
| `tvsTotal` | All rooms with a display. |
| `audioOnlyZones` | Audio rooms with no TV. |
| `cameras`, `rackComponents`, `connectionsMapped`, `nonAvZonesSkipped` | Self-describing totals. |

## `rooms[]`

| Key | Type | Description |
|---|---|---|
| `n` | number | Room number (matches the SiteWalk import order). |
| `name` | string | Room name (normalized, e.g. `"Family Room"`). |
| `zoneType` | string | One of: `"Theater — 5.1 local"`, `"Surround 5.1 — local"`, `"2-ch — house amp"`, `"2-ch — local amp"`, `"TV only"`, `"No AV endpoints"`. |
| `tv` | boolean | Room had a display. |
| `oldTv` | array | `{make, model, size}` — size in inches parsed from the model number, `null` if unknown. |
| `videoFeed` | string | `"matrix"` (was on video distribution), `"local"`, or `"none"`. |
| `avrs` | array | AVR/receiver models in the room, e.g. `["Integra DRX-7.1"]`. |
| `amps` | array | In-room amplifier models (e.g. Sonos Amp). |
| `speakers` | array | Speaker entries as programmed, with model strings. |
| `inRoomSources` | array | Sources physically in the room, with models. |
| `sourcesAvailable` | array | Every source this zone could watch/hear via distribution. |
| `otherSystems` | array | Non-AV gear in the room: lighting, lifts, climate, pool. |
| `flags` | array | Open items for the walk: `"TV size TBD"`, `"Wiring unknown"`, etc. |

## `rack[]`

| Key | Description |
|---|---|
| `component` | Name as programmed (e.g. `"HDMI Matrix"`). |
| `manufacturer`, `model` | e.g. `"AVProConnect"`, `"AC-MX88-AUHD-HDBT"`. |
| `category` | Coarse type: `Source`, `Amplifier`, `Switching`, `Control`, `Lighting`, `Network`, … |
| `deviceClass` | Savant's raw device class string. |

## `connections[]`

| Key | Description |
|---|---|
| `source`, `sink` | Component names (signal flows source → sink). |
| `sourceZone`, `sinkZone` | Which zone each end lives in. |
| `signal` | Format string: `hdmi`, `ethernet`, `audio`, `ir`, `rs-232`, … |

## Example

```json
{
  "generator": "Blueprinted",
  "client": "Adams, Mike (Copo)",
  "system": {
    "hostModel": "Savant Pro Host",
    "hostUid": "F01898EEE36A0000",
    "blueprintVersion": "10.6.1",
    "originallyCreatedWith": "daVinci5.0 (21)",
    "advisories": []
  },
  "counts": { "theaterRooms": 1, "twoChHouseAmp": 8, "tvsTotal": 8 },
  "rooms": [
    {
      "n": 11,
      "name": "Theater",
      "zoneType": "Theater — 5.1 local",
      "tv": true,
      "oldTv": [{ "make": "Samsung", "model": "UN55D7000", "size": 55 }],
      "videoFeed": "matrix",
      "avrs": ["Integra DRX-7.1"],
      "inRoomSources": ["Theater Sat (DIRECTV HR23-700)"],
      "sourcesAvailable": ["Apple TV 1", "DVR 1", "House BluRay"],
      "flags": ["TV size TBD", "Wiring unknown"]
    }
  ],
  "rack": [
    { "component": "HDMI Matrix", "manufacturer": "AVProConnect",
      "model": "AC-MX88-AUHD-HDBT", "category": "Switching" }
  ],
  "connections": [
    { "source": "Apple TV 1", "sourceZone": "Shared Equipment",
      "sink": "HDMI Matrix", "sinkZone": "Shared Equipment", "signal": "hdmi" }
  ]
}
```
