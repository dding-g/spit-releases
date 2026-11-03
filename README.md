# spit

[English](README.md) · [한국어](docs/README.ko.md) · [日本語](docs/README.ja.md) · [简体中文](docs/README.zh-CN.md) · [Español](docs/README.es.md) · [Français](docs/README.fr.md) · [Deutsch](docs/README.de.md) · [Português](docs/README.pt.md)

A small Windows/macOS tray app for quick, single-line notes saved to local Markdown. The minimal composer stays preloaded; file writes happen in the background.

The source repository is private. The [public distribution repository](https://github.com/dding-g/spit-releases) hosts only official installers, updates, and user guides.

## Install and use

Download a published archive for your OS and CPU from [Releases](https://github.com/dding-g/spit-releases/releases) and extract it. [Website](https://spit-ashy.vercel.app). On macOS 12 or later, copy `spit.app` to Applications and open it. On Windows, keep `spit.exe` in a folder of your choice and run it; Microsoft WebView2 Runtime is required. There is no installer or automatic login startup. macOS bundles are ad-hoc signed, **not Developer ID signed or notarized**; Windows executables have no Authenticode signature. Gatekeeper or SmartScreen may warn or block them. Only open a download whose source you trust.

Choose `macos-arm64` for Apple silicon or `macos-amd64` for Intel; on Windows choose `windows-amd64` for x64 or `windows-arm64` for ARM64. **Windows runtime behavior, including update/restart, has not been verified on Windows**; cross-building is not runtime verification.

On first launch, spit discovers registered Obsidian vaults without scanning your disk recursively. It uses the most recently used vault when available; otherwise, choose a folder in Settings. Any existing folder can be selected; Obsidian is not required.

- `Cmd + \` (macOS) / `Ctrl + \` (Windows): show or hide the quick composer.
- `Enter`: hide immediately and submit for background saving. Enter during IME composition does not submit.
- `Esc`: hide and keep the unsubmitted draft **only for this app session**; ordinary quit/relaunch does not restore it. An explicit update restart is the exception described below.
- Multiline paste becomes one line. Empty notes are ignored; the limit is 10,000 UTF-16 code units.
- Left-click the tray/menu-bar icon to toggle the **read-only history panel**, initially showing today. Select any saved date, move between stored dates, or refresh. It does not edit, delete, or search notes, and is not a persistent desktop/OS widget.
- The tray context menu also opens history, New note, and Folder & language settings. Use New note if the global shortcut cannot be registered; use Quit to exit. Closing a window does not quit the app.

Settings offers **automatic OS/browser language detection** or a persisted manual choice: English, Korean, Japanese, Simplified Chinese, Spanish, French, German, or Portuguese. Manual selection overrides automatic detection; unsupported languages and missing translations fall back to English. Changing language keeps the current draft.

## Updates and privacy

spit checks GitHub Releases and automatically downloads available updates in the background. Settings shows status, errors, a manual check, and a restart action when ready. **Installation/restart only happens when you click Restart**; it never interrupts capture automatically. Restart waits for in-flight saves and durable queue persistence, refuses to proceed during IME composition, and preserves the current unsubmitted draft for restoration after the update. This update-only snapshot does not turn ordinary session drafts into persistent drafts.

Before installation, the updater verifies the archive's SHA-256 checksum and Ed25519 signature against the public key pinned in the app; failed verification blocks installation. Update signing is separate from OS code signing and does not provide Apple notarization or Windows publisher trust. Update checks/downloads access GitHub and its download hosts; note text and vault files are not uploaded. Vault syncing follows your other software's settings.

## Files and recovery

Selecting `chosenRoot` writes to `chosenRoot/admo/YYYY-MM-DD.md`. Selecting a folder already named `admo` uses that folder directly, never `admo/admo`. Existing daily notes in the selected root are not moved. Each note uses its submission date and time in local time, even if saving is delayed:

```markdown
- 09:12:04 Buy milk <!-- admo:UUID -->
```

The hidden HTML comment identifies a note for duplicate prevention during recovery; it remains visible in source view. Text is escaped as plain text rather than interpreted as Markdown/HTML. Existing file content is preserved. History reads dated note files in this effective `admo` folder, not the whole vault, including supported older note markers.

Submitted notes are first preserved in a local `state.json` queue, then appended and synced to Markdown before removal from the queue. Failed writes remain queued: errors appear in Settings and the tray, retries run every **5 seconds**, and restarting resumes recovery. If preserving the local queue fails, the submitted text returns to the draft.

Queued notes retain their original destination; changing folders affects only new notes. Processing is ordered, so an unavailable earlier destination blocks later queued notes. Restore the missing folder/access and allow retry. Corrupt state is **not automatically reset**: preserve the file named in the error before repairing it.

Local state (separate from your Markdown folder):

- macOS: `~/Library/Application Support/spit/state.json`
- Windows: `%AppData%\spit\state.json`

At the default state location, legacy `admo` state, then `adhd-memo` state, is migrated when no `spit` state exists; existing new state is never overwritten. The Markdown folder and existing note markers stay `admo` for compatibility: migration does not rename, move, or delete your vault folders, and pending notes keep their original destinations.

Notes and state are **unencrypted plaintext**. Hiding the composer is not proof of durable saving: a forced exit or power loss before queue persistence can lose that input. Interrupted appends can leave partial lines; recovery preserves them and appends the complete note. Filesystem/device failures and simultaneous changes by editors or cloud-sync tools are outside the durability guarantee.
