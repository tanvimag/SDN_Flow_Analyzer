# 🔗 SDN Project: Link Failure Detection and Recovery using Ryu Controller

## Project Overview

This project demonstrates how a Software Defined Network (SDN) behaves under:

- Normal conditions  
- Link failure  
- Link recovery  

The controller dynamically installs flow rules using OpenFlow (Ryu), and behavior is verified using:

- Mininet (connectivity)  
- Flow tables (ovs-ofctl)  
- Wireshark (packet-level analysis)  
- iperf (throughput analysis)  

---

## 🌐 Network Topology
    h1s1        h1s2
      |            |
      s1 -------- s2
      |            |
    h2s1        h2s2


- 2 switches: s1, s2  
- 4 hosts: h1s1, h2s1 (left), h1s2, h2s2 (right)  
- Link between s1 and s2 is the failure point  

---

## ⚙️ REQUIREMENTS

- Ubuntu (20.04 / 22.04)  
- Mininet  
- Docker  
- Ryu Controller  
- Wireshark  

---

## 🚀 COMPLETE EXECUTION GUIDE (STRICT ORDER)

### STEP 1 — OPEN TERMINAL 1 (Controller)

```bash
sudo mn -c
sudo docker rm -f $(docker ps -aq) 2>/dev/null

cd ~/SDN_CN_PROJECT/controller

Start Controller:

sudo docker run -it --rm --network host \
-v $(pwd):/app \
-w /app \
-e PYTHONPATH=/app \
osrg/ryu \
ryu-manager flowanalyser.py

Expected:

CONTROLLER READY

Keep this terminal running

###STEP 2 — OPEN TERMINAL 2 (Mininet)

sudo mn --topo linear,2,2 --controller=remote,ip=127.0.0.1,port=6653

Expected:

mininet>

###🧪 TESTING PHASE
TEST 1 — NORMAL CONNECTIVITY
pingall

Expected:

*** Results: 0% dropped
TEST 2 — LINK FAILURE
py net.configLinkStatus('s1', 's2', 'down')
pingall

Expected:

*** Results: ~66% dropped

TEST 3 — LINK RECOVERY
py net.configLinkStatus('s1', 's2', 'up')
h1s1 ping -c 5 h1s2
pingall

Expected:

*** Results: 0% dropped

###🔍 FLOW TABLE VERIFICATION
sh ovs-ofctl dump-flows s1
sh ovs-ofctl dump-flows s2

Expected:

priority=1,in_port=...,dl_src=...,dl_dst=... actions=output:...
priority=0 actions=CONTROLLER:65535

✔ Confirms dynamic flow installation

###📡 WIRESHARK ANALYSIS

Open Wireshark:

sudo wireshark

Select:

Loopback: lo

Start Capture → Generate traffic:

h1s1 ping -c 5 h1s2

Apply filter:

openflow

Expected:

OFPT_PACKET_IN
OFPT_PACKET_OUT
OFPT_ECHO_REQUEST / REPLY

Note:
FLOW_MOD may not appear due to fast execution, but is verified using flow tables.

###📈 THROUGHPUT ANALYSIS (IPERF)
NORMAL CONDITION
h1s1 iperf -s &
h1s2 iperf -c h1s1

Expected:

~13 Gbits/sec bandwidth

✔ High throughput → network working efficiently

LINK FAILURE
py net.configLinkStatus('s1', 's2', 'down')
h1s2 iperf -c h1s1

Expected:

tcp connect failed (No route to host)

✔ Confirms network is broken

RECOVERY
py net.configLinkStatus('s1', 's2', 'up')
h1s2 iperf -c h1s1

Expected:

~13 Gbits/sec bandwidth restored

✔ Confirms recovery

###📊 PERFORMANCE SUMMARY
Scenario	Packet Loss	Throughput
Normal	0%	~13 Gbps
Link Failure	High	Connection fails
Recovery	0%	~13 Gbps

###🎯 KEY CONCEPT
Controller does NOT directly detect failure
Failure → packet drops
New packets → trigger packet_in
Controller → installs new flows

👉 This is Reactive SDN

###✅ VALIDATION

The system was validated using:

ping → connectivity
ovs-ofctl → flow rules
Wireshark → OpenFlow packets
iperf → throughput


Include screenshots of:

flow tables
Wireshark packets
iperf (normal, failure, recovery)

🧾 CONCLUSION

This project demonstrates:

Dynamic SDN flow rule installation
Network behavior under failure and recovery
Performance validation using throughput


👤 AUTHOR
Tanvi Magalur


---

# ✅ After this

Run:

```bash
git add README.md
git commit -m "Updated README"
git push
🎯 Result

Your repo will now:

look exactly like your friend’s
be clean + professional
be ready for submission
