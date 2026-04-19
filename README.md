# SDN Project: Link Failure Detection and Recovery using Ryu Controller

## 📌 Project Overview
This project demonstrates how a Software Defined Network (SDN) behaves under different conditions using a Ryu Controller.

The network is tested under:
- Normal operation
- Link failure
- Link recovery

The controller dynamically installs flow rules using OpenFlow.

---

## 🛠 Tools & Technologies
- Mininet
- Ryu Controller
- Wireshark
- iperf

---

## 🌐 Network Topology

Linear topology with 3 switches and 3 hosts:

h1 — s1 — s2 — s3 — h3

Each switch is connected to one host.

---

## ⚙️ Requirements
- Ubuntu (20.04 / 22.04)
- Mininet
- Docker
- Ryu Controller
- Wireshark

---

## 🚀 Execution Steps

### Step 1: Start Controller
```bash
cd ~/SDN_CN_PROJECT/controller
sudo docker run -it --rm --network host \
-v $(pwd):/app \
-w /app \
-e PYTHONPATH=/app \
osrg/ryu \
ryu-manager flowanalyser.py
