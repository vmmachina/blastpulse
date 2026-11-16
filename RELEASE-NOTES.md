## What's new in Blast Pulse 1.5.0

Blast Pulse 1.5 adds an optional Microsoft Teams section to the desktop display. It combines automatic Teams connection reports, a separate observation of Teams channel activity and optional historical diagnostic evidence. It also fixes the NVIDIA section briefly appearing on desktops without a detected NVIDIA GPU.

### 1. Automatic Teams connection reports

Blast Pulse can now read the local Teams monitoring file automatically, without repeatedly generating or importing support archives:

```text
%LOCALAPPDATA%\Packages\MSTeams_8wekyb3d8bbwe\LocalCache\Microsoft\MSTeams\tfw\vdi_connection_info.json
```

When enabled, the file is checked every five seconds. The desktop shows the original **Teams report time** and the latest **Checked** time separately.

| Teams report | Desktop display | Interpretation |
| --- | --- | --- |
| `remote` / `3200` | **SlimCore connection reported** | Teams reports a remote SlimCore connection, accepted by the local context checks. |
| `local` / `3200` | **SlimCore fallback reported** | Teams reports a local SlimCore fallback. |
| `local` / `3100` | **WebRTC reported; state unconfirmed** | The reported mode is WebRTC; this record does not confirm whether WebRTC media optimization is active. |
| Teams is stopped | **Teams not running** | No positive automatic connection report is retained. |
| Missing, invalid or unmatched evidence | **Not determined** | There is insufficient matching information; this does not mean Teams is unoptimized. |

These values describe a **connection reported by Teams**, not an independent test of audio/video quality or call functionality. The source time is a connection-event time, not a continuously refreshed heartbeat.

### 2. Process and connection checks

The automatic reader checks the current Windows session, running Teams processes and Windows connection times before and after reading the file.

- A report must be at or after both the current Teams process start and the current Windows connection boundary.
- Teams stopping, a changed process context, a disconnected session or a new connection invalidates the applicable positive state.
- The newest applicable record is selected by its source timestamp, independently of its position in the JSON array.
- Conflicting records, inconsistent timestamps, future-dated data, failed reads and ambiguous context cannot confirm a connection.
- Collected samples expire after 15 seconds without a successful refresh. This expiry applies to the collected sample, not the original connection-event timestamp.
- Replies from an earlier desktop/connection generation are discarded.

The Teams connection report and Horizon channel counters are evaluated separately. A counter-query error does not discard an independently valid Teams report.

### 3. Separate Teams channel activity

A new **Teams channel** row shows whether traffic was observed in the matching Horizon Teams virtual channel. Its source is the local Horizon performance-counter provider, read through WMI.

The observation checks Windows session, Blast worker identity and channel identity. Two compatible samples are needed to compare cumulative byte/packet counters. Worker or channel changes, reconnects, reset counters, stale samples and missing data cause a new baseline or an unconfirmed state.

**Traffic observed** and **No traffic observed** describe activity during the sampled interval. Neither value proves whether media optimization is active, and an idle channel is not automatically an error.

### 4. Optional historical Teams diagnostic import

The tray menu adds **Import Teams diagnosis…** and, when evidence is stored, **Clear imported Teams diagnosis**.

- Supported inputs include Teams diagnostic TXT files, Teams ZIP archives and nested Weblogs archives within the supported size limits.
- Matching initialization and connected events can establish a historical SlimCore confirmation.
- The original event time is retained. A later detected disconnect and older evidence are identified separately.
- The result appears under **Last confirmed (diagnostic)**, explicitly labelled **Imported evidence; not a live status**.
- Imported evidence never overrides the automatic Teams report.

Only selected evidence fields are saved locally in `%LOCALAPPDATA%\BlastPulse\teams-diagnostic.json`; the raw diagnostic archive is not retained by the importer. Reading and archive expansion have size/count limits.

### 5. NVIDIA display stability fix

**Before:** an initial NVIDIA query failure could display an NVIDIA section even though no GPU had been detected. A later “no driver” result hid it again, making the section appear and disappear on desktops without NVIDIA hardware.

**Now:** the normal desktop display keeps the NVIDIA section hidden until a positive GPU count has actually been read. An initial error, timeout, missing driver or zero-GPU result cannot make the section appear.

Once a GPU has been detected, a later query failure, missing driver or zero-GPU result keeps a visible **unknown** state. A previous positive license state is not carried forward. A successful subsequent query restores the current driver-reported information.

### 6. Reliability, presentation and documentation

- Teams collection runs in a separate background process with a six-second timeout, bounded output and bounded input parsing. Queries do not overlap or perform provider/file work on the display thread.
- English and German Teams labels are integrated into the existing desktop layout and DPI fitting.
- The README in both languages explains activation, sources, status meanings, update steps and interpretation limits.
- A new screenshot shows automatic SlimCore reporting alongside channel activity and a separate older imported diagnostic.
- Existing Horizon transport/quality reads, Blast datagram values and NVIDIA NVML data sources are retained. The existing datagram JSON format and wallpaper output paths retain their previous scope.

### Enable the Teams section

The feature is **opt-in**. Merge this property into `config.json` beside `BlastPulse.exe`:

```json
{
  "ShowTeamsChannel": true
}
```

Keep your other configuration properties. The built-in and supplied default is `false`; when disabled, the Teams section is hidden and Teams polling is not started.

Restart Blast Pulse and use its normal desktop mode, without `-preview` or `-apply`. The Teams section is not included in wallpaper preview/apply output or the existing datagram JSON export.

Teams must be running in the target Windows session and provide a readable monitoring file. Blast Pulse does not install Teams, the Horizon endpoint plug-in or SlimCore, and does not configure Teams optimization policies.

### Updating from 1.4

1. Exit the running Blast Pulse instance using its tray menu.
2. Extract the new ZIP or replace the EXE in your chosen application folder. Preserve your existing `config.json` to keep your layout and settings.
3. Add `"ShowTeamsChannel": true` if you want the new Teams section, then start `BlastPulse.exe` inside the Horizon session.

The Windows x64 / .NET Framework 4.8 requirements remain. The executable is unsigned. The download includes the EXE, example configuration, English/German README files, screenshots, release notes, build information and checksums. Standalone EXE, configuration and checksums are also attached to the release.

### Validation

| Area | Evidence and scope |
| --- | --- |
| Final 1.5 Windows executable | Built on Windows; the full automated regression suite passed, including existing Horizon, datagram, NVIDIA, desktop and output-mode checks. |
| Automatic Teams reports | Controlled tests cover source selection, original times, stopped processes, connection changes, conflicts, ambiguity, read failures and independent channel/report failures. |
| Real Horizon VDI | User-assisted checks of the preceding functionally equivalent Teams test build confirmed SlimCore display, a stopped state, Teams reopening with a fresh report and a fresh report after reconnecting the same Windows session. |
| NVIDIA fix | A state-to-renderer regression reproduced the no-GPU flash before the fix and passed afterwards. It also checks visible unknown states after GPU detection and recovery after a successful query. This correction was not re-tested on real NVIDIA hardware. |
| Presentation and package | English/German and DPI/layout regressions passed. ZIP contents and uploaded release assets are checked with SHA-256. |

### Known limits

- Several Windows sessions of the same account are outside the supported attribution scope. The user-local Teams records contain no validated Windows session/process identity; temporal checks cannot supply one. Observed Teams processes in another Windows session suppress positive attribution conservatively.
- WebRTC and SlimCore fallback have controlled test coverage, but were not validated in the real VDI test described above.
- The short interval between Horizon reconnect and the new Teams report was not captured live. Controlled tests cover invalidation, but the screenshots do not prove that intermediate display transition.
- Complete Teams process-tree termination was not independently enumerated during the user-assisted stop/reopen sequence.
- A successful connection report does not establish call health, microphone/camera functionality or media quality. Historical diagnostic evidence is not a current status.
- Monitoring-file availability and format depend on the installed Teams version. The existing direct Horizon transport/quality query still relies on the tested Agent's internal interface; support for other Agent builds depends on the available fallback evidence.

Preparing or running Blast Pulse does not change Teams policies, GPOs, network settings or the installed optimization components. This release preparation does not update your SYSVOL/GPO deployment.
