# MagicBox updates

Public **encrypted** updates for Linux ARM64 Radxa E20C/E24C and NanoPi R2C/R2C Plus.
The application source stays private. No GitHub account is needed to check for or
download updates. Ask your administrator for the release security code.

## From the box UI

Open **Device → Software**. When an update is available, enter the security code
and select **Install update**. Downloads are authenticated and verified before
installation. Existing setup, cameras, networks, passwords and enrollment are
retained. The installer backs up the application and attempts rollback if startup
fails. Keep the box powered on during installation.

## Install directly on an online box

Requires curl, SHA256 tools, root/sudo, and the supported vendor networking image.
This also upgrades boxes that do not yet have the update UI.

```sh
tools_dir=$(mktemp -d)
curl -fsSL https://github.com/kaushalkchoudhary/radxa-setup-releases/releases/latest/download/install.sh -o "$tools_dir/install.sh"
sh "$tools_dir/install.sh"
```

Enter the release security code when prompted. It is not written to disk.

## Send an update from a laptop

On Linux or macOS, download both scripts and run the SSH helper:

```sh
tools_dir=$(mktemp -d)
for script in install.sh deploy.sh; do
  curl -fsSL "https://github.com/kaushalkchoudhary/radxa-setup-releases/releases/latest/download/$script" -o "$tools_dir/$script"
done
sh "$tools_dir/deploy.sh" radxa@BOX_IP
```

The box does not need internet access or GitHub credentials for this method.
Use an SSH config alias for custom ports/keys. Set `RADXA_VERSION` to a release
tag to select a particular build. Installation continues if restarting the
console drops the SSH connection; the helper prints its log/result paths.

## Package format

`manifest.json` identifies the source revision, release, sizes and SHA256 hashes.
The appliance executable is encrypted with AES-256-GCM using a fresh salt/nonce
and PBKDF2-HMAC-SHA256 (600,000 iterations). A wrong code or changed ciphertext
fails authentication before any executable is written or run.

`radxa-download-*` are small, unencrypted platform helpers for downloading and
unlocking the package. They contain neither the appliance application nor the
security code. The `.sha256` files verify their downloads. Only completed,
tested builds become releases; boxes never install without an explicit action.
