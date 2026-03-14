# SysMon Overlay

A lightweight, always-on-top system monitor for Windows 11.  
Combines the best of GPU-Z, CPU-Z, Task Manager, and macOS Activity Monitor — but minimal and modern.

```
◈ SYSMON                              ● LIVE
──────────────────────────────────────────────
CPU   64%                              52°C
████████████░░░░░░░░░░░░░░░░░░░░░░░░

CORES
[▓][▓][░][▓][░][▓][▓][░][░][░][░][░]
 0   1   2   3   4   5   6   7   8 ...

──────────────────────────────────────────────
RAM   72%                          11.4/16 GB
████████████████████░░░░░░░░░░░░░░

──────────────────────────────────────────────
GPU   38%                              67°C
███████░░░░░░░░░░░░░░░░░░░░░░░░░░░░

VRAM  52%                          4.2/8 GB
█████████░░░░░░░░░░░░░░░░░░░░░░░░░
```

---

## Quick Start

### 1. Install Python 3.9+
Download from https://python.org  
✔ Check "Add Python to PATH" during install.

### 2. Install dependencies
Open a terminal in this folder and run:
```
pip install -r requirements.txt
```

### 3. Run
Double-click `run_sysmon.bat`  
— or —  
```
pythonw sysmon.py
```
(`pythonw` = no console window; use `python sysmon.py` if you want the console for debugging)

---

## Controls

| Action | How |
|--------|-----|
| Move overlay | Left-click drag anywhere |
| Toggle metrics | Right-click → check/uncheck |
| Toggle average mode | Right-click → Average Mode |
| Settings | Right-click → Settings… |
| Quit | Right-click → Quit, or click ✕ |

---

## Settings (right-click → Settings…)

| Setting | Default | Description |
|---------|---------|-------------|
| Refresh Rate | 1000 ms | How often data updates. Min 100ms. |
| Average Window | 5 sec | How many seconds to average over in Average Mode. |
| Opacity | 0.92 | Window transparency (0.1 = nearly invisible, 1.0 = solid). |

Settings are saved automatically to `sysmon_settings.json` next to the script.

---

## CPU Temperature

By default, `psutil` can read CPU temps on Linux but **not reliably on Windows**.  
To get CPU temperature on Windows:

**Option A — OpenHardwareMonitor (free, open source)**
1. Download OpenHardwareMonitor from https://openhardwaremonitor.org
2. Run it as Administrator (required for hardware access)
3. In OHM: Options → Remote Web Server → Start (this also enables WMI)
4. Install the WMI bridge: `pip install wmi`
5. Restart SysMon — CPU temp will now appear.

**Option B — HWiNFO64**
1. Install HWiNFO64, run in Sensors-only mode
2. Enable "Support OpenHardwareMonitor WMI" in settings
3. `pip install wmi`
4. Restart SysMon.

Without either, CPU temp displays `--`.

---

## GPU Support

- **NVIDIA**: works automatically via `pynvml` (reads NVML directly — no extra software).
- **AMD / Intel**: not currently supported. GPU section will be hidden automatically.

---

## Metric Colors

| Color | Meaning |
|-------|---------|
| 🔵 Cyan | Normal (< 60%) |
| 🟡 Amber | Elevated (60–80%) |
| 🔴 Red | High (> 80%) |

Temperature uses the same thresholds: < 65°C / 65–80°C / > 80°C.

---

## Average Mode

When enabled (right-click → Average Mode), all displayed values are a rolling average
over the configured **Average Window** (default: 5 seconds).  
Useful for smoothing out spikes and seeing sustained load.  
The badge in the top-right corner switches from `● LIVE` to `⌀ 5s`.

---

## Performance

SysMon uses a single background daemon thread for data collection and tkinter's
built-in `after()` for UI updates — no polling loops on the main thread.
At the default 1000ms refresh rate, CPU overhead is negligible (< 0.1%).
Increase refresh rate (e.g. 500ms) for more responsive updates at slightly higher cost.

---

## Files

```
sysmon/
├── sysmon.py           ← main script
├── requirements.txt    ← pip dependencies
├── run_sysmon.bat      ← Windows launcher (no console window)
├── README.md           ← this file
└── sysmon_settings.json← auto-created on first run, stores your preferences
```
