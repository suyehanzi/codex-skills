# Troubleshooting

## Reinstall an existing unpacked extension

Verified on 2026-11-08 for `ChatGPT` version `1.26.1020.11845`, extension ID `hehggadaopoacecdllhhajmbjkdcmajg`.

1. Read the target extension's registered path from the Chrome profile and confirm the folder contains its `manifest.json`. Check the name, version, ID, and original package provenance before removal. A folder name alone is insufficient.
2. Retain the original CRX, ZIP, and loaded folder. Reinstalling the existing version does not update it to the latest version.
3. With authorization for the uninstall and reinstall, open `chrome://extensions/`, remove only the matching extension, and verify that its card disappears.
4. Use the already-enabled developer mode and choose **Load unpacked**. Select the same verified directory containing `manifest.json`, rather than the ZIP or CRX file.
5. Verify that the card returns with the expected ID and version and its enabled toggle is on. Keep the loaded folder in place because Chrome reads it on subsequent starts.
6. Report local installation and desktop connectivity separately. A welcome page or enabled card proves installation only. Check connectivity with an available browser integration, such as listing Chrome tabs, when that test is in scope. If no such check is available, report it as unverified.

Use the computer/browser tool available in the current environment and respect the user's interaction constraints. A user who has prohibited Computer Use must authorize that method before UI-based reinstallation. Existing approval for the specific reinstall need not be requested again unless the action or access scope changes.

The bundled script's download check accepts the legacy `Codex` manifest strings only. It does not validate the observed `ChatGPT` package. Its profile-directory check can also miss unpacked installations outside `Extensions/`.

## Chrome Web Store still says unavailable

If `https://chromewebstore.google.com/detail/codex/hehggadaopoacecdllhhajmbjkdcmajg` shows "This item is not available", use the script's `--download-check` result to confirm whether Google's update endpoint can still serve the official signed CRX. If the download check fails, ask the user to switch proxy/TUN nodes and try again.

## Chrome installs but disables the extension

External extension installs can appear disabled until the user approves Chrome's warning. Open `chrome://extensions/`, find the verified card for ID `hehggadaopoacecdllhhajmbjkdcmajg` (the name may be `Codex` or `ChatGPT`), and turn on its toggle. If Chrome shows an "Added Codex" permission prompt, click "Enable extension" only after explicit user confirmation.

## Native host is missing

The extension alone is not enough. Codex also needs the native messaging host:

`~/Library/Application Support/Google/Chrome/NativeMessagingHosts/com.openai.codexextension.json`

If it is missing or does not allow `chrome-extension://hehggadaopoacecdllhhajmbjkdcmajg/`, do not hand-edit it unless the user understands the risk. Prefer removing and re-adding the Chrome plugin from Codex Plugins so Codex recreates the host manifest.

## Extension enabled but Codex cannot connect

Run the local checks:

```bash
bash scripts/install_codex_chrome_extension.sh --verify
```

Then start a fresh Codex thread and run a lightweight Chrome plugin check, such as listing open Chrome tabs. If it still fails, remove and re-add the Chrome plugin from Codex Plugins, restart Chrome and Codex, then retry.

## Remove an external-update installation

The following applies to the external-update route. For an unpacked installation, use the procedure above.

To uninstall:

1. Remove `Codex` from `chrome://extensions/`.
2. Delete `~/Library/Application Support/Google/Chrome/External Extensions/hehggadaopoacecdllhhajmbjkdcmajg.json`.
3. Restart Chrome.
