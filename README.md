# Port Prowler

## Overview

Port Prowler is a network scanning tool designed to identify open, closed, and filtered ports on a target system. It supports various scanning techniques—including TCP, UDP, and stealth (SYN) scanning—and can identify common services by port number. Port Prowler is ideal for network administrators, security professionals, and students who want to analyze network security, validate firewall rules, or compare results with Nmap. The tool also supports customizable timeouts for handling different network conditions and can save results to a file for later analysis.


---

## Features
- Scan single, multiple, or ranges of ports
- TCP, UDP, and stealth (SYN) scanning
- Multi-threaded scanning for TCP, UDP, and stealth scans for much faster results
- Customizable timeout for handling different network conditions (e.g., high latency, packet loss)
- Identifies common services (e.g., HTTP, FTP, SSH) by port number in scan results
- Save results to a file (`scan_results1.txt`, `scan_results2.txt`, etc.)
- Output format matches Nmap for easy validation
- Clear error handling and usage instructions
- Handles closed and filtered ports, even during parallel scanning
- Supports scanning with multiple protocols simultaneously (e.g., TCP and UDP in one run)
---

## Diagram of the workflow of this tool
```
User Command
    |
    v
[port_prowler.py: main()]
    |
    +--> Parse args & ports (parse_ports)
    |
    +--> For each scan type requested:
    |       |
    |       +--> scan_ports_concurrently()
    |               |
    |               +--> [ThreadPoolExecutor]
    |                       |
    |                       +--> scan_tcp / scan_udp / scan_stealth (one port per thread)
    |                               |
    |                               +--> Return status for each port and protocol
    |
    +--> Collect results as {(port, proto): status}
    |
    +--> For each result:
    |       |
    |       +--> get_service_name(port, proto)  # Uses correct protocol for each scan
    |       +--> Format output line with port, service name, and status
    |       +--> Print output line to terminal
    |
    +--> If -f is specified:
    |       |
    |       +--> Save all output lines to file (auto-increment filename if needed)
    |
    +--> Print confirmation if results saved to file
```
1. **User runs a command:**  
   Example:  
   `python port_prowler.py 192.168.1.115 -p 21,22,80-85 -tcp -udp --timeout 2`

2. **Main Script (port_prowler.py) Starts**
   - Parses command-line arguments with `argparse`
   - Checks which scan types are requested (`-tcp`, `-udp`, `-s`)
   - Parses port input using `parse_ports` from utils.py
   - Sets up an empty results dictionary

3. **For Each Scan Type Requested:**
   - **TCP Scan:**  
     Calls `scan_ports_concurrently(scan_tcp, ip, ports)`  
     Stores results as `{(port, 'tcp'): status}`
   - **UDP Scan:**  
     Calls `scan_ports_concurrently(scan_udp, ip, ports)`  
     Stores results as `{(port, 'udp'): status}`
   - **Stealth (SYN) Scan:**  
     Calls `scan_ports_concurrently(stealth_wrapper, ip, ports)`  
     Stores results as `{(port, 'syn'): status}`

4. **`scan_ports_concurrently` (in scanner.py):**
   - Uses `ThreadPoolExecutor` to start up to 50 threads
   - Each thread runs the chosen scan function on a different port

5. **Scan Functions (in scanner.py):**
   - **`scan_tcp`:** Tries to connect to the port (TCP handshake)
   - **`scan_udp`:** Sends a UDP packet and waits for a response or ICMP error
   - **`scan_stealth`:** Sends a SYN packet using Scapy and analyzes the response

6. **Each Scan Function Returns a Status:**
   - "Open", "Closed", "Filtered", "Open|Filtered", or "Error"

7. **Results Collected:**
   - All thread results are gathered into the main results dictionary as `{(port, proto): status}`

8. **Output Handling:**
   - For each result, looks up the correct service name using `get_service_name(port, proto)` (uses the correct protocol for each scan)
   - Formats and prints each result line to the terminal
   - If `-f` is specified, saves all output lines to a file (auto-incrementing filename if needed)
   - Prints a confirmation message if results are saved to a file

## Installation

1. **Clone the repository and install dependencies:**
    ```bash
    git clone https://gitea.koodsisu.fi/lauralevisto/port-prowler.git
    cd port-prowler
    pip install -r requirements.txt
    ```

### Dependencies

This project primarily uses standard Python libraries for portability.  
However, for enhanced functionality, you may need to install:

- **Scapy**
required for stealth (SYN) scans. Scapy allows Port Prowler to craft and send raw TCP packets (like SYN packets) and analyze the responses, which is necessary for implementing SYN (stealth/half-open) scanning. This type of scan cannot be done with standard Python sockets.    

  **For your virtual environment (recommended for Python projects):**   
    This installs Scapy only for your current user or virtual environment.        
  ```bash
  pip install scapy
  ```

  **System-wide (needed for `sudo python3 ...` commands):**   
    This installs Scapy for all users, including root.      
  **Use this if you run your script with `sudo` and not from a virtual environment.**   
  ```bash
  sudo pip3 install scapy
  ```


- **Nmap**
not required for Port Prowler to run, but is recommended for comparison and validation. Nmap is the industry-standard port scanner, so you can use it to verify that Port Prowler’s results are accurate by running the same scans with both tools and comparing the outputs.  
  Install with:
  ```bash
  sudo apt install nmap
  ```
- **Argparse** is included in the Python standard library (no extra installation needed).

---

## Setting Up the Test Environment

1. **Download and install VirtualBox:**  
   [VirtualBox Linux Downloads](https://www.virtualbox.org/wiki/Linux_Downloads)

2. **Download Metasploitable 2:**  
   [Metasploitable 2 Download (SourceForge)](https://sourceforge.net/projects/metasploitable/)

3. **Start the VM:**
   - Boot the Metasploitable 2 VM in VirtualBox.
   - Log in with:
     - **Username:** `msfadmin`
     - **Password:** `msfadmin`

---


## Usage
To run the Port Prowler tool, use the following command structure:

```bash
python port_prowler.py <ip_address> -p <port_list> [options]
```

### Options
- `-p`, `--ports`: Specify ports to scan (e.g., `80`, `80,443`, `20-25`).
- `-tcp`: Enable TCP scanning (multi-threaded).
- `-udp`: Enable UDP scanning (multi-threaded).
- `-s`, `--stealth`: Enable stealth scanning (multi-threaded).
- `-f`, `--file`: Save output to a specified file (auto-increments filename if file exists).
- `--timeout`: Set the timeout (in seconds) for each port scan (default: 1 second).



## STEP BY STEP GUIDE

## 1. Start Your Test Environment

### a. Start VirtualBox
```bash
virtualbox
```

### b. Launch Metasploitable 2 VM

- Start the Metasploitable 2 VM from VirtualBox.

### c. Find the VM's IP Address
Inside the Metasploitable 2 terminal:
```bash
ifconfig
```

> Replace `192.168.1.115` with your Metasploitable 2 VM's actual IP address as needed.

## 2. Prepare Your Host Environment

### Activate Python Virtual Environment
```bash
cd /path/to/port-prowler
source venv/bin/activate
```

## 3. Run and Test All Required Cases

**Important:** Run all commands from your host machine (not inside the VM).

**Example results:** All example results are listed in `results.md`.

---

### A. Show Help and Usage

```bash
python port_prowler.py --help
```
---

### B. Test Port Input Handling

#### Single Port
```bash
python port_prowler.py 192.168.1.115 -p 80 -tcp
```
#### Multiple Ports
```bash
python port_prowler.py 192.168.1.115 -p 21,22,80 -tcp
```
#### Port Range
```bash
python port_prowler.py 192.168.1.115 -p 20-25 -tcp
```
---

### C. TCP Connect Scan (`-tcp`)

```bash
python port_prowler.py 192.168.1.115 -p 80,443,8080 -tcp
```
- **Explanation:** TCP connect scan uses the full TCP handshake (SYN, SYN-ACK, ACK) to check if a port is open.

---

### D. UDP Scan (`-udp`)

```bash
sudo ./venv/bin/python port_prowler.py 192.168.1.115  -p 53,69,161 -udp
```
- **Explanation:** UDP scan sends UDP packets and checks for responses or ICMP errors.

---

### E. Stealth (SYN) Scan (`-s`)

```bash
sudo ./venv/bin/python port_prowler.py 192.168.1.115 -p 22,80,443 -s
```
- **Explanation:** Stealth scan sends SYN, waits for SYN-ACK, then sends RST (does not complete handshake). Less likely to be logged.

---

### F. Scan with Multiple Protocols

```bash
python port_prowler.py 192.168.1.115 -p 21,22,80 -tcp -udp
```
---

### G. Save Output to File

```bash
python port_prowler.py 192.168.1.115 -p 21,22,80 -tcp -f scan_results.txt
```
- **Expected:** Results saved to `scan_results.txt` (auto-increments if file exists).

---

### H. Custom Timeout

```bash
python port_prowler.py 192.168.1.115 -p 80,443 -tcp --timeout 3
```
- **Explanation:** Adjusts the timeout for slow networks. 
---

### I. Error Handling

- No scan method:
    ```bash
    python port_prowler.py 192.168.1.115
    python port_prowler.py 192.168.1.115 -p 80
    ```
- Missing port number:
    ```bash
    python port_prowler.py 192.168.1.115 -p
    ```
- Missing filename after `-f`:
    ```bash
    python port_prowler.py 192.168.1.115 -p 80 -tcp -f
    ```
---

### J. Compare with Nmap (Validation)

#### TCP Scan
```bash
nmap -p 80,443,8080 -v 192.168.1.115
```
#### UDP Scan
```bash
sudo nmap -p 80,443,8080 -v -sU 192.168.1.115
```
#### Stealth Scan
```bash
sudo nmap -p 22,80,443 -v -sS 192.168.1.115
```
---

### K. Test with UFW (Firewall) Enabled

#### Enable UFW and Set Rules in Metasploitable 2
```bash
sudo ufw enable
sudo ufw allow 80/tcp
sudo ufw deny 443/tcp
sudo ufw allow 8080/tcp
sudo ufw status verbose
```
#### Run TCP and Stealth Scans Again
```bash
nmap -p 80,443,8080 -v 192.168.1.115
python port_prowler.py 192.168.1.115 -p 80,443,8080 -tcp
sudo  nmap -p 80,443,8080 -v -sS 192.168.1.115
sudo ./venv/bin/python port_prowler.py 192.168.1.115 -p 80,443,8080 -s
```

**Tip:**  
Always check UFW status before and after testing with:
```bash
sudo ufw status verbose
```
- **After you finish all UFW-related tests, restore the firewall to its original state.**
    - If you want to disable UFW (recommended for most labs):
        ```bash
        sudo ufw disable
        ```
    - If you want to remove all rules and reset UFW:
        ```bash
        sudo ufw reset
        ```
- This ensures that any further tests (or future users) are not affected by leftover firewall rules.
---

## 4. Feature Demonstrations

- **Service Detection:**  
  Output includes service names, e.g., `Port 22 (ssh): Open`.

- **Handles Closed/Filtered Ports:**  
  Output shows `Closed` or `Filtered` as appropriate.

- **Timeout Option:**  
  `--timeout` allows tuning for different network conditions.

```bash
python port_prowler.py 192.168.1.115 -p 1-100 -tcp --timeout 3
```
**`--timeout`:**  
  Sets how long the scanner waits for a response from each port before deciding it's closed or filtered.  
  (It does **not** affect how long the whole scan takes, just how long to wait per port.)

- **Scanning Multiple Ports (Single Protocol)** 
```bash
python port_prowler.py 192.168.1.115 -p 20-25,80,443 -tcp
```
- **Scanning with Multiple Protocols (Simultaneous Scans)**
```bash
sudo ./venv/bin/python port_prowler.py 192.168.1.115 -p 53,80,443 -tcp -udp
```
- **Combining All: Multiple Ports, Protocols, and Threads**
```bash
sudo ./venv/bin/python port_prowler.py 192.168.1.115 -p 21,22,53,80,443,8080 -tcp -udp --timeout 2
```

```bash
sudo ./venv/bin/python port_prowler.py 192.168.1.115 -p 21,22,53,80,443 -tcp -udp -s --timeout 3
```
---

## 5. Close Down

### a. Deactivate Python Virtual Environment
```bash
deactivate
```

### b. Shut Down Metasploitable 2
```bash
sudo shutdown now
```
If it asks `resume` , `root`  or `ffix` , choose `resume`.

Stop Metaspoitable2 in VirtualBox.

### c. Close VirtualBox

---

## Identifying Common Services by Port Number

For each port, Port Prowler calls `socket.getservbyport(port, proto)`, which returns the well-known service name (like "http" for 80/tcp, "ssh" for 22/tcp, etc.).
- If there is no standard service for that port, it returns an empty string.
- The scan output then shows both the port number and the detected service name, making results easier to interpret.

```python
def get_service_name(port, proto='tcp'):
    try:
        return socket.getservbyport(port, proto)
    except Exception:
        return ''
```
**TCP and SYN scans** both use the TCP protocol for service name lookups, because SYN scans are a type of TCP scan (they just use a different technique). **UDP scans** use the UDP protocol for service name lookups.
```
lookup_proto = 'tcp' if proto in ('tcp', 'syn') else 'udp'
```

---

## Multi-threading 

Port Prowler implements multi-threading using Python’s `ThreadPoolExecutor` to scan many ports in parallel. For each port, a separate thread is created to run the scan function (`scan_tcp`, `scan_udp`, or `scan_stealth`). This means that instead of scanning ports one by one (sequentially), the tool launches up to 50 threads at once (see `max_workers=50`), so many ports are checked at the same time. This greatly speeds up scanning, especially for large port ranges.

The core of this functionality is in the `scan_ports_concurrently` function in `scanner.py`:

```python
def scan_ports_concurrently(scan_func, ip, ports):
    from concurrent.futures import ThreadPoolExecutor
    with ThreadPoolExecutor(max_workers=50) as executor:
        futures = {executor.submit(scan_func, ip, port): port for port in ports}
        return {port: future.result() for future, port in futures.items()}
```

In the main script, this function is called for each scan type:
```python
results.update(scan_ports_concurrently(scan_tcp, args.ip, ports))
results.update(scan_ports_concurrently(scan_udp, args.ip, ports))
results.update(scan_ports_concurrently(stealth_wrapper, args.ip, ports))
```

**How it works:**
- Each port scan runs in its own thread.
- Many ports are scanned simultaneously, but each thread scans a unique port (no duplicate scans).
- This makes the scan much faster than scanning sequentially, as slow or unresponsive ports do not block the rest.

**Handling Closed and Filtered Ports:**
- Each scan function (`scan_tcp`, `scan_udp`, `scan_stealth`) determines if a port is **Open**, **Closed**, **Filtered**, or **Open|Filtered** based on the response (or lack of response).
- When scanning in parallel, each thread independently checks its assigned port and returns the correct status.
- The results are collected in a dictionary and displayed or saved, showing the status for every port, even when many are scanned at once.

**Example logic:**
- TCP: If connection succeeds → "Open", refused → "Closed", error/timeout → "Filtered"
- UDP: Response → "Open", ICMP Port Unreachable → "Closed", timeout → "Open|Filtered", error → "Error"
- SYN: SYN+ACK → "Open", RST+ACK → "Closed", no response → "Filtered"

**Summary:**  
- Each scan function scans one port at a time per call.
- With concurrency, many ports are scanned at once, each in its own thread.
- This approach allows Port Prowler to scan large port ranges quickly and efficiently, while accurately reporting the status of each port.

---

## Handling Network Conditions and Timeouts

Network conditions—such as latency, firewalls, or packet loss—can significantly affect scan results. If a port does not respond within the specified timeout period, it may be marked as "Filtered" or "Open|Filtered", even if it is actually open but slow to respond. To address this, Port Prowler provides the `--timeout` option, allowing you to adjust how long the scanner waits for a response from each port.

- **High latency:**  
  If the network is slow or has high latency, increasing the timeout (e.g., `--timeout 3`) gives ports more time to respond before being marked as "Filtered" or "Closed". This helps reduce false negatives caused by delayed responses.

- **Packet loss:**  
  In unreliable networks where packet loss is common, a longer timeout allows for possible retransmissions or delayed packets, making it less likely that open ports are incorrectly reported as filtered or closed.

By tuning the `--timeout` value, you can improve the accuracy of your scan results and ensure more reliable detection of open, closed, or filtered ports under a variety of network conditions.

---

## Legal and Ethical Disclaimer
Port scanning without permission is illegal. Ensure you have explicit authorization to scan any network or system. This tool is intended for educational purposes and should only be used in compliance with applicable laws and regulations.

---

## Coder
Laura Levistö
6/25