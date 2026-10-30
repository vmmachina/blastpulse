<div align="center">
<p><img src="assets/banner-en.png" alt="Blast Pulse — Omnissa Horizon" width="1200"></p>
<p>by <strong>Stefan Gourguis</strong> · Omnissa Tech Insider · solvig. GmbH · solvig.ch</p>
<p><kbd>Version 1.3.0</kbd> &nbsp; <kbd>Windows x64</kbd> &nbsp; <kbd>.NET Framework 4.8</kbd></p>
<p><a href="https://github.com/vmmachina/blastpulse/blob/main/README.md"><kbd>English</kbd></a> | <a href="https://github.com/vmmachina/blastpulse/blob/main/README.de.md"><kbd>Deutsch</kbd></a></p>
<p><a href="https://github.com/vmmachina/blastpulse/releases/download/v1.3.0/BlastPulse.exe"><img src="assets/download-exe-en.png" alt="Download EXE" width="280"></a> &nbsp; <a href="https://github.com/vmmachina/blastpulse/releases/download/v1.3.0/BlastPulse-1.3.0-win-x64.zip"><img src="assets/download-zip-en.png" alt="Download Windows ZIP" width="280"></a></p>
</div>

The idea came from a simple inconvenience: checking a connection meant repeatedly opening the original Horizon Blast tray icon at the bottom right of the screen. Blast Pulse keeps the relevant information visible on the desktop, so you can follow your session while you work.

The compact display shows connection details alongside Blast FPS, round-trip time (RTT) and uplink packet loss. Live counters refresh every second by default. It appears as a transparent layer at the bottom right of the primary desktop, beneath ordinary application windows. Your wallpaper remains the background; it is not rewritten for each update.

## Latest release — 1.3.0

- Adds the negotiated Blast datagram maximum and safe TX/RX sizes, each with its own last log time.
- Fixes worker verification when Windows denies the direct process start-time query; a local WMI fallback supplies the identity without elevation.
- Corrects connection tracking when disconnect notifications arrive late or more than once.

[Downloads and full release notes](https://github.com/vmmachina/blastpulse/releases/latest)

## In action

<p align="center"><img src="assets/screenshot-nvidia.png" alt="Blast Pulse 1.2 showing NVIDIA A2-16Q, driver 581.42, vGPU Guest mode and an Unlicensed badge" width="900"></p>

NVIDIA GPU and licensing information, shown at 192 DPI. The license state comes from the guest driver.

<p align="center"><img src="assets/screenshot-desktop.png" alt="Blast Pulse on the desktop beside a video of the Omnissa ONE keynote" width="1100"></p>

Horizon session during video playback. Metrics and log timestamps vary by session.

## Download and start

Download [Blast Pulse 1.3.0 for Windows x64 (ZIP)](https://github.com/vmmachina/blastpulse/releases/download/v1.3.0/BlastPulse-1.3.0-win-x64.zip). The package contains the application, an example configuration and English/German documentation. For offline reading, extract the ZIP and open `README.html` in your browser; use the English/Deutsch links at the top to change language.

The [release page](https://github.com/vmmachina/blastpulse/releases/tag/v1.3.0) also offers the [standalone EXE](https://github.com/vmmachina/blastpulse/releases/download/v1.3.0/BlastPulse.exe), [config.json](https://github.com/vmmachina/blastpulse/releases/download/v1.3.0/config.json) and [SHA-256 checksums](https://github.com/vmmachina/blastpulse/releases/download/v1.3.0/SHA256SUMS.txt).

You need Windows 10/11 x64, .NET Framework 4.8 and an active Horizon Blast session with readable session counters and logs. The application is unsigned.

1. Extract the ZIP into a local folder or an accessible network share.
2. Run `BlastPulse.exe` inside your Horizon Blast session.
3. The desktop display appears and updates automatically.

Right-click the Blast Pulse icon in the notification area next to the clock to change **Language → English / Deutsch**, **Hide/Show desktop display**, or **Exit Blast Pulse**. English is the default. A language change takes effect immediately and is remembered for your Windows user.

## Adjust the display

Place `config.json` beside `BlastPulse.exe` and restart Blast Pulse after editing it. Without this file, the defaults below apply.

| Setting | Default | Purpose and allowed values |
| --- | --- | --- |
| `RefreshSeconds` | `1` | Live counter refresh interval, 1–3600 seconds |
| `LogRefreshSeconds` | `5` | Log and display-information refresh interval, 1–3600 seconds |
| `PanelWidth` | `640` | Preferred display width at 96 DPI, 460–900 logical pixels |
| `Margin` | `30` | Distance from the right edge at 96 DPI, 12–150 logical pixels |
| `BottomOffset` | `150` | Space below the display at 96 DPI, 0–1000 logical pixels |
| `FontSize` | `11` | Preferred text size, 10–22 points |
| `FontFamily` | `"Segoe UI"` | Installed Windows font; Windows substitutes a font if unavailable |
| `Language` | `"en"` | Initial language: `"en"` or `"de"` |

Panel width, margins and text scale together with the current Windows DPI. The pixel settings above refer to 100% scaling (96 DPI). Resolution and DPI changes are picked up while the application is running; editing `config.json` still requires a restart. Long labels, values and footer lines wrap, and the display height is measured from their actual text. If the desktop is too small, the text size is reduced only as far as needed.

The default bottom offset leaves room for existing BGInfo text. For a narrower display directly above the taskbar, use this complete `config.json`; omitted settings retain their defaults:

```json
{
  "PanelWidth": 460,
  "Margin": 20,
  "BottomOffset": 0,
  "FontSize": 11
}
```

The language selected in the tray is saved in `%LOCALAPPDATA%\BlastPulse\preferences.json` and takes priority over `Language` in the shared configuration. To return to the configured language, exit Blast Pulse, remove that preference file and start it again. Users only need read access to `config.json`, including when it is on a network share.

## Start from a share or GPO

Keep `BlastPulse.exe` and `config.json` together on the share. Users need read/execute access. To start it at sign-in, add a command like this to a **user logon script**:

```bat
start "" "\\example.com\SYSVOL\example.com\scripts\BlastPulse\BlastPulse.exe"
```

Replace the example path with your own UNC or local path. Blast Pulse keeps running in the user's session, so the logon script should not wait for it to exit. The application does not set up autostart itself. Existing BGInfo can run alongside it; adjust `BottomOffset` to leave enough space.

No fixed delay is needed in the logon script. If Explorer is still starting, Blast Pulse checks again without blocking the UI, for up to two minutes per initialization. After that, timed retries stop; new shell/session activity or choosing **Show desktop display** can trigger detection again. Failures and recovery are recorded in `%LOCALAPPDATA%\BlastPulse\desktop.log`.

For an update, exit Blast Pulse, replace the EXE and start it again. Keep your existing `config.json` to preserve your layout. Test share access and your organisation's application policies before wider deployment.

## Data sources

Blast Pulse reads data locally on the Windows desktop. It does not query a Horizon Connection Server API or call the tray tool's internal `wavedll.dll` interface. No credentials are required by Blast Pulse, and it does not upload the collected values to an external service.

| Desktop information | Source |
| --- | --- |
| RTT and uplink packet loss | `Win32_PerfFormattedData_Counters_HorizonBlastSessionCounters`: `RTT`, `PacketLossUplink` |
| Blast FPS | `Win32_PerfFormattedData_Counters_HorizonBlastImagingCounters`: `Framespersecond` |
| Codec name and screen capture method | Latest matching `Created region encoder` and `Screen capture name` entries in the session's Blast-Worker log |
| Horizon quality state, score and transport backend | Latest `BlastSocket_GetVvcQualityIndicators` entry: `qualityState`, `qualityScore`, `Backend` and the entry's timestamp |
| Display driver, resolution and refresh rate | `Win32_VideoController`, filtered to devices whose name contains `Horizon` |
| Primary screen position and DPI | Windows screen bounds and desktop graphics DPI |

Both performance-counter classes are read through WMI in `root\cimv2`. Blast Pulse selects the counter instance whose `Name` starts with `Session ID: <current session ID>;`.

The compact desktop view prioritizes the six original connection details: codec, capture method, display device, screen configuration, transport and Horizon quality. Blast FPS, RTT and uplink packet loss appear beneath them. **Blast FPS describes the desktop transmission, not the source video's playback frame rate.**

The log file is:

```text
%ProgramData%\Omnissa\Horizon\Logs\Blast-Worker-SessionId<session ID>.log
```

For codec, capture and quality, only the last 4 MiB are read. Those log values represent the latest matching entry in that section and may be older than the current counter readings. The codec name comes directly from the log; no numeric encoder-to-codec mapping is assumed.

**The original Horizon quality status is not guaranteed to update continuously.** It depends on a matching Agent log entry; Blast Pulse does not request an update from the original tray tool. Blast Pulse shows that entry's own timestamp and marks a status older than 90 seconds as an older log state. It does not present a custom rating as Horizon's original status.

If the current log section contains no matching quality entry, transport and quality show **Not reported**. The footer says **Quality status: No matching log entry**. This refers specifically to the quality entry; codec/capture log data and live counters can still be available. A recognized entry displays its own report time; an unrecognized timestamp is identified separately.

The displayed connection transport is the backend recorded in the log. It is not inferred from momentary traffic activity.

### Blast datagram sizes

Version 1.3.0 reads three additional values from the active session's Blast-Worker log:

| Display | Log source |
| --- | --- |
| Blast datagram maximum | `BlastSocketBuildUDPResponse` — negotiated maximum |
| Safe datagram TX | `BlastSocketUpdateSafeDatagramSendSize` — Agent → client |
| Safe datagram RX | `BlastSocketUpdateSafeDatagramReceiveSize` — client → Agent |

The log is checked at `LogRefreshSeconds`, five seconds by default. Each value keeps its own **last log time**. A new matching entry updates the corresponding value; an unchanged file does not become a new measurement. These are reported Blast datagram sizes, **not a measurement of the IP or path MTU**.

Before accepting a value, Blast Pulse verifies the current `VMBlastW` worker's session, PID and start time against the log and identifies its connection interval. If direct access to the start time is denied, it reads `ProcessId`, `SessionId` and `CreationDate` through the local `Win32_Process` WMI provider. No elevation or process-permission changes are used. Missing or ambiguous context shows **Not reported**, with a reason in the footer.

The incremental reader handles complete appended UTF-8/UTF-16 lines and checks for file replacement, truncation and history gaps. Each poll reads at most 4 MiB plus a small header. A new connection or worker requires verified context; rotated archives are not combined. Size records have no socket identifier, so they are associated with the worker/connection interval rather than individual simultaneous raw channels. Available updates depend on what the installed Agent logs.

For a one-time JSON export, use `BlastPulse.exe -datagram-json "C:\Path\datagram.json"` within the target user's Horizon session. It writes a snapshot and exits. The export includes computer/session identifiers, worker and connection context, state and per-value source timestamps; consumers should check those fields. An unavailable size is `null`. `DiscoveryMtuBytes` remains `null`; logged search defaults are not proof that discovery completed. Blast Pulse sends no MTU probes and changes no network settings.

### NVIDIA data

The NVIDIA section reads the installed driver's **NVML** library locally. It does not launch `nvidia-smi`, query the DLS server or change licensing settings. No NVIDIA library is bundled with Blast Pulse.

| NVIDIA information | NVML source |
| --- | --- |
| GPU count | `nvmlDeviceGetCount_v2` — devices visible inside the Windows guest |
| GPU name | `nvmlDeviceGetName` |
| Virtualization mode | `nvmlDeviceGetVirtualizationMode` |
| Driver version | `nvmlSystemGetDriverVersion` |
| Licensed product and license status | `nvmlDeviceGetGridLicensableFeatures_v4` — product name, enabled flag and license state |

A supported, enabled feature is shown as **Licensed** or **Unlicensed**, as reported by the driver. Disabled features and unsupported licensing are identified separately. An unavailable query or a sample older than 90 seconds produces **Unknown**, rather than a guessed license state. The query runs in a separate process with a 10-second timeout so a stalled driver call does not hold up Blast sampling.

A **Licensed** guest does not by itself prove a current connection to NVIDIA DLS; it describes the guest driver's reported license state. Blast Pulse does not read license-token files or call UUID, serial-number or license-server administration APIs. Tokens, GPU UUIDs, serial numbers and server addresses are not included in the display.

## Compatibility

Blast Pulse 1.3.0 for Windows x64.

The Windows build and automated regression suite passed, including worker-identity fallback, datagram updates, file rotation/truncation, reconnect boundaries and the existing desktop, DPI, language and NVIDIA checks. User testing confirmed all three datagram values at 1372 bytes in a live Horizon session. A real mid-session size change and the corrected binary’s reconnect behavior have not yet been manually confirmed. Broader GPO, Explorer-restart and multi-user coverage remains limited.

The NVIDIA display was checked in a Horizon vGPU session with an NVIDIA A2-16Q, driver 581.42 and 192 DPI. All six NVIDIA values were visible; the driver reported **Unlicensed**. Other license states and multi-GPU layouts have not yet been checked on real hardware. Automated Windows regression checks passed for the 1.2 source revision, covering license-state handling, query timeouts, English/German text and DPI layouts.

The 1.1 baseline passed automated Windows rendering checks at 100%, 125%, 150%, 200% and 300% scaling, including large fonts and narrow panels. User testing in Horizon confirmed readable layouts at 3840 × 2160 with 192 DPI (200%) and 1920 × 1080 with 168 DPI (175%).

- The display uses the primary screen. Multiple monitors, Explorer restarts and session reconnects have not been comprehensively tested.
- Available information depends on the Horizon Agent version, log format and the user's access to the counters and logs.
- Test central deployment in your own environment before rollout.

## References

- [Omnissa: Blast performance counters](https://docs.omnissa.com/de-DE/Horizon-Remote-Desktop-Features-V2606/ExaminingBlastSessionStatisticswithWindowsPerformanceMonitor)
- [Omnissa: Blast logs and packet loss](https://kb.omnissa.com/s/article/89455)

- [NVIDIA: NVML vGPU queries](https://docs.nvidia.com/deploy/nvml-api/latest/api/group__nvmlVirtualGpuQueries.html)
- [NVIDIA: vGPU licensing troubleshooting](https://docs.nvidia.com/vgpu/latest/grid-licensing-user-guide/troubleshooting-grid-licensing.html)
