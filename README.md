# Sailboat Weather HDA: test pack

A sailboat that sails on real weather. It downloads hourly wind, gusts and wave height for a port or place from Open-Meteo, keys them onto the timeline, and sails the boat accordingly. When the course points into the wind it tacks, and the hull rolls, pitches and bobs with the wind and waves.

- Asset: `zoran::sailboat_weather::1.0` (Object level, "Sailboat Weather")
- Built in: Houdini 22.0.464, non-commercial (Apprentice / Education)

## Contents

```
sailboat_weather/
  sailboat_weather_demo.hipnc            demo scene, weather already fetched (Lisbon, 2026-10-09)
  otls/zoran.sailboat_weather.1.0.hdanc  the asset
  cache/                                 cached weather for the demo (lets the asset re-map without internet)
  README.md
```

## Install

1. Keep the folder together and open `sailboat_weather_demo.hipnc` straight from it.
2. To use the asset in other scenes too, copy `otls/zoran.sailboat_weather.1.0.hdanc` to your Houdini user folder:
   - Windows: `Documents/houdini22.0/otls/`
   - Linux: `~/houdini22.0/otls/`
   - macOS: `~/Library/Preferences/houdini/22.0/otls/`

   Create the `otls` folder if it doesn't exist, then restart Houdini. Alternatively, use Assets > Install Asset Library and pick the file.
3. If the demo opens with a missing-asset warning, do step 2 and reopen the scene.

Licence note: `.hdanc` / `.hipnc` are non-commercial files. They open in Houdini Apprentice and Education.

## Quick test (5 minutes)

Select `/obj/sailboat_weather2` and look at its parameters.

1. **Play.** Press play. The boat should sail, tack and roll. Look through `follow_cam` (inside the asset) for the intended view, 50 m from the boat.
2. **On-screen strip and sky.** Through `follow_cam`, one centred row runs along the bottom of the frame: a navy compass dial with N/E/S/W, then the time of day (yellow), the place (green), and "WIND x kn from NE     BOAT y kn". On the dial, the yellow needle points where the wind blows to and the shorter orange needle shows the boat's heading. Scrub the timeline: the text and both needles should update. The sky is a large sphere whose solid colour follows the fetched data: night, sunrise and sunset, clear, overcast and rain. The far sea fades into the same colour, so the horizon stays soft at any time of day.
3. **Readout tab.** Scrub the timeline. "Weather Now" should update with the hour of day, wind (m/s, knots, Beaufort, the direction it blows from), gusts, waves, boat speed and heading, and state (ON COURSE / TACKING / HOLD).
4. **Port Preset (Location tab).** Pick a port, e.g. Singapore. Place Name should change to that port and "Use Lat/Lon Directly" should switch off.
5. **Fetch Weather.** Needs internet. The demo's Date is set to 2026-10-09 so that step 8 works offline from the shipped cache; clear Date to get today's weather instead. Status should read `OK (forecast) ...` (or `OK (archive) ...` for dates more than 5 days old) followed by `| 25 samples ...`, and the Readout should show the new place. The data is cached in `cache/` next to the scene.
6. **Course tab.** Press the compass buttons to set the course. A course into the wind makes the boat tack (Â±45Â° no-go zone).
7. **HOLD keyed on the timeline.** Set a key on Course Direction (Alt+click), move a few frames on, press HOLD, move on again, press N. HOLD should only apply between those two frames. Without any course keys, HOLD and the compass buttons apply to the whole shot.
8. **Time tab.** Change Frames per Hour, e.g. 5. The weather re-maps from the cache without going online.

## How the weather works

- The asset only goes online when you press **Fetch Weather** or **Geocode**. Nothing is downloaded while playing or cooking.
- One fetch makes one place lookup (OpenStreetMap Nominatim, skipped if the name is already cached), one weather request and one marine (wave) request to Open-Meteo. The weather request returns wind, gusts, cloud cover, precipitation and daylight.
- For dates more than 5 days in the past, daylight is assumed to be 07:00â€“19:00.
- The data is hourly. Each hour becomes one keyframe, **Frames per Hour** apart (default 10), starting at **Start Hour**, with linear blending in between. Over frames 1â€“240 that gives 25 keys, i.e. 24 hours of weather in 10 seconds of animation.
- **Date:** leave it empty for today. Forecasts go up to about 15 days ahead. Dates more than 5 days in the past come from the historical archive.
- **Offline:** if a fetch fails, the asset uses the cached data for that place and date, and Status says so.
- Ports are looked up by name, which gives the city centre. Wave data comes from the nearest sea grid cell.

## Known issues (please report anything else)

- Tacks are instant heading jumps, with no smooth turn.
- The jib is static; only the mainsail and boom react to the wind.
- The hull bobs analytically and doesn't follow the actual wave surface.
- The boom's spring motion is computed for frames 1â€“240 only.
- Wind direction is treated as "blowing from" (meteorological convention). If the boat looks mirrored relative to the wind, tell me.
- A harmless merge warning (N, height attributes) shows inside `boat_geo`.

## What to send back

Your Houdini version and licence, which test steps worked or failed, the Status text if a fetch failed, and a screenshot of anything that looks wrong.

## Credits

- Weather data: [Open-Meteo.com](https://open-meteo.com) (CC BY 4.0)
- Geocoding: Â© OpenStreetMap contributors (ODbL), via Nominatim
- Asset: Zoran Arizanovic
