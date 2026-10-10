# Basic Network Lab

## Goal
Practice basic networking tools between my Linux Mint test PC and my Windows laptop.

## Setup
- Linux Mint PC: `192.168.1.64`
- Windows laptop: `192.168.1.39`
- Router / Gateway: `192.168.1.1`
- Network: `192.168.1.0/24`

## What I Did

### 1. Checked my network info
```bash - Linux
ip a
ip route
```
```powershell - Windows

ipconfig
```
This helped me identify my local IP, gateway, and subnet.

### 2. Tested connectivity
From Windows I pinged my Linux PC:

```powershell
ping 192.168.1.64
```

The ping worked.

From Linux to Windows, the ping failed because Windows was blocking ICMP traffic.

### 3. Scanned my Windows laptop with Nmap
```bash
nmap -Pn -sV 192.168.1.39
```

I found an HTTP service running on port `5357`.

I checked it directly with:

```bash
curl -I http://192.168.1.39:5357
```

The server responded with `503 Service Unavailable`, which confirmed that the service was reachable.

### 4. Watched traffic live
I used tcpdump to watch traffic between the two computers:

```bash
sudo tcpdump -n -i any host 192.168.1.39
```

Then I sent pings and Nmap scans from Windows and could see the packets arriving in real time.

### 5. Opened a test port on Linux
I checked that UFW was active:

```bash
sudo ufw status
```

Then I allowed port 8000:

```bash
sudo ufw allow 8000/tcp
```

I started a temporary Python web server:

```bash
python3 -m http.server 8000
```

From Windows I scanned it:

```powershell
nmap -Pn -p 8000 192.168.1.64
```

Nmap showed:

```text
8000/tcp open
```

Then I opened this in my Windows browser:

```text
http://192.168.1.64:8000
```

The page loaded successfully and I could see the HTTP `GET` requests appear in the Linux terminal.

### 6. Cleaned everything up
I stopped the Python server with:

```text
Ctrl + C
```

Then removed the firewall rule:

```bash
sudo ufw delete allow 8000/tcp
```

After scanning again, the port appeared filtered.

## What I Learned
- How to find my IP, subnet, and default gateway
- How ping and firewalls affect connectivity
- Basic Nmap scanning
- How to identify a service running on a port
- How to inspect HTTP responses with curl
- How to watch packets with tcpdump
- How opening and closing a firewall port changes what another machine can see
- How to run a basic HTTP server between two computers on the same network
