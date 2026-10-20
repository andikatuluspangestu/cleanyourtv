# cleanyourtv (Polytron Glass Edition)

**Your TV, minus the ads, with a frosted-glass home that changes from day to night.**
A fork of [Gathin23/cleanyourtv](https://github.com/Gathin23/cleanyourtv), the lightweight tvOS-style launcher and
debloat kit for Android TV and Google TV, built and tested on a 1 GB Polytron 2K Android TV.

Listed in the upstream [SHOWCASE](https://github.com/Gathin23/cleanyourtv/blob/main/SHOWCASE.md) (#386). Everything
the upstream launcher does still works here; this page covers what the fork adds. For the full manual (doing it by
hand, restoring, troubleshooting, every supported TV) see the
[upstream README](https://github.com/Gathin23/cleanyourtv#readme).

Everything is reversible: debloat lists disable packages, and a restore script puts the stock launcher back.

## Fork features

- **Frosted-glass dock and pills.** With a wallpaper, the dock band and the top pills show a blurred copy of the
  picture behind them, with a sheen along the top and a light rim, like macOS. Android 11 has no live blur, so the
  wallpaper is blurred once, on a 160×90 copy, and drawn through a shader: nothing is recomputed per frame.
- **Day and night wallpapers.** `wallpaper.jpg` by day, `wallpaper_night.jpg` from `night_from` to `night_until`
  (18:00 to 06:00 by default), with a 1.5 s crossfade; the glass follows the picture. Wallpapers decode in full
  colour, so night skies don't band.
- **Icon-only top bar.** Four round glass pills: Inputs, Wi-Fi (its own icon when offline, Ethernet when wired),
  Tools and Settings. The date and stats line get the room back.
- **Tools menu.** *Reorder apps* (move any app into the first row to dock it) and *Recents*: the apps you used in the
  last three days; OK opens one, hold OK closes it, and **Close all** ends every sideloaded app in the background
  and tells you how much memory came back.
- **Wi-Fi panel** instead of the system picker: current network, band, link speed, signal (in words and dBm), IP
  address, a hint when the 5 GHz network is strong while the TV sits on 2.4 GHz, and the strongest networks nearby.
  Android 10+ lets only system apps switch networks, so picking one opens the TV's Wi-Fi settings; hold OK on the
  pill to go there directly.
- **[`glass-night`](themes/glass-night/) theme**: a sunrise coast by day and an Icelandic aurora by night, both from
  Wikimedia Commons, with the glass on.
- **[Polytron 2K debloat list](examples/polytron-2k-hayward/debloat.txt)** for the Hisense/CVTE MediaTek board
  behind it.

## Contributed upstream

| Pull request | What | Status |
|---|---|---|
| [#386](https://github.com/Gathin23/cleanyourtv/pull/386) | This fork in SHOWCASE.md | Merged |
| [#388](https://github.com/Gathin23/cleanyourtv/pull/388) | Polytron 2K Android TV debloat list | Open |
| [#389](https://github.com/Gathin23/cleanyourtv/pull/389) | Optional night picture for themes (`wallpaper_night.jpg`), rebuilt on upstream's new wallpaper rotation | Open |

The glass, the icon pills, Recents and the Wi-Fi panel stay in this fork: they change the default look and need
permissions the reference launcher deliberately avoids (see [CONTRIBUTING](CONTRIBUTING.md)).

## Quick start

1. **On the TV:** Settings → Device Preferences → About → press OK on **Build** seven times → back →
   Developer options → **USB debugging** on. Keep the TV on, on the same Wi-Fi as your computer.
2. **On your computer:** open Claude Code (or another coding assistant with shell access) in a clone of this repo and
   paste [`PROMPT.md`](PROMPT.md), filling in your TV model.
3. Accept "Allow USB debugging?" on the TV, then answer the checklist: which apps you use, which go in the dock.

Or by hand, once the debloat is done:

```bash
TV_HOST=192.168.1.50 launcher/build-and-install.sh      # build, install, make it Home, grant usage access
TV_HOST=192.168.1.50 tools/theme.sh themes/glass-night   # glass + day/night wallpapers, no rebuild
```

On Windows: `launcher\build-and-install.ps1 -TvHost 192.168.1.50` and `tools\theme.ps1 -TvHost 192.168.1.50 themes\glass-night`.
Building needs JDK 17+ and Android SDK platform 34; see the upstream README for setup.

## Theme keys added by this fork

Set them in a theme's `config.json` and push it with `tools/theme.sh`; no rebuild.

| Key | Type | Meaning |
|---|---|---|
| `dock_glass` | bool | Glass dock band and pills over the blurred wallpaper (`true` by default; only with a wallpaper) |
| `night_from`, `night_until` | `HH:MM` | When `wallpaper_night.jpg` from the theme folder replaces `wallpaper.jpg`; 18:00 and 06:00 by default |

Keep wallpapers dark-ish, 1920×1080 and under about 300 KB. Many budget sets draw their UI at 1280×720
(`wm size` tells you); a picture prepared at that size skips one resize on the TV and looks sharper.

## Permissions and memory

Like upstream: **no internet permission**, no background services, nothing phones home. The fork adds two
permissions, both for Recents:

- **Usage access** (`PACKAGE_USAGE_STATS`), to list recently used apps. Android TV has no screen to grant it, so
  `build-and-install` runs `appops set com.gathin.tvhome GET_USAGE_STATS allow`. Without it Recents shows only
  Close all.
- **`KILL_BACKGROUND_PROCESSES`**, a normal permission, for Close all. It ends background processes only; a real
  force-stop needs a system signature.

On the Polytron (about 930 MB of RAM) Home sits around 75 MB with the glass and a wallpaper, and the stats line
shows about 400 MB free after the debloat.

## Supported TVs (lists from upstream)

Anything running **Android TV** or **Google TV**. Samsung (Tizen) and LG (webOS) run different
systems; see [SHOWCASE.md](SHOWCASE.md) for a webOS port. **Fire TV** takes the debloat half only,
since Fire OS will not hand Home to a third-party launcher. A few China-market TCL sets refuse to
install any launcher at all; their lists say so in the header.

Tested lists from contributors, one folder per model in [`examples/`](examples/):

**TCL** 18 · **Sony** 16 · **Xiaomi** 15 · **Philips** 6 · **Hisense** 5 · **BenQ** 2 · **CHiQ** 2 ·
**Haier** 2 · **Hitachi** 2 · **NVIDIA Shield** 2 · **Panasonic** 2 · **Skyworth** 2 · AiPro2K ·
Amazon Fire TV Stick · Chromecast with Google TV · Insignia Fire TV · Kalley · OnePlus · onn · Polytron · PRISM+ · RCA ·
Redmi · SANYO · Thomson · Toshiba Fire TV · Vu · JMGO, Portronics, Stellamore, Wanbo and XGIMI projectors

<details>
<summary><strong>The full list</strong></summary>

| TV | OS |
|---|---|
| AiPro2K (Expressluck, dongnae; Changhong AI PONT family) | Android TV 14 |
| Amazon Fire TV Stick 4K, 1st gen 2018 (AFTMM, mantis) | Fire OS 6.7.1.1 / Android 7.1 |
| BenQ E55-735 (Changhong AI PONT, DVB96BQ) | Google TV 14 |
| BenQ E55-750 (Changhong AI PONT, DVB96BQ) | Google TV (Android 11) |
| CHiQ AI PONT (Changhong GTVU65, dangni, MediaTek mt5867) | Google TV 14 |
| CHiQ AI PONT (DVB93CQ) | Android TV 11 |
| Chromecast with Google TV 4K (sabrina) | Google TV 14 |
| Haier Android TV DVB2K (broadway, HaierATVT56DVB) | Android TV 11 |
| Haier H32S80EFX (MatrixTV CE, HRT56EU, deto, MediaTek), Thailand | Google TV 14 |
| Hisense HG9 (HiSmart 4K A4FFM) | Android TV 9 |
| Hisense HiSmartTV A4 (xiaoyushan, MStar m7322), Android 11 firmware RTT2.221108.001 | Android TV 11 |
| Hisense HiSmartTV A4 (xiaoyushan, MStar m7322), India | Android TV 9 |
| Hisense SmartTV 4K FFM (huangshan, MediaTek) | Android TV 10 |
| Hisense U75 (hengshan, MediaTek), Latin America | Google TV 12 |
| Hitachi 65QLEDSM20 (MTC, bangbae, MediaTek m7322), Australia | Android TV 11 |
| Hitachi CDH-LE32SMART21 (TCL-built C06, C06_2K_GB, MediaTek MT9221, 1 GB), Argentina | Android TV 11 |
| Insignia 55" F30 4K Fire TV (NS-55F301NA25, AFTDCT31, duckie) | Fire OS 7 / Android 9 |
| JMGO N1S projector (K80A, holan, MediaTek m7332), Australia | Google TV 14 |
| Kalley R10G (device R10G, product ACER_FFM, MOKA, Realtek RTD2875P), Latin America | Google TV 14 |
| NVIDIA SHIELD TV 2019 tube (P3430, sif) | Android TV 11 |
| NVIDIA SHIELD TV Pro 2019 (P2897, mdarcy) | Android TV 11 |
| OnePlus TV (D00, Dubai) | Google TV 14 |
| onn 4K Streaming Box (2023, Walmart; YOC, Amlogic), US | Google TV 14 |
| Panasonic 4KTV-JUP (Jupiter_PA, MediaTek) | Android TV 11 |
| Panasonic TH-40HS550K (Skyworth-built, GS655 shibuya, MediaTek) | Android TV 11 |
| Philips 50PUS8517/12 (TP Vision, PH1M_EA_9970A, MediaTek MT5895), Europe | Android TV 11 |
| Philips 55OLED707/12 (TP Vision, PH1M_EA_9970A, MediaTek MT5895), UK | Android TV 11 |
| Philips 55PUS7503/12 (TP Vision TPM171E, PH7M_EU_5596, MediaTek MT5891), Europe | Android TV 8.0 |
| Philips 65PUS8102/12 (TP Vision TPM171E, PH7M_EU_5596, MediaTek MT5891), Europe | Android TV 8.0 |
| Philips R8G Google TV (MOKA, whitefield, Realtek RTD2851F), US | Google TV 14 |
| Philips Smart 4K TV (Skyworth, eleonas, MediaTek), India | Google TV 14 |
| Portronics Beem 470 Smart LED Projector (Allwinner H713, 1 GB), India | Android 11 |
| Polytron 2K (WLTV POLYTRON2K, hayward; Hisense/CVTE board, MediaTek mt5867), Indonesia | Android TV 11 |
| PRISM+ Q65 Ultra 2026 QLED 4K AI Google TV (R10G, PRISMPLUS_FFM, MOKA, Realtek RTD2875 SoC, RTD6748 platform) | Google TV 14 |
| RCA AI PONT SA (Changhong, redwood, ISDB92RC; also sold as realme 40-inch) | Android 14 |
| Redmi TV (MiTV-ANTL0, starwars) | Android 5.1.1 (not Android TV) |
| SANYO ATV R2 (SCBC, Realtek RTD285O) | Android TV 9 |
| Skyworth SWTV-21AE (sunnyvale, MediaTek) | Android TV 14 |
| Skyworth SWTV-24NA-4K NF (eleonas, MediaTek mt5896), US | Google TV 14 |
| Sony BRAVIA 4K AE2 (BRAVIA_AE2, MediaTek MT5897), North America | Google TV 14 |
| Sony BRAVIA 4K GB (BRAVIA_ATV2_EU, BRAVIA_ATV2) | Android TV 9 |
| Sony BRAVIA 4K GB (BRAVIA_ATV2_UC, MediaTek mt5891) | Android TV 9 |
| Sony BRAVIA 4K UR2 (BRAVIA_UR2_4K, MediaTek) | Android TV 10 |
| Sony BRAVIA 4K VH2 (KD-55X80J, BRAVIA_VH2, MediaTek), EU | Google TV 12 |
| Sony BRAVIA 4K VH21 (KD-65X85K, 2022, BRAVIA_VH21, MediaTek MT5895), Australia | Google TV 12 |
| Sony BRAVIA 9 II (K-65XR90M2, BRAVIA_AE3, MediaTek MT5838) | Google TV 14 |
| Sony BRAVIA BF1 (BRAVIA 2 II class, BRAVIA_BF1_4K_A, MediaTek) | Google TV 12 |
| Sony BRAVIA KD-55X8509C (2015, "BRAVIA 4K 2015", SVP-DTV15, MediaTek), EU firmware | Android TV 7.0 |
| Sony BRAVIA KD-55X85J (2021, BRAVIA_VH2, MediaTek MT5895), South America | Google TV 12 |
| Sony BRAVIA KD-65X80J (2021, BRAVIA_VH2, MediaTek), India | Google TV 10 |
| Sony BRAVIA KD-65X9300D (2016, SVP-DTV15, MediaTek), Asia-Pacific | Android TV 8.0 |
| Sony BRAVIA TL2 (OD0M_EA_T32, TPV-built, MediaTek), Europe | Android TV 11 |
| Sony BRAVIA VU31 4K (BRAVIA_VU31_4K, Realtek), India | Google TV 14 |
| Sony BRAVIA XBR-65X900E (2017, BRAVIA_ATV2) | Android TV 9 |
| Sony BRAVIA XBR-65X900F (2018, BRAVIA_ATV3_4K, MediaTek MT5891), US/Canada | Android TV 9 |
| Stellamore T950S projector (Droidlogic be30a3, Amlogic txhd2) | Android 13 |
| TCL 40S5400A (C06, C06_2K_GB, MediaTek MT9221, 1 GB) | Android TV 11 |
| TCL 43C645 (Smart TV Pro, Realtek RTD2851 / R51MT05, V8-R51MT05-LF1V652) | Google TV launcher on Android TV 11 |
| TCL 43EP660 (Europe, BeyondTV, Realtek rtd285o, V8-R851T02-LF1V378) | Android TV 9 |
| TCL 50EP680 (Europe, BeyondTV, Realtek rtd285o) | Android TV 11 |
| TCL 55" 4K Google TV, 2023 (G10, G10_4K_US_NF); also TCL 55P6K, Spain (G10_4K_GB_NF_32BIT) | Google TV 12 |
| TCL 55Q10G Pro (China, tcl_mt5879_cn, MediaTek MT9952) | Android TV 11 |
| TCL 55S470G (G10, G10_4K_US_NF, Realtek), US | Google TV 12 |
| TCL 65C725 (Smart TV Pro, BeyondTV4) | Android TV 11 |
| TCL 65C735 (Smart TV Pro, G05, MediaTek), France | Google TV 11 |
| TCL ak30a5 (China, Amlogic) | Android TV 9 |
| TCL BeyondTV (India) | Android TV 9 |
| TCL BeyondTV, EU/Nordic (Realtek RTD2851, V8-R851T02-LF1V606) | Android TV 9 |
| TCL BeyondTV2 (Latin America / Brazil, Realtek RTD2851) | Android TV 9 |
| TCL C755 QD-Mini LED, 2024, Australia (G08, MediaTek tcl9618) | Google TV 14 |
| TCL QM6K Pro, 2025 (G08, MediaTek tcl9618) | Google TV 14 |
| TCL Smart TV (G07, G07_4K_GB_NF, Realtek RTD2851A, 2 GB) | Google TV 11 |
| TCL Smart TV Pro (G05, G05_4K_US, MediaTek MT5889) | Google TV 11 |
| TCL UnionTV, Latin America (TCL-built like Thomson 32HE5800, Realtek RT2841) | Android TV 9 |
| Thomson 32HE5800 (TCL-built UnionTV, Realtek RT2841), Spain | Android TV 9 |
| Toshiba Fire TV (AFTHA004, hazel) | Fire OS 7 / Android 9 |
| Vu 4K (HVCL, lushan, MediaTek), India | Google TV 12 |
| Wanbo X5 Pro projector (built-in Google TV stick: SkyworthDigital YYT, Amlogic), Australia | Google TV 14 |
| XGIMI Horizon S Pro projector (XK03H, xgimigalileo, MediaTek) | Android TV 11 |
| Xiaomi Mi Box S (MIBOX4, oneday), streaming box | Android TV 9 |
| Xiaomi Mi Box S 2nd gen (MiTV-AFKR0, jaws), streaming box | Google TV 11 |
| Xiaomi Mi TV (MiTV-MOOQ0, croods) | Android TV 10 |
| Xiaomi Mi TV 4A (MiTV-MOOQ1) | Android TV 10 |
| Xiaomi MiTV-AESP0 stick (aquaman, 1 GB) | Android TV 9 |
| Xiaomi MiTV-ASTP1 (maverick), China firmware 2.40.641.0514.cn, 1 GB RAM, 720p | Android TV 9 (CN build) |
| Xiaomi MiTV-AXFR1 (rango, Amlogic, 1 GB), India | Android TV 11 |
| Xiaomi MiTV-AXSO0 (dangal; Mi TV 4C Pro 2019 / 4A Pro) | Android TV 9 |
| Xiaomi MiTV-AXSO2 (dangalUHD, India, 2 GB) | Android TV 9 |
| Xiaomi MiTV-MOOR2 (venom, MediaTek) | Google TV 11 |
| Xiaomi MiTV-MOSR4 (irobot, MediaTek) | Google TV 11 |
| Xiaomi MiTV-MSSP3 (machuca, MediaTek) | Android TV 11 |
| Xiaomi MiTV-MSSP3 (machuca, MediaTek), Android 9 firmware PI.7524 | Android TV 9 |
| Xiaomi TV A Pro 75" 2026 (MiTV-MZTU0, river, MediaTek) | Google TV 14 |
| Xiaomi TV Box S 3rd gen (MiTV-AFMU0, twilight, Amlogic), streaming box | Google TV 14 |

</details>

Your TV not here? The prompt discovers the bloat itself. Add your list afterwards and the next
person with your model skips that step: [CONTRIBUTING.md](CONTRIBUTING.md).

## Tested on

| TV | Android | Notes |
|---|---|---|
| Polytron 2K (WLTV POLYTRON2K, hayward; Hisense/CVTE board, MediaTek mt5867), Indonesia | Android TV 11 (RTMA.250416.094) | 1 GB RAM, 1280×720 UI, decoders up to 1080p; stock `tvlauncher` and `setupwraith` both claim Home and are disabled by the installer; Home checked from inside apps and after a reboot |

## Status

This fork is based on upstream v2.4 (20 Oct 2026). Upstream has since added wallpaper collections, editing the dock
from the TV and more; merging those in is on the list. Until then, for the newest upstream features use upstream
directly and push the [`glass-night`](themes/glass-night/) theme for the day/night wallpapers once #389 lands.

## Credits

Launcher and tools: [Gathin23/cleanyourtv](https://github.com/Gathin23/cleanyourtv) by Gathin and contributors.
Font: [Outfit](https://fonts.google.com/specimen/Outfit) under the OFL ([launcher/FONT-LICENSE-OFL.txt](launcher/FONT-LICENSE-OFL.txt)).
Wallpapers in `themes/glass-night/` are from Wikimedia Commons under CC BY-SA 4.0, see
[its CREDITS](themes/glass-night/CREDITS.md). Fork changes by [andikatuluspangestu](https://github.com/andikatuluspangestu).

MIT, see [LICENSE](LICENSE).
