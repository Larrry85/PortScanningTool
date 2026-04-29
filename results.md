
# Run and Test All Required Cases

## A. Show Help and Usage

```bash
python port_prowler.py --help
```
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ python port_prowler.py --help
usage: port_prowler.py [-h] [-p PORTS] [-tcp] [-udp] [-s] [-f FILE] [--timeout TIMEOUT] ip

positional arguments:
  ip                    Target IP address

options:
  -h, --help            show this help message and exit
  -p PORTS, --ports PORTS
                        Comma-separated list of ports or port ranges
  -tcp                  Enable TCP scanning
  -udp                  Enable UDP scanning
  -s, --stealth         Enable stealth scanning
  -f FILE, --file FILE  Output results to a file
  --timeout TIMEOUT     Set the timeout in seconds.
(venv)
```
---

## B. Test Port Input Handling

### Single Port
```bash
python port_prowler.py 192.168.1.115 -p 80 -tcp
```
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ python port_prowler.py 192.168.1.115 -p 80 -tcp
Port 80 (http): Open
(venv)
```

### Multiple Ports
```bash
python port_prowler.py 192.168.1.115 -p 21,22,80 -tcp
```
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ python port_prowler.py 192.168.1.115 -p 21,22,80 -tcp
Port 21 (ftp): Open
Port 22 (ssh): Open
Port 80 (http): Open
(venv)
```

### Port Range
```bash
python port_prowler.py 192.168.1.115 -p 20-25 -tcp
```
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ python port_prowler.py 192.168.1.115 -p 20-25 -tcp
Port 20 (ftp-data): Closed
Port 21 (ftp): Open
Port 22 (ssh): Open
Port 23 (telnet): Open
Port 24: Closed
Port 25 (smtp): Open
(venv)
```
---

## C. TCP Connect Scan (`-tcp`)

```bash
python port_prowler.py 192.168.1.115 -p 80,443,8080 -tcp
```
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ python port_prowler.py 192.168.1.115 -p 80,443,8080 -tcp
Port 80 (http): Open
Port 443 (https): Closed
Port 8080 (http-alt): Closed
(venv)
```
---

## D. UDP Scan (`-udp`)

```bash
sudo ./venv/bin/python port_prowler.py 192.168.1.115  -p 53,69,161 -udp
```
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ sudo ./venv/bin/python port_prowler.py 192.168.1.115  -p 53,69,161 -udp
Port 53 (domain): Open|Filtered
Port 69 (tftp): Open|Filtered
Port 161 (snmp): Closed
(venv)
```
---

## E. Stealth (SYN) Scan (`-s`)

```bash
sudo ./venv/bin/python port_prowler.py 192.168.1.115 -p 22,80,443 -s
```
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ sudo ./venv/bin/python port_prowler.py 192.168.1.115 -p 22,80,443 -s
[sudo] password for student:         
Port 22 (ssh): Open
Port 80 (http): Open
Port 443 (https): Closed
(venv)
```
---

## F. Scan with Multiple Protocols

```bash
sudo ./venv/bin/python port_prowler.py 192.168.1.115 -p 21,22,80 -tcp -udp
```
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ sudo ./venv/bin/python port_prowler.py 192.168.1.115 -p 21,22,80 -tcp -udp
Port 21 (ftp): Open
Port 22 (ssh): Open
Port 80 (http): Open
Port 21 (fsp): Closed
Port 22: Closed
Port 80: Closed
(venv)
```
---

## G. Save Output to File

```bash
python port_prowler.py 192.168.1.115 -p 21,22,80 -tcp -f scan_results.txt
sudo ./venv/bin/python port_prowler.py 192.168.1.115 -p 21,22,80 -udp -f scan_results.txt
sudo ./venv/bin/python port_prowler.py 192.168.1.115 -p 22,80,443  -s -f scan_results.txt
```
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ python port_prowler.py 192.168.1.115 -p 21,22,80 -tcp -f scan_results.txt
Port 21 (ftp): Open
Port 22 (ssh): Open
Port 80 (http): Open
Results saved to scan_results3.txt       
```
file:
```
Port 21 (ftp): Open
Port 22 (ssh): Open
Port 80 (http): Open
```
---

## H. Custom Timeout

```bash
python port_prowler.py 192.168.1.115 -p 80,443 -tcp --timeout 3
```
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ python port_prowler.py 192.168.1.115 -p 80,443 -tcp --timeout 3
Port 80 (http): Open
Port 443 (https): Closed
(venv)
```
---

## I. Error Handling

- No scan method:
```bash
python port_prowler.py 192.168.1.115
python port_prowler.py 192.168.1.115 -p 80
```
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ python port_prowler.py 192.168.1.115
Please specify at least one scanning method: -tcp, -udp, or -s for stealth.
(venv)
```

- Missing port number:
```bash
python port_prowler.py 192.168.1.115 -p
```
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ python port_prowler.py 192.168.1.115 -p
usage: port_prowler.py [-h] [-p PORTS] [-tcp] [-udp] [-s] [-f FILE] [--timeout TIMEOUT] ip
port_prowler.py: error: argument -p/--ports: expected one argument
(venv)
```

- Missing filename after `-f`:
```bash
python port_prowler.py 192.168.1.115 -p 80 -tcp -f
```
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ python port_prowler.py 192.168.1.115 -p 80 -tcp -f
usage: port_prowler.py [-h] [-p PORTS] [-tcp] [-udp] [-s] [-f FILE] [--timeout TIMEOUT] ip
port_prowler.py: error: argument -f/--file: expected one argument
(venv)
```
---

## J. Compare with Nmap (Validation)

### TCP Scan
```bash
nmap -p 80,443,8080 -v 192.168.1.115
```
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ nmap -p 80,443,8080 -v 192.168.1.115
Starting Nmap 7.80 ( https://nmap.org ) at 2025-06-02 17:16 EEST
Initiating Ping Scan at 17:16
Scanning 192.168.1.115 [2 ports]
Completed Ping Scan at 17:16, 0.01s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 17:16
Completed Parallel DNS resolution of 1 host. at 17:16, 0.00s elapsed
Initiating Connect Scan at 17:16
Scanning 192.168.1.115 [3 ports]
Discovered open port 80/tcp on 192.168.1.115
Completed Connect Scan at 17:16, 0.00s elapsed (3 total ports)
Nmap scan report for 192.168.1.115
Host is up (0.0039s latency).

PORT     STATE  SERVICE
80/tcp   open   http
443/tcp  closed https
8080/tcp closed http-proxy

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.05 seconds
(venv)
```
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ python port_prowler.py 192.168.1.115 -p 80,443,8080 -tcp
Port 80 (http): Open
Port 443 (https): Closed
Port 8080 (http-alt): Closed
(venv)
```

### UDP Scan
```bash
sudo nmap -p 80,443,8080 -v -sU 192.168.1.115
```
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ sudo nmap -p 80,443,8080 -v -sU 192.168.1
.115
Starting Nmap 7.80 ( https://nmap.org ) at 2025-06-13 15:31 EEST
Initiating ARP Ping Scan at 15:31
Scanning 192.168.1.115 [1 port]
Completed ARP Ping Scan at 15:31, 0.03s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 15:31
Completed Parallel DNS resolution of 1 host. at 15:31, 0.00s elapsed
Initiating UDP Scan at 15:31
Scanning 192.168.1.115 [3 ports]
Completed UDP Scan at 15:31, 0.04s elapsed (3 total ports)
Nmap scan report for 192.168.1.115
Host is up (0.00058s latency).

PORT     STATE  SERVICE
80/udp   closed http
443/udp  closed https
8080/udp closed http-alt
MAC Address: 08:00:27:EF:67:62 (Oracle VirtualBox virtual NIC)

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.34 seconds
           Raw packets sent: 4 (193B) | Rcvd: 4 (277B)
(venv)
```
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ sudo ./venv/bin/python port_prowler.py 192.168.1.115  -p 80,443,8080 -udp
Port 80: Closed
Port 443 (https): Closed
Port 8080: Closed
(venv)
```

#### Stealth Scan
```bash
sudo nmap -p 22,80,443 -v -sS 192.168.1.115
```
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ sudo nmap -p 22,80,443 -v -sS 192.168.1.115
Starting Nmap 7.80 ( https://nmap.org ) at 2025-06-02 17:16 EEST
Initiating ARP Ping Scan at 17:16
Scanning 192.168.1.115 [1 port]
Completed ARP Ping Scan at 17:16, 0.02s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 17:16
Completed Parallel DNS resolution of 1 host. at 17:16, 0.01s elapsed
Initiating SYN Stealth Scan at 17:16
Scanning 192.168.1.115 [3 ports]
Discovered open port 80/tcp on 192.168.1.115
Discovered open port 22/tcp on 192.168.1.115
Completed SYN Stealth Scan at 17:16, 0.02s elapsed (3 total ports)
Nmap scan report for 192.168.1.115
Host is up (0.00026s latency).

PORT    STATE  SERVICE
22/tcp  open   ssh
80/tcp  open   http
443/tcp closed https
MAC Address: 08:00:27:EF:67:62 (Oracle VirtualBox virtual NIC)

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.26 seconds
           Raw packets sent: 4 (160B) | Rcvd: 4 (156B)
(venv)
```
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ sudo ./venv/bin/python port_prowler.py 192.168.1.115 -p 22,80,443 -s
Port 22 (ssh): Open
Port 80 (http): Open
Port 443 (https): Closed
(venv)
```
---

## K. Test with UFW (Firewall) Enabled

### Enable UFW and Set Rules in Metasploitable 2
```bash
sudo ufw enable
sudo ufw allow 80/tcp
sudo ufw deny 443/tcp
sudo ufw allow 8080/tcp
sudo ufw status verbose
```
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ sudo ufw enable
sudo ufw allow 80/tcp
sudo ufw deny 443/tcp
sudo ufw allow 8080/tcp
sudo ufw status verbose
Firewall is active and enabled on system startup
Rule added
Rule added (v6)
Rule added
Rule added (v6)
Rule added
Rule added (v6)
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), deny (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
80/tcp                     ALLOW IN    Anywhere                  
443/tcp                    DENY IN     Anywhere                  
8080/tcp                   ALLOW IN    Anywhere                  
80/tcp (v6)                ALLOW IN    Anywhere (v6)             
443/tcp (v6)               DENY IN     Anywhere (v6)             
8080/tcp (v6)              ALLOW IN    Anywhere (v6)             

(venv)
```

### Run TCP and Stealth Scans

**Nmap TCP**
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ nmap -p 80,443,8080 -v 192.168.1.115
Starting Nmap 7.80 ( https://nmap.org ) at 2025-06-13 15:35 EEST
Initiating Ping Scan at 15:35
Scanning 192.168.1.115 [2 ports]
Completed Ping Scan at 15:35, 0.00s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 15:35
Completed Parallel DNS resolution of 1 host. at 15:35, 0.00s elapsed
Initiating Connect Scan at 15:35
Scanning 192.168.1.115 [3 ports]
Discovered open port 80/tcp on 192.168.1.115
Completed Connect Scan at 15:35, 0.00s elapsed (3 total ports)
Nmap scan report for 192.168.1.115
Host is up (0.00032s latency).

PORT     STATE  SERVICE
80/tcp   open   http
443/tcp  closed https
8080/tcp closed http-proxy

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.05 seconds
(venv)
```
**TCP**
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ python port_prowler.py 192.168.1.115 -p 80,443,8080 -tcp
Port 80 (http): Open
Port 443 (https): Closed
Port 8080 (http-alt): Closed
(venv)
```
**Nmapl Stealth**
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ sudo  nmap -p 80,443,8080 -v -sS 192.168.1.115
Starting Nmap 7.80 ( https://nmap.org ) at 2025-06-13 15:40 EEST
Initiating ARP Ping Scan at 15:40
Scanning 192.168.1.115 [1 port]
Completed ARP Ping Scan at 15:40, 0.02s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 15:40
Completed Parallel DNS resolution of 1 host. at 15:40, 0.00s elapsed
Initiating SYN Stealth Scan at 15:40
Scanning 192.168.1.115 [3 ports]
Discovered open port 80/tcp on 192.168.1.115
Completed SYN Stealth Scan at 15:40, 0.02s elapsed (3 total ports)
Nmap scan report for 192.168.1.115
Host is up (0.00027s latency).

PORT     STATE  SERVICE
80/tcp   open   http
443/tcp  closed https
8080/tcp closed http-proxy
MAC Address: 08:00:27:EF:67:62 (Oracle VirtualBox virtual NIC)

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.21 seconds
           Raw packets sent: 4 (160B) | Rcvd: 4 (152B)
(venv)
```
**Stealth**
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ sudo ./venv/bin/python port_prowler.py 192.168.1.115 -p 80,443,8080 -s
Port 80 (http): Open
Port 443 (https): Closed
Port 8080 (http-alt): Closed
(venv)
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
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ sudo ufw disable
Firewall stopped and disabled on system startup
(venv) student@student-ThinkPad-T490s:~/port-prowler$ sudo ufw reset
Resetting all rules to installed defaults. Proceed with operation (y|n)? y
Backing up 'user.rules' to '/etc/ufw/user.rules.20250602_171925'
Backing up 'before.rules' to '/etc/ufw/before.rules.20250602_171925'
Backing up 'after.rules' to '/etc/ufw/after.rules.20250602_171925'
Backing up 'user6.rules' to '/etc/ufw/user6.rules.20250602_171925'
Backing up 'before6.rules' to '/etc/ufw/before6.rules.20250602_171925'
Backing up 'after6.rules' to '/etc/ufw/after6.rules.20250602_171925'

(venv) student@student-ThinkPad-T490s:~/port-prowler$ sudo ufw status verbose
Status: inactive
(venv)
```


Testing again after ufw disable and reset:
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ python port_prowler.py 192.168.1.115 -p 80,443,8080 -tcp
Port 80 (http): Open
Port 443 (https): Closed
Port 8080 (http-alt): Closed
(venv)

(venv) student@student-ThinkPad-T490s:~/port-prowler$ sudo ./venv/bin/python port_prowler.py 192.168.1.115 -p 80,443,8080 -s
Port 80 (http): Open
Port 443 (https): Closed
Port 8080 (http-alt): Closed
(venv)
```
---


## 4. Feature Demonstrations

```
time python port_prowler.py 192.168.1.115 -p 1-100 -tcp
```
**`time`**:  
A Linux command that measures how long a command takes to run (shows real, user, sys times as above).
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ time python port_prowler.py 192.168.1.115 -p 1-100 -tcp
Port 1 (tcpmux): Closed
Port 2: Closed
Port 3: Closed
Port 4: Closed
Port 5: Closed
Port 6: Closed
Port 7 (echo): Closed
Port 8: Closed
Port 9 (discard): Closed
Port 10: Closed
Port 11 (systat): Closed
Port 12: Closed
Port 13 (daytime): Closed
Port 14: Closed
Port 15 (netstat): Closed
Port 16: Closed
Port 17 (qotd): Closed
Port 18: Closed
Port 19 (chargen): Closed
Port 20 (ftp-data): Closed
Port 21 (ftp): Open
Port 22 (ssh): Open
Port 23 (telnet): Open
Port 24: Closed
Port 25 (smtp): Open
Port 26: Closed
Port 27: Closed
Port 28: Closed
Port 29: Closed
Port 30: Closed
Port 31: Closed
Port 32: Closed
Port 33: Closed
Port 34: Closed
Port 35: Closed
Port 36: Closed
Port 37 (time): Closed
Port 38: Closed
Port 39: Closed
Port 40: Closed
Port 41: Closed
Port 42: Closed
Port 43 (whois): Closed
Port 44: Closed
Port 45: Closed
Port 46: Closed
Port 47: Closed
Port 48: Closed
Port 49 (tacacs): Closed
Port 50: Closed
Port 51: Closed
Port 52: Closed
Port 53 (domain): Open
Port 54: Closed
Port 55: Closed
Port 56: Closed
Port 57: Closed
Port 58: Closed
Port 59: Closed
Port 60: Closed
Port 61: Closed
Port 62: Closed
Port 63: Closed
Port 64: Closed
Port 65: Closed
Port 66: Closed
Port 67: Closed
Port 68: Closed
Port 69: Closed
Port 70 (gopher): Closed
Port 71: Closed
Port 72: Closed
Port 73: Closed
Port 74: Closed
Port 75: Closed
Port 76: Closed
Port 77: Closed
Port 78: Closed
Port 79 (finger): Closed
Port 80 (http): Open
Port 81: Closed
Port 82: Closed
Port 83: Closed
Port 84: Closed
Port 85: Closed
Port 86: Closed
Port 87: Closed
Port 88 (kerberos): Closed
Port 89: Closed
Port 90: Closed
Port 91: Closed
Port 92: Closed
Port 93: Closed
Port 94: Closed
Port 95: Closed
Port 96: Closed
Port 97: Closed
Port 98: Closed
Port 99: Closed
Port 100: Closed

real    0m0,111s
user    0m0,077s
sys     0m0,040s
(venv)
```
- **real**: The total elapsed wall-clock time from start to finish of the command (how long you waited).
- **user**: The amount of CPU time spent in user-mode (your Python code).
- **sys**: The amount of CPU time spent in kernel-mode (system calls, like networking).

---

### **Scanning Multiple Ports (Single Protocol)**
Scan a range or list of ports at once:
```bash
python port_prowler.py 192.168.1.115 -p 20-25,80,443 -tcp
```
- This scans ports 20, 21, 22, 23, 24, 25, 80, and 443 in one run.
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ python port_prowler.py 192.168.1.115 -p 20-25,80,443 -tcp
Port 20 (ftp-data): Closed
Port 21 (ftp): Open
Port 22 (ssh): Open
Port 23 (telnet): Open
Port 24: Closed
Port 25 (smtp): Open
Port 80 (http): Open
Port 443 (https): Closed
(venv)
```

---

### **Scanning with Multiple Protocols (Simultaneous Scans)**
Scan the same ports with both TCP and UDP at the same time:
```bash
sudo ./venv/bin/python port_prowler.py 192.168.1.115 -p 53,80,443 -tcp -udp
```
- This runs both TCP and UDP scans on ports 53, 80, and 443 in parallel.
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ sudo ./venv/bin/python port_prowler.py 192.168.1.115 -p 53,80,443 -tcp -udp
Port 53 (domain): Open
Port 80 (http): Open
Port 443 (https): Closed
Port 53 (domain): Open|Filtered
Port 80: Closed
Port 443 (https): Closed
(venv)
```

### **Combining All: Multiple Ports, Protocols, and Threads**
```bash
sudo ./venv/bin/python port_prowler.py 192.168.1.115 -p 21,22,53,80,443,8080 -tcp -udp --timeout 2
```
- Scans 6 ports with both TCP and UDP, using multiple threads for speed, and a custom timeout.
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ sudo ./venv/bin/python port_prowler.py 192.168.1.115 -p 21,22,53,80,443,8080 -tcp -udp --timeout 2
Port 21 (ftp): Open
Port 22 (ssh): Open
Port 53 (domain): Open
Port 80 (http): Open
Port 443 (https): Closed
Port 8080 (http-alt): Closed
Port 21 (fsp): Closed
Port 22: Closed
Port 53 (domain): Open|Filtered
Port 80: Closed
Port 443 (https): Closed
Port 8080: Closed
(venv)
```

```bash
sudo ./venv/bin/python port_prowler.py 192.168.1.115 -p 21,22,53,80,443 -tcp -udp -s --timeout 3
```
```
(venv) student@student-ThinkPad-T490s:~/port-prowler$ sudo ./venv/bin/python port_prowler.py 192.168.1.115 -p 21,22,53,80,443 -tcp -udp -s --timeout 3
Port 21 (ftp): Open
Port 22 (ssh): Open
Port 53 (domain): Open
Port 80 (http): Open
Port 443 (https): Closed
Port 21 (fsp): Closed
Port 22: Closed
Port 53 (domain): Open|Filtered
Port 80: Closed
Port 443 (https): Closed
Port 21 (ftp): Open
Port 22 (ssh): Open
Port 53 (domain): Open
Port 80 (http): Open
Port 443 (https): Closed
(venv)
```
---