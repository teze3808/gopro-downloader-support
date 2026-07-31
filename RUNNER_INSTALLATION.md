# Install the GoPro Downloader Runner

This guide starts after the **GoPro Downloader** Chrome extension has been installed.

The aria2 runner is recommended for a large GoPro Cloud library or an unreliable internet connection. It runs in Docker on your Mac, Windows PC, QNAP, or Synology NAS and provides resumable downloads, retries, inventory comparison, and a dashboard.

The runner operates only on the computer or NAS that you choose. Do not expose it directly to the public internet.

## What You Need

- Google Chrome with the GoPro Downloader extension installed.
- A Mac, Windows PC, QNAP, or Synology NAS with enough free storage.
- OrbStack or Docker Desktop on Mac, Docker Desktop on Windows, QNAP Container Station, or Synology Container Manager.
- One persistent folder mounted read/write to `/data` inside the container.
- Runner image: `teze3808/gopro-downloader-runner:latest`
- Container port: `8766`

The runner automatically creates:

```text
/data/gopro     Downloaded GoPro media
/data/batches   Cloud inventory, batch manifests, state, and logs
/data/certs     HTTPS certificate and local certificate authority
```

## Choose Your Installation

| Where the runner will run | Recommended method | Runner URL before HTTPS |
| --- | --- | --- |
| Same Mac as Chrome | [Mac with OrbStack (recommended)](#option-1-mac-with-orbstack-recommended) | `http://127.0.0.1:8766` |
| Same Mac, Docker Desktop already installed | [Mac with Docker Desktop](#option-2-mac-with-docker-desktop) | `http://127.0.0.1:8766` |
| Same Windows PC as Chrome | [Windows with Docker Desktop and WSL 2](#option-3-windows-with-docker-desktop-and-wsl-2) | `http://127.0.0.1:8766` |
| QNAP NAS | [QNAP Container Station](#option-4-qnap-container-station) | `http://QNAP_IP:ASSIGNED_PORT` |
| Synology NAS | [Synology Container Manager](#option-5-synology-container-manager) | `http://SYNOLOGY_IP:ASSIGNED_PORT` |

Use `127.0.0.1` only when Chrome and the runner are on the same computer. For a NAS, use the NAS IP address and complete the HTTPS wizard.

## Option 1: Mac With OrbStack (Recommended)

OrbStack is the simplest recommended option for a personal Mac. It provides a lightweight Docker-compatible environment, and the standard `docker` commands in this guide work without modification.

### Step 1: Install OrbStack

1. Open the official [OrbStack website](https://orbstack.dev/) or [OrbStack documentation](https://docs.orbstack.dev/).
2. Download OrbStack and move it to the `Applications` folder.
3. Open OrbStack.
4. Complete the short first-run setup.
5. Wait until OrbStack reports that Docker is running.

A Docker Hub account is not required to run the public runner image.

### Step 2: Create the Storage Folder

Open `Terminal` from `Applications` > `Utilities`, then run:

```bash
mkdir -p "$HOME/GoPro-Downloader"
```

All downloaded media, runner state, logs, and certificates will stay in this folder.

### Step 3: Start the Runner

Paste this entire command into Terminal:

```bash
docker run -d \
  --name gopro-downloader-runner \
  --restart unless-stopped \
  -p 127.0.0.1:8766:8766 \
  -v "$HOME/GoPro-Downloader:/data" \
  teze3808/gopro-downloader-runner:latest
```

Verify that it is running:

```bash
docker ps --filter name=gopro-downloader-runner
```

The command should list a running container named `gopro-downloader-runner`.

### Step 4: Connect the Extension

1. Open [http://127.0.0.1:8766](http://127.0.0.1:8766) in Chrome.
2. Confirm that the GoPro Downloader Runner dashboard appears.
3. Open the GoPro Downloader extension.
4. Set `Download method` to `aria2 runner`.
5. Enter:

   ```text
   http://127.0.0.1:8766
   ```

6. Click `Open runner dashboard` to test it.

Because the runner is on the same Mac, localhost HTTP is allowed. You do not need to install a certificate unless you want to access this runner from another computer.

## Option 2: Mac With Docker Desktop

### Step 1: Install Docker Desktop

1. Open the official [Docker Desktop for Mac installation guide](https://docs.docker.com/desktop/setup/install/mac-install/).
2. Choose the correct download:
   - **Apple silicon** for M1, M2, M3, M4, M5, and later Apple chips.
   - **Intel** for an Intel-based Mac.
3. Open `Docker.dmg`.
4. Drag Docker into the `Applications` folder.
5. Open Docker from Applications.
6. Accept the license and choose the recommended settings.
7. Wait until Docker reports that the engine is running.

Docker Desktop may request your Mac login password during installation. A Docker Hub account is not required to run the public runner image.

### Step 2: Create the Storage Folder

Open `Terminal` from `Applications` > `Utilities`, then run:

```bash
mkdir -p "$HOME/GoPro-Downloader"
```

All downloaded media, runner state, logs, and certificates will stay in this folder.

### Step 3: Start the Runner

Paste this entire command into Terminal:

```bash
docker run -d \
  --name gopro-downloader-runner \
  --restart unless-stopped \
  -p 127.0.0.1:8766:8766 \
  -v "$HOME/GoPro-Downloader:/data" \
  teze3808/gopro-downloader-runner:latest
```

Verify that it is running:

```bash
docker ps --filter name=gopro-downloader-runner
```

### Step 4: Connect the Extension

1. Open [http://127.0.0.1:8766](http://127.0.0.1:8766) in Chrome.
2. Confirm that the GoPro Downloader Runner dashboard appears.
3. Open the GoPro Downloader extension.
4. Set `Download method` to `aria2 runner`.
5. Enter:

   ```text
   http://127.0.0.1:8766
   ```

6. Click `Open runner dashboard` to test it.

Because the runner is on the same Mac, localhost HTTP is allowed. You do not need to install a certificate unless you want to access this runner from another computer.

## Option 3: Windows With Docker Desktop and WSL 2

### Step 1: Install Docker Desktop

1. Open the official [Docker Desktop for Windows installation guide](https://docs.docker.com/desktop/setup/install/windows-install/).
2. Download and run `Docker Desktop Installer.exe`.
3. Use the recommended per-user installation.
4. Keep the WSL 2 option selected when offered.
5. Restart Windows if the installer requests it.
6. Open Docker Desktop and wait until the engine is running.

If Docker reports that WSL 2 or virtualization is unavailable:

- Install Windows updates.
- Enable virtualization in the computer BIOS/UEFI.
- Follow Docker's WSL 2 setup instructions from the official guide above.

Docker Desktop should be using **Linux containers**, which is its normal default for this runner.

### Step 2: Create the Storage Folder

Open `PowerShell`, then run:

```powershell
$DataPath = Join-Path $HOME "GoPro-Downloader"
New-Item -ItemType Directory -Force -Path $DataPath
```

### Step 3: Start the Runner

Continue in the same PowerShell window:

```powershell
docker run -d `
  --name gopro-downloader-runner `
  --restart unless-stopped `
  -p 127.0.0.1:8766:8766 `
  --mount "type=bind,source=$DataPath,target=/data" `
  teze3808/gopro-downloader-runner:latest
```

Verify that it is running:

```powershell
docker ps --filter "name=gopro-downloader-runner"
```

### Step 4: Connect the Extension

1. Open [http://127.0.0.1:8766](http://127.0.0.1:8766) in Chrome.
2. Confirm that the dashboard appears.
3. Open the GoPro Downloader extension.
4. Set `Download method` to `aria2 runner`.
5. Enter:

   ```text
   http://127.0.0.1:8766
   ```

6. Click `Open runner dashboard`.

Because the runner is on the same Windows PC, localhost HTTP is allowed.

## Option 4: QNAP Container Station

The labels may differ slightly between Container Station versions.

### Step 1: Prepare QNAP

1. Open QTS or QuTS hero.
2. Open `App Center`.
3. Install or update `Container Station`.
4. Open `File Station`.
5. Choose or create a persistent folder for the runner, for example:

   ```text
   /Multimedia/gopro_media
   ```

6. Make sure the folder is writable by Container Station.

You need only one storage mapping. The runner creates its own `gopro`, `batches`, and `certs` subfolders.

### Step 2: Download the Image

1. Open `Container Station`.
2. Open `Images`, `Create`, or the Docker Hub search page, depending on your version.
3. Search for:

   ```text
   teze3808/gopro-downloader-runner
   ```

4. Select the `latest` tag and download or install it.
5. Create a new container from the image.

<img src="screenshots/qnap-01-select-image.png" alt="QNAP Container Station image selection" width="900">

### Step 3: Configure the Container

Use these settings:

| Setting | Value |
| --- | --- |
| Container name | `gopro-downloader-runner` |
| Restart policy | `Unless stopped` |
| Network protocol | TCP |
| Host port | Automatic/random |
| Container port | `8766` |
| Host storage folder | Your chosen folder, for example: `/Multimedia/gopro_media` |
| Container storage path | `/data` |
| Storage access | Read/write |

Do not override the command or entrypoint. Privileged mode, interactive mode, and TTY are not required.

Enter the container name, select `Unless Stopped`, and open `Advanced Settings`:

<img src="screenshots/qnap-02-configure-container.png" alt="QNAP container name and advanced settings" width="900">

Under `Storage`, map your chosen QNAP folder to `/data` with read/write access:

<img src="screenshots/qnap-03-storage-mapping.png" alt="QNAP storage mapping from a host folder to container path data" width="900">

Apply the settings and start the container. Container Station selects an available host port.

Review the summary and click `Finish`:

<img src="screenshots/qnap-04-review-finish.png" alt="QNAP container summary and Finish button" width="900">

### Step 4: Open the Dashboard

1. Open the running container's details in Container Station.
2. Find the host port assigned to container port `8766`.
3. Find the QNAP IP address in QTS.
4. Open:

Confirm that the container is running, then open its details:

<img src="screenshots/qnap-05-verify-running.png" alt="QNAP Container Station showing the runner container is running" width="900">

Find the host port mapped to `8766/TCP`. In this example, the assigned host port is `32773`:

<img src="screenshots/qnap-06-find-host-port.png" alt="QNAP container details showing the assigned host port" width="900">

```text
http://QNAP_IP:ASSIGNED_PORT
```

Example:

```text
http://192.168.1.50:49157
```

The assigned port may differ from the example. A normal container restart usually keeps it, but recreating the container can assign a new port.

The official [QNAP container creation documentation](https://docs.qnap.com/operating-system/qne-network/1.0.x/en-us/container-creation-1A95801A.html) explains Docker Hub image selection, port forwarding, and volume settings.

### Step 5: Complete HTTPS Setup

Continue with [HTTPS Setup for a NAS Runner](#https-setup-for-a-nas-runner).

## Option 5: Synology Container Manager

This method uses the Container Manager interface. No extra configuration file or command line is required.

The screenshots below use Synology's earlier `Docker` package. On newer DSM versions, open `Container Manager`; the image, port, and volume settings are equivalent even when the labels look slightly different.

### Step 1: Prepare Synology

1. Open DSM.
2. Open `Package Center`.
3. Install or update `Container Manager`.
4. Open `File Station`.
5. Choose or create a persistent folder for the runner, for example:

   ```text
   /video/gopro_media
   ```

The folder can be on any Synology volume, provided Container Manager has read/write access.

### Step 2: Download the Image

1. Open `Container Manager`.
2. Select `Registry`.
3. Search for:

   ```text
   teze3808/gopro-downloader-runner
   ```

4. Select the image and click `Download`.
5. Select the `latest` tag.
6. Wait for the download to finish.

Search Docker Hub for the runner:

<img src="screenshots/synology-01-registry-search.png" alt="Synology Docker registry search for GoPro Downloader Runner" width="900">

Select the downloaded `latest` image:

<img src="screenshots/synology-02-select-image.png" alt="Synology Docker image list with GoPro Downloader Runner selected" width="900">

### Step 3: Create the Container

1. Select `Image`.
2. Select `teze3808/gopro-downloader-runner:latest`.
3. Click `Run`.
4. Enter the container name:

   ```text
   gopro-downloader-runner
   ```

5. Enable automatic restart.
6. Add a TCP port mapping. Leave the local port blank or select `Auto` so Container Manager assigns an available port:

   | Local port | Container port |
   | --- | --- |
   | Automatic/random | `8766` |

7. Add one volume mapping:

   | Synology folder | Container path | Access |
   | --- | --- | --- |
   | Your chosen folder, for example: `/video/gopro_media` | `/data` | Read/write |

8. Keep the default bridge network.
9. Do not override the command or entrypoint.
10. Review the settings and start the container.

Set the container name and open `Advanced Settings`:

<img src="screenshots/synology-03-general-settings.png" alt="Synology runner container general settings" width="900">

In `Volume`, add one writable folder and map it to `/data`:

<img src="screenshots/synology-04-volume-mapping.png" alt="Synology volume mapping from a host folder to container path data" width="900">

Review the automatically assigned local port, container port `8766`, and `/data` volume before clicking `Apply`:

<img src="screenshots/synology-05-review-apply.png" alt="Synology runner container summary and Apply button" width="900">

### Step 4: Open the Dashboard

1. Open the running container's details in Container Manager.
2. Find the local port assigned to container port `8766`.
3. Find the Synology IP address in DSM.
4. Open:

```text
http://SYNOLOGY_IP:ASSIGNED_PORT
```

Example:

```text
http://192.168.1.60:49158
```

The assigned port may differ from the example. A normal container restart usually keeps it, but recreating the container can assign a new port.

### Step 5: Complete HTTPS Setup

Continue with the next section.

## HTTPS Setup for a NAS Runner

Chrome extensions require a secure HTTPS connection when the runner is on another computer or NAS. The runner's first-visit wizard creates a private local certificate authority and a certificate for the NAS address.

Keep `/data/certs` safe and persistent. If it is deleted, the runner creates a new certificate authority and every Chrome computer must trust the new certificate again.

### Step 1: Generate the Certificate

1. Open the runner's HTTP dashboard using its NAS IP address.
2. Open or reopen `Connection security` / `HTTPS setup`.
3. Confirm that the detected address is the same IP address used by the Chrome computer.
4. Click `Enable HTTPS`.
5. Wait for the runner to generate its certificate, restart, and redirect the browser to HTTPS.
6. Download the CA certificate from step 2 of the dashboard wizard.

<img src="screenshots/https-01-enable-https.png" alt="Runner dashboard Enable HTTPS button" width="900">

The downloaded file is normally named `gopro-downloader-runner-ca.crt`.

<img src="screenshots/https-02-download-ca.png" alt="Runner HTTPS wizard Download CA certificate button" width="900">

### Step 2A: Trust the Certificate on Mac

1. Open the downloaded `gopro-downloader-runner-ca.crt`.
2. If macOS opens `Keychain Access`, add it to the `System` keychain.
3. Open `System` > `Certificates` and find `GoPro Downloader Runner Local CA`.
4. Double-click it.
5. Expand `Trust`.
6. Set `When using this certificate` to `Always Trust`.
7. Close the window and enter the Mac password when requested.
8. Completely quit Chrome with `Chrome` > `Quit Google Chrome`.
9. Reopen Chrome.

In Keychain Access, select the `System` keychain and find `GoPro Downloader Runner Local CA`. Before it is trusted, macOS shows a red warning:

<img src="screenshots/mac-ca-01-install-certificate.png" alt="GoPro Downloader Runner Local CA installed in the macOS System keychain but not yet trusted" width="900">

Double-click the certificate, expand `Trust`, and set `When using this certificate` to `Always Trust`:

<img src="screenshots/mac-ca-02-always-trust.png" alt="macOS Keychain Access setting the runner CA certificate to Always Trust" width="620">

After approving the change, return to the `System` keychain. The certificate should say that it is trusted for all users:

<img src="screenshots/mac-ca-03-verify-installed.png" alt="macOS Keychain Access showing the runner CA certificate trusted for all users" width="900">

### Step 2B: Trust the Certificate on Windows

1. Double-click `gopro-downloader-runner-ca.crt`.
2. Click `Install Certificate`.
3. Select `Local Machine` and approve the administrator prompt.
4. Select `Place all certificates in the following store`.
5. Choose `Trusted Root Certification Authorities`.
6. Finish the wizard and accept the security warning.
7. Completely close every Chrome window.
8. Reopen Chrome.

Open the downloaded certificate:

<img src="screenshots/windows-ca-01-downloaded-file.png" alt="Downloaded runner CA certificate in Windows Downloads" width="700">

Click `Install Certificate`:

<img src="screenshots/windows-ca-02-install-certificate.png" alt="Windows certificate window with Install Certificate button" width="520">

Choose `Local Machine`:

<img src="screenshots/windows-ca-03-local-machine.png" alt="Windows Certificate Import Wizard Local Machine option" width="520">

Place the certificate in `Trusted Root Certification Authorities`:

<img src="screenshots/windows-ca-04-trusted-root-store.png" alt="Windows Trusted Root Certification Authorities certificate store selection" width="520">

Finish the import and confirm that you trust the CA generated by your runner:

<img src="screenshots/windows-ca-05-finish-import.png" alt="Windows Certificate Import Wizard Finish button" width="520">

<img src="screenshots/windows-ca-06-confirm-trust.png" alt="Windows security warning confirming trust for the runner CA" width="520">

To verify the installation, search Windows for `Manage computer certificates`:

<img src="screenshots/windows-ca-07-open-certificate-manager.png" alt="Windows search result for Manage computer certificates" width="700">

The certificate should appear under `Trusted Root Certification Authorities` > `Certificates`:

<img src="screenshots/windows-ca-08-verify-installed.png" alt="GoPro Downloader Runner Local CA in the Windows trusted root store" width="700">

Install only a CA certificate generated by your own runner. Do not share the CA private key stored in `/data/certs`.

### Step 3: Test HTTPS

1. Return to the runner wizard.
2. Copy the HTTPS URL shown by the runner, for example:

   ```text
   https://192.168.1.50:ASSIGNED_PORT
   ```

3. Open that URL directly in Chrome.
4. Confirm that the dashboard opens without a certificate warning.

The final wizard step displays the exact URL to copy into the extension:

<img src="screenshots/https-03-copy-runner-url.png" alt="Runner HTTPS wizard showing the final runner URL" width="900">

If Chrome still shows a privacy warning, do not bypass it. Check that:

- The certificate was installed in the trusted root store.
- Chrome was fully restarted.
- The URL uses the same IP address that the runner detected when generating the certificate.
- The `/data/certs` folder is writable and persistent.

## Connect the Extension to a NAS Runner

1. Sign in to [GoPro Media Library](https://gopro.com/media-library/) in Chrome.
2. Open the GoPro Downloader extension.
3. Set `Download method` to `aria2 runner`.
4. Enter the final HTTPS runner URL, including the port.
5. Click `Open runner dashboard`.
6. If Chrome requests permission for the runner address, approve it.
7. Return to the GoPro Media Library tab.
8. Open the extension again.
9. Click `Start / Retry all downloads`.

<img src="screenshots/extension-01-start-download.png" alt="GoPro Downloader extension configured for the aria2 runner" width="420">

The extension first gathers or updates the GoPro Cloud inventory. The runner compares that inventory with local files and queues only media that is missing or incomplete.

Keep the GoPro Media Library tab open while the extension is gathering inventory and resolving fresh download URLs.

## Check Progress

Open the runner dashboard to see:

- Cloud media count.
- Locally complete files.
- Missing or partial files.
- Files needing download or retry.
- Current and previous runs.
- Batch details and URL lookup failures.

A batch can finish while another batch is still being prepared. Use the overall **Cloud Media vs Local Downloaded** section to decide whether the complete library has finished.

The extension also shows the current inventory page and a detailed action log:

<img src="screenshots/extension-02-download-progress.png" alt="GoPro Downloader extension current status and download log" width="420">

## Stop, Start, and Update

### Docker Desktop or Docker Engine

```bash
docker stop gopro-downloader-runner
docker start gopro-downloader-runner
docker logs --tail 100 gopro-downloader-runner
```

To update a container created with `docker run`:

```bash
docker pull teze3808/gopro-downloader-runner:latest
docker stop gopro-downloader-runner
docker rm gopro-downloader-runner
```

Then run the original `docker run` command again with the same `/data` mapping. Removing the container does not remove files in the mapped host folder.

### QNAP

1. Record the existing `/data` storage mapping.
2. Stop the runner container.
3. Download the newest `latest` image.
4. Recreate the container with container port `8766` and the same `/data` host-folder mapping.
5. Check the newly assigned host port.
6. Start it and confirm the dashboard still shows the existing inventory.
7. If the assigned port changed, update the runner URL in the extension.

### Synology

1. Record the existing `/data` storage mapping.
2. Download the newest `latest` image from `Registry`.
3. Stop the existing runner container.
4. Recreate it from the new image with container port `8766` and the same read/write host folder mapped to `/data`.
5. Check the newly assigned local port.
6. Start it and confirm the dashboard still shows the existing inventory.
7. If the assigned port changed, update the runner URL in the extension.

Never delete the mapped host data folder during an update.

## Troubleshooting

### Dashboard Does Not Open

- Confirm that the container status is running.
- Confirm that host port maps to container port `8766`.
- Try another unused host port.
- Check the Mac/Windows firewall or NAS firewall.
- Make sure you are using the NAS IP address, not `127.0.0.1`, from another computer.

### Runner Cannot Write Files

- Confirm that the host folder is mapped to `/data`.
- Confirm that the mapping is read/write.
- Confirm that Docker or the NAS container service has permission to use the host folder.
- Do not map separate folders over `/data/gopro`, `/data/batches`, or `/data/certs` on a new installation.

### Extension Says `Failed to fetch`

- Open the exact runner URL directly in Chrome first.
- For a NAS runner, confirm the URL starts with `https://`.
- Install the runner CA certificate and restart Chrome.
- Confirm that the extension runner URL includes the correct port.
- Confirm that the Chrome computer can reach the NAS on the local network.

### Existing File Is Still Queued

- Let the runner finish its inventory scan.
- Check whether the existing file size matches the cloud inventory.
- A zero-byte or partial file is intentionally queued again.
- Use `Start / Retry all downloads` again after temporary URL lookup failures.

### Getting Support

1. Open the extension.
2. Expand `Advanced settings`.
3. Click `Copy support summary`.
4. Open [Help & feedback](https://github.com/teze3808/gopro-downloader-support/issues/new/choose) and select `Installation help` or `Bug report`.
5. Paste the summary and describe the platform, NAS model if applicable, runner URL with the IP redacted if preferred, and what happened.
6. If requested, use `Download diagnostic log` and review it before attaching.

Never post GoPro passwords, browser cookies, signed media URLs, CA private keys, or private filenames in a public issue.
