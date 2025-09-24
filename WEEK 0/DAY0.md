# RISC-V-Chip-Tapeout-VSD
# Day 0 # Task 0

 **Journey Begins**  
Documenting my progress in the **India RISC-V SoC Tapeout Program**—a nation-wide initiative to design, verify, and tape-out open-source silicon.

---

##  Video Summary
The kickoff video introduced:
- **Program Overview:** 20-week path from RTL design to silicon tapeout.
- **Objectives:** Build India’s largest online academic tapeout initiative; nurture semiconductor talent using RISC-V and open-source tools.
- **Key Partners:** IIT Gandhinagar, VLSI System Design (VSD), Semi-Conductor Laboratory (SCL), and industry collaborators.
- **Expected Outcome:** Hands-on chip-design expertise and a collaborative open-source ecosystem.

---

##  Tool Installation

### System Requirements
| Resource | Minimum |
|----------|--------|
| RAM      | 6 GB |
| Storage  | 50 GB HDD |
| CPU      | 4 vCPU |
| OS       | Ubuntu 20.04+ |

### 1️⃣ Oracle VirtualBox
Download & install: [https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads)

---

### 2️⃣ Yosys
```bash
sudo apt-get update
git clone https://github.com/YosysHQ/yosys.git
cd yosys
sudo apt install make build-essential clang bison flex \
 libreadline-dev gawk tcl-dev libffi-dev git \
 graphviz xdot pkg-config python3 libboost-system-dev \
 libboost-python-dev libboost-filesystem-dev zlib1g-dev
make config-gcc
make
sudo make install
```
<img width="875" height="617" alt="Screenshot 2025-09-20 171445" src="https://github.com/user-attachments/assets/29b6dc99-8f68-44c1-b212-ddaad6851b82" />

### 3️⃣ Icarus Verilog
```bash
sudo apt-get update
sudo apt-get install iverilog
```
<img width="792" height="219" alt="Screenshot 2025-09-20 165356" src="https://github.com/user-attachments/assets/ea150571-dc64-44f5-8d47-01c2588cfd7d" />

### 4️⃣ GTKWave
```bash
sudo apt-get update
sudo apt-get install gtkwave
```
<img width="1919" height="1024" alt="Screenshot 2025-09-20 165320" src="https://github.com/user-attachments/assets/2f939f0c-3a44-425a-96d7-674ef56d1620" />

