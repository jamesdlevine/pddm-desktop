# PDDM Desktop

A desktop application for Prompt-Driven Development projects managed by PDDM.
It bundles the PDDM Workspace and PDDM Builder views, so you do not need to
install VS Code or Cursor.

**[Download the latest release →](https://github.com/jamesdlevine/pddm-desktop/releases/latest)**

macOS on Apple silicon (arm64). Linux is planned; Windows is not.

This repository holds releases only — the application source lives elsewhere.

---

## Installing

1. Download the `.dmg` from the [releases page](https://github.com/jamesdlevine/pddm-desktop/releases/latest).
2. Open it and drag **pddm-desktop** to your Applications folder.
3. The first launch needs one extra step — see below.

## First launch: "cannot be opened because the developer cannot be verified"

macOS will refuse to open the app the first time, with a message like
*"pddm-desktop cannot be opened because the developer cannot be verified"* or
*"Apple could not verify pddm-desktop is free of malware."*

This is expected. The app is signed, but with an ad-hoc signature rather than a
paid Apple Developer ID, and it is not notarized — macOS treats anything
downloaded from the internet without notarization this way.

Two ways past it. The first is easier; the second is faster if you are
comfortable in a terminal.

### Option 1 — Open Anyway (recommended)

1. Double-click **pddm-desktop**. Dismiss the warning dialog.
2. Open **System Settings → Privacy & Security**.
3. Scroll to the **Security** section. Within about an hour of the blocked
   attempt you will see a line naming pddm-desktop with an **Open Anyway**
   button. Click it.
4. Authenticate with Touch ID or your password.
5. Launch the app again and click **Open** in the confirmation dialog.

The step order matters: **Open Anyway** only appears *after* macOS has blocked a
launch, so you have to try opening it first. If you do not see the button, try
launching once more and go straight back to Privacy & Security.

### Option 2 — remove the quarantine flag

The warning comes from an extended attribute macOS attaches to downloaded
files. Removing it from the installed app clears the warning permanently:

```bash
xattr -dr com.apple.quarantine /Applications/pddm-desktop.app
```

Then launch normally.

Two notes on this command:

- Run it against the **installed app in `/Applications`**, not the `.dmg` and
  not a copy still sitting in `~/Downloads`.
- The installed bundle is always named `pddm-desktop.app` regardless of
  version — the version number appears only in the `.dmg` filename
  (`pddm-desktop-0.1.3-arm64.dmg`) — so the command above does not change from
  one release to the next.

To clear it on the download instead, quote a glob rather than typing the
version out:

```bash
xattr -dr com.apple.quarantine ~/Downloads/pddm-desktop-*-arm64.dmg
```

Doing it on the `.dmg` before dragging to Applications works too, and saves
doing it afterwards.

## First run

The first launch provisions a private Python environment (about 2.8 GB) from a
wheel bundled inside the app. Expect a few minutes on the setup progress bar
before the views come alive. Later launches are immediate.

## Updating

The app checks for a newer release on launch and offers to open the download
page. Install the new `.dmg` over the old one — the same drag to Applications,
replacing when asked.

You will need the Gatekeeper step again for each new version: the quarantine
flag comes attached to every new download, and Option 1's approval is granted
per version too.

An update prompt can be dismissed with **Remind Me Later**, or silenced for one
specific version with **Skip This Version**.

The PDDM extension inside the app updates separately and silently — it is
fetched fresh on every launch, so extension fixes arrive without reinstalling
anything.

## Where things are stored

The app keeps almost nothing inside its own bundle:

| Location | Holds |
|---|---|
| `/Applications/pddm-desktop.app` | The shell. No extension, no Python. |
| `~/Library/Application Support/pddm-desktop/plugins/` | The PDDM extension, fetched at launch |
| `~/Library/Application Support/pddm-desktop/Local Storage/` | Window layout and frontend state |
| `~/.pddm-vscode-extension/` | The managed Python environment |

If the app is misbehaving, deleting those three folders and relaunching returns
it to a first-run state without touching any of your projects. The layout store
is also cleared automatically whenever the bundled extension version changes.
