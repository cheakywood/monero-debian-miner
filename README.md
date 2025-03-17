# Monero Mining on Debian (Asus X93S Notebook)

This guide explains how to mine Monero ($XMR) on an **Asus X93S** laptop while optimizing power consumption for efficiency.

## Hardware Specifications
- **Model:** Asus X93S
- **Motherboard:** K93SV
- **CPU:** Intel Core i5-2430M (2.4 GHz, 35W TDP)
- **GPU:** Nvidia GeForce GT 540M (1GB, 35W TDP)
- **Memory:** 4GB RAM
- **Storage:** 1TB HDD

## Estimated Power Consumption
| Component            | Power Consumption (W) |
|---------------------|---------------------|
| CPU (i5-2430M)      | ~35W                |
| GPU (GT 540M)       | ~35W                |
| Other Components    | ~10-15W             |
| **Total**           | **70–90W**          |

## Installing Debian for Mining
### 1. Install Debian (Minimal Setup)
1. Download **Debian Net Install**.
2. Create a bootable USB:
   ```sh
   sudo dd if=debian-netinst.iso of=/dev/sdX bs=4M status=progress
   ```
3. Boot from USB and select **"Install"**.
4. Choose minimal installation options:
   - **Basic system utilities** ✅
   - **No Desktop Environment** ❌
   - **SSH Server** ✅
   - **Standard system utilities** ✅

### 2. Set Up SSH for Remote Access
1. Install and enable SSH:
   ```sh
   sudo apt update && sudo apt install openssh-server
   sudo systemctl enable ssh --now
   ```
2. Find your IP:
   ```sh
   hostname
   ```
3. Connect remotely:
   ```sh
   ssh username@ip_address
   ```

## Reducing Power Consumption
To minimize electricity costs, disable or remove unnecessary components.

### 1. Disable the Discrete GPU (GT 540M) by blacklisting Nvidia Kernel Modules
You can blacklist the Nvidia kernel modules to prevent the GPU from being loaded at boot.

#### Step 1: Edit the Blacklist Configuration
Open the blacklist configuration file in a text editor:
```sh
sudo nano /etc/modprobe.d/blacklist.conf
```

#### Step 2: Add the Following Lines
Append these lines at the end of the file:
```sh
blacklist nvidia
blacklist nouveau
```

#### Step 3: Update Initramfs
After modifying the blacklist file, update the **initramfs**:
```sh
sudo update-initramfs -u
```

#### Step 4: Reboot the System
Reboot for the changes to take effect:
```sh
sudo reboot
```
✅ This will prevent the Nvidia drivers from loading, effectively disabling the discrete GPU.


✅ **Power Savings:** Up to **35W**

### 2. Optimize Hardware for Efficiency
- **Remove or replace HDD** (saves **6–8W**): Use a USB flash drive or SSD.
- **Remove the optical drive** (saves **5W**).
- **Reduce RAM** (saves **3–5W**, but may impact performance).
- **Disable the screen**:
  ```
   sudo sh -c "echo 1 > /sys/class/graphics/fb0/blank"
  ```

  ✅ Saves **5–10W**.
- **Remove the battery** if running on AC power (saves **2–5W**).

## Final Power Savings Estimation
| Component          | Default Power (W) | Optimized Power (W) | Savings (W) |
|-------------------|-----------------|-----------------|------------|
| CPU (Untouched)   | 35W             | 35W             | 0W        |
| GPU (Disabled)   | 35W             | 0W              | 35W        |
| HDD (Removed)    | 8W              | 0W              | 8W         |
| DVD Drive (Removed) | 5W              | 0W              | 5W         |
| RAM (Untouched)    | 5W              | 5W              | 0W         |
| Screen (Disabled)  | 10W             | 0W              | 10W         |
| Battery Removed  | 5W              | 0W              | 5W         |
| **Total Savings** | **100W**         | **45W**         | **65W**    |

## Prevent Laptop Shutdown or Sleep When Closing Lid (Debian, No GUI)

If you want to prevent your Debian laptop (without a GUI) from shutting down or sleeping when you close the lid, follow these steps:

## 1. Edit the `logind.conf` file
Open a terminal and edit the systemd logind configuration file:

```bash
sudo nano /etc/systemd/logind.conf
```

## 2. Modify the Lid Switch Behavior
Find these lines and modify them (or add them if they don’t exist):

```ini
HandleLidSwitch=ignore
HandleLidSwitchDocked=ignore
HandleLidSwitchExternalPower=ignore
```

## 3. Save and Exit
- Press `CTRL + X`, then `Y`, and hit `Enter` to save the changes.

## 4. Restart the `systemd-logind` Service
Apply the changes by restarting the service:

```bash
sudo systemctl restart systemd-logind
```

## Mining Monero with XMRig
### 1. Install XMRig
```sh
sudo apt install git build-essential cmake libuv1-dev libssl-dev libhwloc-dev
```
Clone and build XMRig:
```sh
git clone https://github.com/xmrig/xmrig.git
cd xmrig
mkdir build && cd build
cmake ..
make -j$(nproc)
```

### 2. Configure XMRig
#### Step 1: Obtain Your Monero Wallet Address
Ensure you have a Monero wallet. If not, create one using the one [Monero Wallet](https://www.getmonero.org/downloads/).

#### Step 2: Select a Mining Pool and Configure XMRig
Find a mining pool: [Mining Pool Stats](https://miningpoolstats.stream/monero)  
Example: [hashvault.pro](https://monero.hashvault.pro/en/getting-started)

Follow the instructions from the pool to generate a mining configuration and update your `config.json`.

#### Create `config.json`
```sh
cd ~/xmrig/
nano config.json
```

Example `config.json`:
```json
{
  "api": {
    "id": null,
    "worker-id": null
  },
  "http": {
    "enabled": false,
    "host": "127.0.0.1",
    "port": 0,
    "access-token": null,
    "restricted": true
  },
  "autosave": true,
  "version": 1,
  "background": false,
  "colors": true,
  "randomx": {
    "init": -1,
    "numa": true
  },
  "cpu": {
    "enabled": true,
    "huge-pages": true,
    "hw-aes": null,
    "priority": null,
    "memory-pool": false,
    "max-threads-hint": 100,
    "asm": true,
    "argon2-impl": null,
    "cn/0": false,
    "cn-lite/0": false
  },
  "opencl": {
    "enabled": false,
    "cache": true,
    "loader": null,
    "platform": "AMD",
    "cn/0": false,
    "cn-lite/0": false
  },
  "cuda": {
    "enabled": false,
    "loader": null,
    "nvml": true,
    "cn/0": false,
    "cn-lite/0": false
  },
  "donate-level": 1,
  "donate-over-proxy": 1,
  "log-file": null,
  "pools": [
    {
      "algo": null,
      "coin": null,
      "url": "pool.hashvault.pro:443",
      "user": <WALLET_ADDRESS>,
      "pass": <WORKER>,
      "rig-id": null,
      "nicehash": false,
      "keepalive": false,
      "enabled": true,
      "tls": true,
      "tls-fingerprint": "420c7850e09b7c0bdcf748a7da9eb3647daf8515718f36d9ccfdd6b9ff834b14",
      "daemon": false,
      "self-select": null
    }
  ],
  "print-time": 60,
  "health-print-time": 60,
  "retries": 5,
  "retry-pause": 5,
  "syslog": false,
  "user-agent": null,
  "watch": true
}
```
Replace `<WALLET_ADDRESS>` with your actual wallet address and `<WORKER>` with your worker name.

### 3. Start Mining
#### Use Screen (Recommended for Remote Mining)
Install screen:
```sh
sudo apt update && sudo apt install screen -y
```
Start a screen session:
```sh
screen -S mining
```
Run XMRig:
```sh
cd ~/xmrig/build
./xmrig --config ../config.json
```
Detach the session:
```sh
CTRL + A, then D
```
Reopen the session:
```sh
screen -r mining
```
XMRig will display stats about your mining performance, including hash rates and accepted shares.

Check your mining pool's dashboard to track earnings via https://monero.hashvault.pro/en/dashboard



