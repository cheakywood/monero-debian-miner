# Monero Mining on Debian (Using an Asus X93S Notebook)

This guide details how to mine Monero ($XMR) on an old Asus X93S laptop while optimizing power consumption for efficiency.

## **Hardware Specifications**
- **Model**: Asus X93S
- **Motherboard**: K93SV
- **CPU**: Intel Core i5-2430M, 2.4 GHz
- **GPU**: Nvidia GeForce GT 540M CUDA 1GB
- **Memory**: 4GB RAM
- **Storage**: 1TB HDD

## **Estimated Power Consumption**
- **CPU (i5-2430M)**: ~35W TDP
- **GPU (GT 540M)**: ~35W TDP
- **Other Components (RAM, Motherboard, HDD)**: ~10-15W
- **Total Estimated Power Consumption**: ~70–90W while mining

## **Reducing Power Consumption**
To minimize electricity costs, disable or remove unnecessary components.

### **1. Disable or Remove Unnecessary Hardware**
#### **a) Disable Discrete GPU (GT 540M)**
The Nvidia GT 540M consumes ~35W and is unnecessary for CPU mining.
- Enter BIOS (press **F2** during startup).
- Look for **"Integrated Graphics"** or **"Disable Discrete GPU"** settings.
- Disable **Nvidia Optimus** or **Hybrid Graphics** (if available).
- Save & Exit.
✅ **Effect**: Saves **up to 35W** of power.

#### **b) Replace or Remove the Hard Drive (HDD)**
- A 1TB HDD consumes **6–10W**.
- Replace it with a **USB flash drive (2–4W)** or **small SSD (1–2W)**.
- If running a minimal Linux distro from a USB stick, remove the HDD:
  - Power off the laptop.
  - Open the HDD compartment.
  - Disconnect and remove the HDD.
✅ **Effect**: Saves **up to 8W**.

#### **c) Remove or Disable the Optical Drive (DVD Drive)**
- DVD drives consume **5–10W**, even when idle.
- Remove it by unscrewing and sliding out the drive.
✅ **Effect**: Saves **up to 5W**.

#### **d) Reduce RAM Usage**
- Each RAM stick consumes **3–5W**.
- If possible, run on a **single 2GB stick**.
✅ **Effect**: Saves **3–5W**, but may impact mining performance.

### **2. Optimize Power Settings**
#### **a) Lower CPU Power Usage**
Limit CPU frequency to reduce power draw:
```sh
sudo apt install cpufrequtils
sudo cpufreq-set -g powersave
```
✅ **Effect**: Saves **10–15W**.

#### **b) Lower Screen Brightness or Disable Screen**
Reduce power usage by dimming the screen or running headless:
```sh
xrandr --output LVDS-1 --brightness 0.1
```
✅ **Effect**: Saves **5–10W**.

#### **c) Remove the Battery (If Running on AC Power)**
Batteries consume power even when full.
✅ **Effect**: Saves **2–5W**.

### **Final Power Savings Estimation**
| Component            | Default Power (W) | Optimized Power (W) | Savings (W) |
|---------------------|-----------------|-----------------|------------|
| CPU (Tuned)        | 35W             | 20W             | 15W        |
| GPU (Disabled)     | 35W             | 0W              | 35W        |
| HDD (Removed)      | 8W              | 0W              | 8W         |
| DVD Drive (Removed)| 5W              | 0W              | 5W         |
| RAM (Reduced)      | 5W              | 2W              | 3W         |
| Screen (Dimmed)    | 10W             | 2W              | 8W         |
| Battery (Removed)  | 5W              | 0W              | 5W         |
| **Total Savings**  | **70W**         | **25W**         | **45W**    |

---

## **Installing Debian for Mining**
### **1. Prepare the Laptop**
Before installing Linux, remove or disable the unnecessary components listed above.

### **2. Install Debian (Minimal Setup)**
- Download **Debian Net Install**.
- Create a bootable USB using Rufus (Windows) or `dd` (Linux/Mac):
```sh
sudo dd if=debian-netinst.iso of=/dev/sdX bs=4M status=progress
```
- Boot from USB (press **F2** or **F12** at startup).
- Select **"Install"** (no GUI).
- Choose:
  - **"Basic system utilities"** ✅
  - **Do NOT install a Desktop Environment** ❌
  - **"SSH Server"** ✅
  - **"Standard system utilities"** ✅

### **3. Set Up SSH for Remote Access**
After installation, enable SSH:
```sh
sudo apt update && sudo apt install openssh-server
sudo systemctl enable ssh --now
```
Find your IP address:
```sh
ip a
```
Connect via SSH from another device:
```sh
ssh username@192.168.1.100
```

---

## **Mining Monero with XMRig**
### **1. Install XMRig**
```sh
sudo apt install git build-essential cmake libuv1-dev libssl-dev libhwloc-dev
```
Clone and compile XMRig:
```sh
git clone https://github.com/xmrig/xmrig.git
cd xmrig
mkdir build && cd build
cmake ..
make -j$(nproc)
```

### **2. Configure XMRig**
Create a config file:
```sh
nano config.json
```
Paste the following:
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
Replace **YOUR_WALLET_ADDRESS** with your Monero wallet address.

### **3. Start Mining**
```sh
./xmrig
```
✅ **You should now see hash rate, accepted shares, and pool connection status.**

### **4. Optimize Performance & Power Usage**
Limit CPU usage:
```sh
./xmrig --cpu-max-threads-hint=50
```
Run XMRig in the background:
```sh
nohup ./xmrig > xmrig.log 2>&1 &
```
Monitor CPU usage:
```sh
htop
```

---

## **Final Notes**
✅ Your laptop is now optimized for low-power **Monero mining**.
✅ Check your earnings on your mining pool’s website.
✅ Keep temperatures low (use a cooling pad if needed).
✅ Automate mining startup on boot for continuous operation.

💰 **Happy Mining!**

