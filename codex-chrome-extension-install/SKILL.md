---
name: codex-chrome-extension-install
description: Install, repair, or verify the Codex / ChatGPT Chrome extension on macOS when store installation fails, an existing unpacked extension needs reinstalling, or the desktop application cannot connect to Chrome.
---

# Codex Chrome Extension Install

## Overview

Use this skill to install or repair the official Codex Chrome Extension on macOS when the normal Chrome Web Store setup flow fails. The core workaround writes Chrome's external extension configuration for the official extension ID so Chrome pulls the signed CRX from Google's update service.

Official extension constants:

- Extension ID: `hehggadaopoacecdllhhajmbjkdcmajg`
- Native host: `com.openai.codexextension`
- Update URL: `https://clients2.google.com/service/update2/crx`
- Observed legacy name and description: `Codex` / `Control Chrome with Codex.`
- Observed name and description in version `1.26.1020.11845`: `ChatGPT` / `Control your browser with ChatGPT.`

Display names vary by package version. Check the extension ID and package provenance alongside the name. The bundled script currently expects the legacy manifest name and description, so its download check rejects the observed ChatGPT package.

## Safety

Installing this extension gives Chrome high-risk permissions: read and change data on all websites, read and change browsing history, access the debugger, manage downloads/bookmarks/tab groups, and communicate with native applications.

Before running an install or clicking Chrome's "Enable extension" prompt, get explicit user confirmation that they want to install the official Codex Chrome Extension. Do not install similarly named Chrome Web Store results such as "Codex Chrome Bridge" unless the user separately asks for that third-party extension.

## Choose the installation route

Inspect the existing extension before changing it. An unpacked extension may be registered in the profile's `Secure Preferences` with a path outside the profile's `Extensions` directory. Its absence from that directory does not establish that it is uninstalled. Read only the target extension entry when inspecting preferences.

For an existing unpacked installation with its verified source folder still available, follow the reinstall procedure in [Troubleshooting](references/troubleshooting.md#reinstall-an-existing-unpacked-extension). This route was verified on 2026-11-08 with version `1.26.1020.11845`.

For the legacy external-update installation, use the workflow below. Read the script's compatibility limits before relying on its checks. Preserve a working source folder until replacement installation succeeds.

## External-update workflow

1. Confirm the machine is macOS with Google Chrome installed.
2. If the store is unreachable, check the reported network failure before proposing a proxy or TUN change.
3. Run a read-only check:

   ```bash
   bash scripts/install_codex_chrome_extension.sh --check
   ```

4. If the official Chrome Web Store page still shows unavailable but the Google update endpoint can serve the CRX, run the manual install after user confirmation:

   ```bash
   bash scripts/install_codex_chrome_extension.sh --install --download-check --restart --open-extensions --yes
   ```

5. In Chrome, handle only the verified extension prompt/card for the target ID. Click "Enable extension" or toggle the extension on if Chrome leaves it disabled after external installation.
6. Verify the local Chrome state:

   ```bash
   bash scripts/install_codex_chrome_extension.sh --verify
   ```

7. If the Codex Chrome plugin is available in the current Codex environment, perform a lightweight browser connection check by listing Chrome tabs. If communication still fails and the extension is installed/enabled, remove and re-add the Chrome plugin from Codex Plugins, then retry in a new Codex thread.

## Script Notes

Use `bash scripts/install_codex_chrome_extension.sh` for deterministic operations. Running through `bash` is intentional because GitHub uploads may not preserve the executable bit.

- `--check`: read-only environment, package, native host, and install-state checks.
- `--download-check`: download the official CRX to a temp directory and inspect `manifest.json`.
- `--install`: write Chrome's external extension JSON.
- `--restart`: quit and reopen Google Chrome.
- `--open-extensions`: open `chrome://extensions/`.
- `--verify`: inspect the target profile registration and disable reasons. This is a local installation check, not a desktop connection test; an unpacked folder may be outside the profile extension directory.
- `--yes`: skip the script's interactive install confirmation after the user has already confirmed in chat.

The script has not been adapted to the ChatGPT package name. For that package, use the verified unpacked reinstall route and Chrome UI checks instead of treating script success as installation proof.

The script intentionally does not repair the native messaging host. If the native host manifest is missing or invalid, use Codex's Chrome plugin setup flow again from Plugins.

## Troubleshooting

Read `references/troubleshooting.md` when:

- Chrome installs the extension but keeps it disabled.
- The extension is enabled but Codex cannot connect.
- The user wants to remove the manual install.
- The Chrome Web Store page remains unavailable after TUN/proxy changes.
