# Installing EA Delivery Hub in Docker inside WSL2

Guide to installing the app on a Windows 11 laptop. When you finish, the app is available at `http://localhost:8080`, reachable only from that machine, with its data in a Docker volume that survives restarts and updates.

| Step | Where | Approximate time |
|---|---|---|
| 1. WSL2 and Ubuntu | PowerShell (administrator) | 10 min + reboot |
| 2. Docker | Windows or Ubuntu | 10 min |
| 3. Copy the package | Ubuntu | 2 min |
| 4. Install | Ubuntu | 5 min the first time |

## 0. What you need

- Windows 11 (or Windows 10 22H2) with virtualization enabled in the BIOS/UEFI. Check it in *Task Manager → Performance → CPU → Virtualization: Enabled*.
- At least 8 GB of RAM and about 5 GB of free disk space.
- The installation package, generated on the source machine with `make bundle` (or `make bundle-image` if the target machine has no internet access):
  - `ea-delivery-hub-<version>.tar.gz`: the code.
  - `ea-delivery-hub-image-<version>.tar.gz`: the prebuilt image (optional).

## 1. WSL2 with Ubuntu

In a **PowerShell as administrator**:

```powershell
wsl --install -d Ubuntu-24.04
```

Reboot when prompted. When you come back, Ubuntu opens and asks for a Linux username and password (they do not have to match your Windows ones). Check that it is WSL **2**:

```powershell
wsl -l -v
```

If `Ubuntu-24.04` shows `VERSION 1`: `wsl --set-version Ubuntu-24.04 2`.

## 2. Docker

Choose **one** of the two options.

### Option A: Docker Desktop (recommended)

1. In PowerShell: `winget install -e --id Docker.DockerDesktop` (or download it from docker.com). First check whether your organization requires a Docker Desktop licence.
2. Open Docker Desktop and accept the terms.
3. *Settings → General*: tick **Use the WSL 2 based engine**.
4. *Settings → Resources → WSL integration*: turn on **Ubuntu-24.04** and click *Apply & restart*.

### Option B: Docker Engine inside Ubuntu (no Docker Desktop)

In the Ubuntu terminal:

```bash
curl -fsSL https://get.docker.com | sudo sh
sudo usermod -aG docker "$USER"
```

Close and reopen the Ubuntu terminal. If Docker does not start on its own: `sudo service docker start` (or enable systemd in `/etc/wsl.conf` with `[boot]` and `systemd=true`, then `wsl --shutdown`).

### Check (in Ubuntu, with either option)

```bash
docker version
docker compose version
docker run --rm hello-world
```

## 3. Copy the package to Ubuntu

The app must live on the Linux disk (`~`), not under `/mnt/c/...`: it is much faster there. From Ubuntu, assuming the package is in your Windows Downloads folder:

```bash
cd ~
tar xzf /mnt/c/Users/<your_windows_user>/Downloads/ea-delivery-hub-<version>.tar.gz
cd ~/ea-delivery-hub
```

If you use the git repository instead of the package: `git clone <url> ~/ea-delivery-hub`.

## 4. Install

```bash
bash scripts/install.sh --seed
```

The script:

1. Checks that you are in WSL2, that Docker responds and that the app is not under `/mnt/...`.
2. Creates `.env` from `.env.example` (if it already exists, it is kept).
3. Builds the production image and starts the container.
4. Waits for the healthcheck and checks `/health`.
5. With `--seed`, loads the 3 (fictitious) demo programs. Without `--seed`, the app starts empty and programs are created from *Administration*.

Options:

| Option | Purpose |
|---|---|
| `--seed` | Load the demo programs |
| `--port 8090` | Use another port if 8080 is taken (saved as `EA_PORT` in `.env`) |
| `--image /path/ea-delivery-hub-image-<version>.tar.gz` | Install without internet using the prebuilt image |

Open **http://localhost:8080** in the Windows browser (Edge, Chrome…).

Equivalent shortcuts with `make` (if installed: `sudo apt install make`): `make install`, `make install-demo`. `make` is optional: the scripts and the `docker compose` commands shown in this guide work the same without it, including on Windows machines that do not have `make` (run them from the Ubuntu terminal).

## 5. Daily use

| What | Command (from `~/ea-delivery-hub`) |
|---|---|
| Stop | `docker compose -f docker-compose.yml -f docker-compose.prod.yml down` |
| Start | `docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d` |
| View logs | `docker compose -f docker-compose.yml -f docker-compose.prod.yml logs -f app` |
| Status | `curl http://localhost:8080/health` |

Note: the `make up`, `make down` and `make logs` shortcuts use the development configuration (they build the dev image), not the production one installed by `install.sh`. For this installation use the commands above (or `make install` to update).

The container has `restart: unless-stopped`: it starts by itself when Docker Desktop opens (or Docker Engine starts) unless you stopped it by hand.

Templates are downloaded with the **Export** button on each screen, straight into the Windows browser.

## 6. Backups

The data (the SQLite database, uploaded diagrams and branding texts changed from *Administration*) lives in the volume `ea-delivery-hub_ea_data`.

```bash
bash scripts/backup.sh                                          # creates backups/ea_data_YYYYMMDD_HHMMSS.tar.gz
bash scripts/restore.sh backups/ea_data_20261017_101500.tar.gz  # replaces all data (asks for confirmation)
```

(`make backup` and `make restore FILE=backups/ea_data_....tar.gz` do the same.)

The backup stops the app for a few seconds so that it is consistent, and starts it again afterwards. Copy the `backups/` folder off the machine (from Windows Explorer: `\\wsl$\Ubuntu-24.04\home\<user>\ea-delivery-hub\backups`).

To move **a single program** to another installation: use the *JSON* link in the program's row under *Administration → Programs* on the source, and *Import a program* on the destination. Uploaded diagram files are not included in that JSON; use the full backup for those.

## 7. Updating to a new version

```bash
bash scripts/backup.sh
cd ~ && tar xzf /mnt/c/Users/<your_windows_user>/Downloads/ea-delivery-hub-<new_version>.tar.gz
cd ~/ea-delivery-hub && bash scripts/install.sh
```

Extracting over the existing folder replaces the code and keeps `.env`, `backups/` and the data. Database migrations are applied automatically on startup. Do not use `--seed` when updating if you do not want to reset the demo programs (programs you created are not touched).

## 8. Corporate proxy

If the network uses a proxy, edit `.env` before installing:

```bash
HTTP_PROXY=http://proxy.example:8080
HTTPS_PROXY=http://proxy.example:8080
NO_PROXY=localhost,127.0.0.1,db
```

If the proxy inspects TLS, copy the corporate root certificate (`*.crt`, PEM format) into `docker/certs/` before running `install.sh`; it is installed inside the image. If the build still cannot download anything, install with the prebuilt image (`make bundle-image` on a machine with internet and `--image` on this one).

## 9. Uninstall

```bash
docker compose -f docker-compose.yml -f docker-compose.prod.yml down      # keeps the data
docker compose -f docker-compose.yml -f docker-compose.prod.yml down -v   # ALSO DELETES the data
docker image rm ea-delivery-hub:prod
rm -rf ~/ea-delivery-hub
```

## 10. Common problems

| Symptom | Cause and fix |
|---|---|
| `docker: command not found` in Ubuntu | Docker Desktop without WSL integration for that distro (step 2A.4), or Docker Engine not installed. |
| `permission denied ... docker.sock` | The user is not in the `docker` group: `sudo usermod -aG docker $USER` and reopen the terminal. |
| `/usr/bin/env: 'bash\r'` or odd errors in the scripts | Files were copied with Windows line endings. Extract the package from Ubuntu (step 3), not from Explorer. |
| `port is already allocated` | Port 8080 is taken: `bash scripts/install.sh --port 8090`. |
| The page does not open in Windows but `/health` responds in Ubuntu | Restart WSL with `wsl --shutdown` in PowerShell and start Docker again. |
| The build fails downloading packages | Corporate proxy or certificate (step 8), or install with `--image`. |
| The app is slow | It is under `/mnt/c/...`: move it to `~` (step 3). |
