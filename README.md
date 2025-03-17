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

## Reducing Power Consumption
To minimize electricity costs, disable or remove unnecessary components.

### 1. Disable the Discrete GPU (GT 540M)
#### Method 1: Using Nvidia Prime
1. Check for Nvidia drivers:
   ```sh
   nvidia-smi
   ```
2. Install Nvidia Prime if not installed:
   ```sh
   sudo apt update
   sudo apt install nvidia-driver nvidia-prime
   ```
3. Switch to Intel integrated graphics:
   ```sh
   sudo prime-select intel
   sudo reboot
   ```

#### Method 2: Using bbswitch
1. Install bbswitch:
   ```sh
   sudo apt update
   sudo apt install bbswitch-dkms
   ```
2. Disable the discrete GPU:
   ```sh
   sudo tee /proc/acpi/bbswitch <<< OFF
   ```
3. Verify GPU is off:
   ```sh
   cat /proc/acpi/bbswitch
   ```
4. Reboot to apply changes:
   ```sh
   sudo reboot
   ```
✅ **Power Savings:** Up to **35W**

### 2. Optimize Hardware for Efficiency
- **Remove or replace HDD** (saves **6–8W**): Use a USB flash drive or SSD.
- **Remove the optical drive** (saves **5W**).
- **Reduce RAM** (saves **3–5W**, but may impact performance).
- **Dim or disable the screen**:
  ```sh
  xrandr --output LVDS-1 --brightness 0.1
  ```
  ✅ Saves **5–10W**.
- **Remove the battery** if running on AC power (saves **2–5W**).

### 3. Optimize Power Settings
- **Lower CPU frequency:**
  ```sh
  sudo apt install cpufrequtils
  sudo cpufreq-set -g powersave
  ```
  ✅ Saves **10–15W**.

### Final Power Savings Estimation
| Component          | Default Power (W) | Optimized Power (W) | Savings (W) |
|-------------------|-----------------|-----------------|------------|
| CPU (Tuned)      | 35W             | 20W             | 15W        |
| GPU (Disabled)   | 35W             | 0W              | 35W        |
| HDD (Removed)    | 8W              | 0W              | 8W         |
| DVD Drive        | 5W              | 0W              | 5W         |
| RAM (Reduced)    | 5W              | 2W              | 3W         |
| Screen (Dimmed)  | 10W             | 2W              | 8W         |
| Battery Removed  | 5W              | 0W              | 5W         |
| **Total Savings** | **70W**         | **25W**         | **45W**    |

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
   ip a
   ```
3. Connect remotely:
   ```sh
   ssh username@192.168.1.100
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
1. Create a config file:
   ```sh
   nano config.json
   ```
2. Add the following configuration:
   ```json
   {
       "autosave": true,
       "cpu": true,
       "donate-level": 1,
       "randomx": { "1gb-pages": true },
       "pools": [
           {
               "url": "pool.minexmr.com:443",
               "user": "YOUR_WALLET_ADDRESS",
               "pass": "x",
               "keepalive": true,
               "tls": true
           }
       ]
   }
   ```
   ✅ Replace **YOUR_WALLET_ADDRESS** with your Monero wallet address.

### 3. Start Mining
```sh
./xmrig
```
✅ You should see hash rate, accepted shares, and pool connection status.

### 4. Optimize Performance & Power Usage
- Limit CPU threads:
  ```sh
  ./xmrig --cpu-max-threads-hint=50
  ```
- Run XMRig in the background:
  ```sh
  nohup ./xmrig > xmrig.log 2>&1 &
  ```
- Monitor system usage:
  ```sh
  htop
  ```

## Final Notes
✅ Your laptop is now optimized for low-power **Monero mining**.
✅ Monitor your earnings on the mining pool's website.
✅ Keep temperatures low with proper cooling.
✅ Automate mining on startup for continuous operation.

💰 **Happy Mining!**

