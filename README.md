# Stargazer for Wear OS

A live sky map for your wrist. Point your watch at the sky and see the Sun, Moon, planets, bright stars, the International Space Station and Starlink satellites, all rotating with your compass heading.

## Features

- **Live sky map** with the Sun (yellow), Moon (greyish blue), all seven planets, 16 bright stars (tinted by colour), the ISS and recent Starlink satellites
- **Compass-aware**: the map turns as you turn
- **Red mode** for night vision (tap "Red mode" on the sky screen)
- **ISS and Starlink pass predictions**: next visible passes, soonest first, with start time, maximum height, direction and duration
- **ISS alert**: optional notification 5 minutes before the next visible ISS pass
- **Planet pages**: picture plus rise, set and highest-point times for the Sun, Moon and each planet
- **Adjustable screen idle time**: 15 s, 30 s, 1 min, 2 min, 5 min or always on
- **Flip sky 180°** option

## Gestures

| Gesture | What it does |
|---|---|
| Swipe up | Next ISS and Starlink passes |
| Swipe down | Settings |
| Swipe left | Sun, Moon and planet pages (keep swiping for the next one) |
| Swipe right | Back towards the sky screen |
| Tap "Red mode" | Toggle night-vision colours |

Swipe-to-dismiss is switched off so it doesn't clash with the sideways swipes. Press the watch's side button to leave the app.

## Requirements

- Wear OS 3 or later (API 30+) with a compass (rotation vector sensor) and location
- An internet connection (Wi-Fi, phone link or LTE) now and then to download satellite data

## Build the APK

### On GitHub (no computer needed)

1. Put this project in a GitHub repository.
2. Add the workflow file at `.github/workflows/build.yml`.
3. Open the **Actions** tab. The **Build APK** workflow runs on every push, or tap **Run workflow**.
4. When the run turns green, open it and download **Stargazer-debug-apk** from **Artifacts**.

### With Android Studio

1. Open the `StargazerWear` folder.
2. Let Gradle sync.
3. Choose **Build > Build APK(s)**.
4. The APK is at `app/build/outputs/apk/debug/app-debug.apk`.

## Install on a watch

Enable **Developer options** and **ADB debugging** (or **Wireless debugging**) on the watch, then:

```
adb connect <watch-ip>:<port>
adb install app-debug.apk
```

Without a computer, an Android phone app such as Bugjaeger or Wear Installer can send the APK over Wi-Fi.

## How it works

- **Sun, Moon and stars**: standard low-precision astronomy formulas, with simple precession so stars match the Sun and Moon.
- **Planets**: Keplerian orbital elements from the JPL "Approximate Positions of the Planets" tables (valid 1800 to 2050).
- **Satellites**: orbit data (TLEs) is downloaded from [CelesTrak](https://celestrak.org) and cached for 12 hours. Positions come from a simplified Kepler model with J2 drift rather than full SGP4.
- **Visible passes**: counted when the satellite is above 10°, in sunlight, and the Sun is more than 6° below the horizon.
- **Starlink**: tracks the 80 most recently launched satellites and merges passes within 10 minutes into one "train".

## Limitations

- Pass times can be off by up to about a minute, more if the cached data is old.
- Moon position ignores parallax (up to about 1° error).
- Planet pictures are drawn illustrations, not photographs.
- ISS alerts are not rescheduled after a restart until the app is opened once.
- There is no tile or watch-face complication yet.

## Project layout

```
app/src/main/java/com/burtbot/stargazer/
  MainActivity.kt        screens, gestures, settings, idle timer
  Astro.kt               Sun, Moon, planets, stars, rise/set times
  Satellites.kt          TLE download, orbit model, pass finder
  PlanetArt.kt           drawn planet pictures
  AlertScheduler.kt      5-minute ISS alarm
  PassAlertReceiver.kt   alert notification and boot handling
```

## Data and credits

Satellite orbit data courtesy of CelesTrak. Planet elements from NASA JPL (E. M. Standish).
