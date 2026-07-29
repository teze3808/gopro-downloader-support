# Install the GoPro Downloader Runner

This guide starts after the **GoPro Downloader** Chrome extension has been installed.

The aria2 runner is recommended for a large GoPro Cloud library or an unreliable internet connection. It runs in Docker on your Mac, Windows PC, QNAP, or Synology NAS and provides resumable downloads, retries, inventory comparison, and a dashboard.

The runner operates only on the computer or NAS that you choose. Do not expose it directly to the public internet.

## What You Need

- Google Chrome with the GoPro Downloader extension installed.
- A Mac, Windows PC, QNAP, or Synology NAS with enough free storage.
- Docker Desktop, QNAP Container Station, or Synology Container Manager.
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
| Same Mac as Chrome | [Mac with Docker Desktop](#option-1-mac-with-docker-desktop) | `http://127.0.0.1:8766` |
| Same Windows PC as Chrome | [Windows with Docker Desktop](#option-2-windows-with-docker-desktop) | `http://127.0.0.1:8766` |
| QNAP NAS | [QNAP Container Station](#option-3-qnap-container-station) | `http://QNAP_IP:32771` |
| Synology NAS | [Synology Container Manager](#option-4-synology-container-manager) | `http://SYNOLOGY_IP:8766` |

Use `127.0.0.1` only when Chrome and the runner are on the same computer. For a NAS, use the NAS IP address and complete the HTTPS wizard.

## Option 1: Mac With Docker Desktop

### Step 1: Install Docker Desktop

> **Screenshot placeholder: Mac Docker Desktop installation**  
> File: `screenshots/mac-01-docker-desktop-install.png`  
> Show the Docker Desktop download or installation screen for Apple silicon and Intel Macs.

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

> **Screenshot placeholder: Mac Docker Desktop running**  
> File: `screenshots/mac-02-runner-container-running.png`  
> Show Docker Desktop with `gopro-downloader-runner` in the running state.

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

## Option 2: Windows With Docker Desktop

### Step 1: Install Docker Desktop

> **Screenshot placeholder: Windows Docker Desktop installation**  
> File: `screenshots/windows-01-docker-desktop-install.png`  
> Show the Docker Desktop installer with the WSL 2 option.

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

> **Screenshot placeholder: Windows runner container running**  
> File: `screenshots/windows-02-runner-container-running.png`  
> Show Docker Desktop with `gopro-downloader-runner` in the running state.

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

## Option 3: QNAP Container Station

The labels may differ slightly between Container Station versions.

### Step 1: Prepare QNAP

1. Open QTS or QuTS hero.
2. Open `App Center`.
3. Install or update `Container Station`.
4. Open `File Station`.
5. Create a persistent folder for the runner, for example:

   ```text
   /share/Container/gopro-downloader
   ```

6. Make sure the folder is writable by Container Station.

You need only one storage mapping. The runner creates its own `gopro`, `batches`, and `certs` subfolders.

### Step 2: Download the Image

> **Screenshot placeholder: QNAP Docker Hub image**  
> File: `screenshots/qnap-01-runner-image.png`  
> Show Container Station selecting `teze3808/gopro-downloader-runner:latest`.

1. Open `Container Station`.
2. Open `Images`, `Create`, or the Docker Hub search page, depending on your version.
3. Search for:

   ```text
   teze3808/gopro-downloader-runner
   ```

4. Select the `latest` tag and download or install it.
5. Create a new container from the image.

### Step 3: Configure the Container

> **Screenshot placeholder: QNAP port and storage mappings**  
> File: `screenshots/qnap-02-port-storage-mapping.png`  
> Show host port `32771` to container port `8766`, plus one read/write host folder mapped to `/data`.

Use these settings:

| Setting | Value |
| --- | --- |
| Container name | `gopro-downloader-runner` |
| Restart policy | `Unless stopped` |
| Network protocol | TCP |
| Host port | `32771` or another unused port |
| Container port | `8766` |
| Host storage folder | `/share/Container/gopro-downloader` or your chosen folder |
| Container storage path | `/data` |
| Storage access | Read/write |

Do not override the command or entrypoint. Privileged mode, interactive mode, and TTY are not required.

Apply the settings and start the container.

### Step 4: Open the Dashboard

> **Screenshot placeholder: QNAP runner dashboard**  
> File: `screenshots/qnap-03-runner-dashboard.png`  
> Show the runner dashboard opened using the QNAP IP address and host port.

Find the QNAP IP address in QTS, then open:

```text
http://QNAP_IP:32771
```

Example:

```text
http://192.168.1.50:32771
```

The official [QNAP container creation documentation](https://docs.qnap.com/operating-system/qne-network/1.0.x/en-us/container-creation-1A95801A.html) explains Docker Hub image selection, port forwarding, and volume settings.

### Step 5: Complete HTTPS Setup

Continue with [HTTPS Setup for a NAS Runner](#https-setup-for-a-nas-runner).

## Option 4: Synology Container Manager

This method uses the Container Manager interface. No extra configuration file or command line is required.

### Step 1: Prepare Synology

> **Screenshot placeholder: Synology storage folder**
> File: `screenshots/synology-01-storage-folder.png`
> Show the `gopro-downloader` folder in File Station.

1. Open DSM.
2. Open `Package Center`.
3. Install or update `Container Manager`.
4. Open `File Station`.
5. Create a persistent folder:

   ```text
   /volume1/docker/gopro-downloader
   ```

If your Docker shared folder is on another volume, replace `volume1` with the correct volume.

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

### Step 3: Create the Container

> **Screenshot placeholder: Synology port and storage mappings**
> File: `screenshots/synology-02-port-storage-mapping.png`
> Show host port `8766` to container port `8766`, plus one read/write host folder mapped to `/data`.

1. Select `Image`.
2. Select `teze3808/gopro-downloader-runner:latest`.
3. Click `Run`.
4. Enter the container name:

   ```text
   gopro-downloader-runner
   ```

5. Enable automatic restart.
6. Add a TCP port mapping:

   | Local port | Container port |
   | --- | --- |
   | `8766` | `8766` |

7. Add one volume mapping:

   | Synology folder | Container path | Access |
   | --- | --- | --- |
   | `/volume1/docker/gopro-downloader` | `/data` | Read/write |

8. Keep the default bridge network.
9. Do not override the command or entrypoint.
10. Review the settings and start the container.

### Step 4: Open the Dashboard

> **Screenshot placeholder: Synology runner dashboard**
> File: `screenshots/synology-03-runner-dashboard.png`
> Show the runner dashboard opened using the Synology IP address and host port.

Open:

```text
http://SYNOLOGY_IP:8766
```

Example:

```text
http://192.168.1.60:8766
```

If local port `8766` is already in use, choose another local port such as `32771`, keep the container port as `8766`, and open `http://SYNOLOGY_IP:32771`.

### Step 5: Complete HTTPS Setup

Continue with the next section.

## HTTPS Setup for a NAS Runner

Chrome extensions require a secure HTTPS connection when the runner is on another computer or NAS. The runner's first-visit wizard creates a private local certificate authority and a certificate for the NAS address.

> **Screenshot placeholder: Runner HTTPS wizard**  
> File: `screenshots/https-01-runner-wizard.png`  
> Show the wizard detecting the NAS IP address and offering `Enable HTTPS`.

Keep `/data/certs` safe and persistent. If it is deleted, the runner creates a new certificate authority and every Chrome computer must trust the new certificate again.

### Step 1: Generate the Certificate

1. Open the runner's HTTP dashboard using its NAS IP address.
2. Open or reopen `Connection security` / `HTTPS setup`.
3. Confirm that the detected address is the same IP address used by the Chrome computer.
4. Click `Enable HTTPS`.
5. Wait for the runner to generate its certificate, restart, and redirect the browser to HTTPS.
6. Download the CA certificate from step 2 of the dashboard wizard.

The downloaded file is normally named `gopro-downloader-runner-ca.crt`.

### Step 2A: Trust the Certificate on Mac

> **Screenshot placeholder: Trust the runner CA on Mac**  
> File: `screenshots/https-02-mac-keychain-trust.png`  
> Show the imported certificate in Keychain Access with `Always Trust` selected.

1. Open the downloaded `gopro-downloader-runner-ca.crt`.
2. If macOS opens `Keychain Access`, add it to the `System` keychain.
3. Open `System` > `Certificates` and find `GoPro Downloader Runner Local CA`.
4. Double-click it.
5. Expand `Trust`.
6. Set `When using this certificate` to `Always Trust`.
7. Close the window and enter the Mac password when requested.
8. Completely quit Chrome with `Chrome` > `Quit Google Chrome`.
9. Reopen Chrome.

### Step 2B: Trust the Certificate on Windows

> **Screenshot placeholder: Trust the runner CA on Windows**  
> File: `screenshots/https-03-windows-certificate-store.png`  
> Show `Trusted Root Certification Authorities` selected in the Certificate Import Wizard.

1. Double-click `gopro-downloader-runner-ca.crt`.
2. Click `Install Certificate`.
3. Select `Local Machine` and approve the administrator prompt.
4. Select `Place all certificates in the following store`.
5. Choose `Trusted Root Certification Authorities`.
6. Finish the wizard and accept the security warning.
7. Completely close every Chrome window.
8. Reopen Chrome.

Install only a CA certificate generated by your own runner. Do not share the CA private key stored in `/data/certs`.

### Step 3: Test HTTPS

1. Return to the runner wizard.
2. Copy the HTTPS URL shown by the runner, for example:

   ```text
   https://192.168.1.50:32771
   ```

3. Open that URL directly in Chrome.
4. Confirm that the dashboard opens without a certificate warning.

If Chrome still shows a privacy warning, do not bypass it. Check that:

- The certificate was installed in the trusted root store.
- Chrome was fully restarted.
- The URL uses the same IP address that the runner detected when generating the certificate.
- The `/data/certs` folder is writable and persistent.

## Connect the Extension to a NAS Runner

> **Screenshot placeholder: Extension runner settings**  
> File: `screenshots/extension-01-aria2-runner-url.png`  
> Show `aria2 runner` selected, the HTTPS runner URL, and the `Open runner dashboard` button.

1. Sign in to [GoPro Media Library](https://gopro.com/media-library/) in Chrome.
2. Open the GoPro Downloader extension.
3. Set `Download method` to `aria2 runner`.
4. Enter the final HTTPS runner URL, including the port.
5. Click `Open runner dashboard`.
6. If Chrome requests permission for the runner address, approve it.
7. Return to the GoPro Media Library tab.
8. Open the extension again.
9. Click `Start / Retry all downloads`.

The extension first gathers or updates the GoPro Cloud inventory. The runner compares that inventory with local files and queues only media that is missing or incomplete.

Keep the GoPro Media Library tab open while the extension is gathering inventory and resolving fresh download URLs.

## Check Progress

> **Screenshot placeholder: Overall download progress**  
> File: `screenshots/dashboard-01-cloud-vs-local.png`  
> Show `Cloud Media vs Local Downloaded`, including complete, missing, partial, and need-download counts.

Open the runner dashboard to see:

- Cloud media count.
- Locally complete files.
- Missing or partial files.
- Files needing download or retry.
- Current and previous runs.
- Batch details and URL lookup failures.

A batch can finish while another batch is still being prepared. Use the overall **Cloud Media vs Local Downloaded** section to decide whether the complete library has finished.

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

1. Stop the runner container.
2. Download the newest `latest` image.
3. Recreate the container with the same port and `/data` host-folder mapping.
4. Start it and confirm the dashboard still shows the existing inventory.

### Synology

1. Record the existing port and `/data` storage mapping.
2. Download the newest `latest` image from `Registry`.
3. Stop the existing runner container.
4. Recreate it from the new image with the same port and read/write host folder mapped to `/data`.
5. Start it and confirm the dashboard still shows the existing inventory.

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
4. Open a [support issue](https://github.com/teze3808/gopro-downloader-support/issues).
5. Paste the summary and describe the platform, NAS model if applicable, runner URL with the IP redacted if preferred, and what happened.
6. If requested, use `Download diagnostic log` and review it before attaching.

Never post GoPro passwords, browser cookies, signed media URLs, CA private keys, or private filenames in a public issue.
