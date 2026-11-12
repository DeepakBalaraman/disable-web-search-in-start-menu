# Disable Web Search in Start Menu

Keeps the Start menu and the search flyout local. Web results, Bing suggestions
and the "search the web" row stop showing up, and everything else about search
keeps working.

Windows already has policies for this, but they are read when the search UI
starts, so applying them means a reboot, and the registry entries stay behind
afterwards. This mod answers those policy reads from inside the search UI
processes instead: nothing is written to the registry, and turning the mod off
restores the default behavior the next time the search UI starts.

## Settings

| Setting | What it does |
| --- | --- |
| Start menu | Hides web results in the Start menu search. |
| Taskbar search | The same for the search flyout, which is also the window Windows 10 uses for the Start menu search. |

## Which processes it hooks

`StartMenuExperienceHost.exe`, `SearchHost.exe` and `SearchApp.exe`. The mod
reports the same values Windows itself uses:

- `IsMSACloudSearchEnabled` and `IsAADCloudSearchEnabled` in
  `Software\Microsoft\Windows\CurrentVersion\SearchSettings` — these are the
  "Cloud content search" switches in Settings, and the ones the Windows 11
  search UI asks before it shows web results
- `BingSearchEnabled` and `CortanaConsent` in
  `Software\Microsoft\Windows\CurrentVersion\Search`
- `DisableSearchBoxSuggestions` in `Software\Policies\Microsoft\Windows\Explorer`
- `ConnectedSearchUseWeb`, `DisableWebSearch` and `AllowCloudSearch` in
  `Software\Policies\Microsoft\Windows\Windows Search`

Values you set yourself are left alone; the mod only fills in the answers the
search UI asks for, for both the current user and the machine.

Every registry function exists twice in a process: once in `advapi32.dll` for
desktop callers and once in `kernelbase.dll` for the API sets. The search UI is
a packaged app, so it uses the `kernelbase` copy. Both are hooked.

## If web results still show up

Turn on the mod's log in Windhawk and open the search UI once. Every policy read
the mod answered is listed, which shows whether the process asks for these
values at all. If it does not, the build reads them through a path the mod does
not cover, and the log is the place to start.
