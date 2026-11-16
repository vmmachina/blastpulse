## What's new in Blast Pulse 1.5.0

- **Automatic Teams connection reports:** the optional Microsoft Teams section now reads Teams' local monitoring file every five seconds. It shows “SlimCore connection reported” with the original report time and the latest check time, without repeatedly importing support archives.
- **Connection and process checks:** reports are checked against the running Teams processes and Windows connection context. Stopping Teams, reconnecting, ambiguous context and unreadable or conflicting data invalidate the positive report. The newest applicable record is selected independently of array order.
- **Separate Teams channel activity:** session/worker-bound Horizon counters show whether channel traffic was observed. Traffic alone is never treated as proof of media optimization; a counter failure does not discard an independently valid Teams report.
- **Optional diagnostic import:** supported Teams TXT/ZIP diagnostics, including nested Weblogs, can supply a separately labelled historical SlimCore confirmation. Original event times and later detected disconnects are retained; an imported confirmation never becomes the current automatic status. Imports can be cleared from the tray menu.
- **NVIDIA display stability:** desktops without a detected NVIDIA GPU no longer briefly show an NVIDIA section when an initial query fails. After a GPU has been detected, a failed query keeps the section visible with an unknown license status rather than retaining a previous positive license state.
- **Reliability and presentation:** bounded background queries, additional failure/context/rendering tests, and English/German Teams text integrated into the existing DPI-aware desktop layout.

### Enable the Teams section

Add `"ShowTeamsChannel": true` to `config.json` beside `BlastPulse.exe`, then restart Blast Pulse. The option defaults to `false`. Start the normal desktop display without arguments; the Teams section is not included in wallpaper preview/apply modes or the existing datagram JSON export.

### Interpretation and validation

The automatic value is a **Teams-reported connection**, not an independent audio/video health check. A source connection time is an event time, not a heartbeat. Missing evidence is unknown, not proof that Teams is unoptimized. WebRTC and SlimCore fallback have distinct report labels; WebRTC is not automatically labelled unoptimized.

User-assisted checks in a real Horizon VDI covered SlimCore display, the stopped state, Teams reopening with a new report, and a fresh report after reconnecting the same Windows session. The short gap between reconnect and the new report was not captured live. WebRTC/fallback and multi-user operation were not verified in that VDI; several sessions of the same Windows account are outside the supported attribution scope because the Teams file provides no validated Windows session/process identity. The final versioned package is separately checked by the Windows regression suite.

Existing Horizon transport/quality, datagram and NVIDIA data sources remain in place. Blast Pulse does not install the Teams optimization components or change Teams policies, GPOs or network settings.
