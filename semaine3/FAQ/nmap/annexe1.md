### Course Outline: Nmap Commands in Kali Linux

#### Module 1: Introduction to Kali Linux and Nmap
- Overview of Kali Linux
- Introduction to Nmap
  - What is Nmap?
  - Importance of Nmap in network security
- Installing and updating Nmap on Kali Linux

#### Module 2: Basic Nmap Commands
- Syntax of Nmap commands
- Simple host discovery
  - `nmap <target>`
  - `nmap -sn <target>`
- Scanning multiple hosts
  - `nmap <target1> <target2> <target3>`

#### Module 3: Advanced Scanning Techniques
- TCP connect scan
  - `nmap -sT <target>`
- SYN scan
  - `nmap -sS <target>`
- UDP scan
  - `nmap -sU <target>`
- TCP ACK scan
  - `nmap -sA <target>`
- TCP Window scan
  - `nmap -sW <target>`

#### Module 4: OS and Service Detection
- Detecting operating systems
  - `nmap -O <target>`
- Detecting services and versions
  - `nmap -sV <target>`
- Combining OS and service detection
  - `nmap -A <target>`

#### Module 5: Network Mapping and Inventory
- Scanning subnets
  - `nmap -sn <subnet>`
- Creating network maps
  - `nmap -sn -oX network_map.xml <subnet>`
- Using Nmap scripts
  - `nmap --script <script_name> <target>`
  - Example: `nmap --script vuln <target>`

#### Module 6: Firewall Evasion and Spoofing
- Using decoys
  - `nmap -D RND:10 <target>`
- Fragmentation
  - `nmap -f <target>`
- Timing options
  - `nmap -T<0-5> <target>`

#### Module 7: Reporting and Output
- Saving scan results
  - `nmap -oN output.txt <target>`
  - `nmap -oX output.xml <target>`
  - `nmap -oG output.gnmap <target>`
- Analyzing Nmap output files

#### Module 8: Practical Exercises and Case Studies
- Hands-on lab: Scanning a network with Nmap
- Case study: Using Nmap for vulnerability assessment
- Exercise: Mapping a corporate network

#### Module 9: Ethical Considerations and Best Practices
- Legal aspects of network scanning
- Ethical use of Nmap
- Best practices for network security

### Detailed Example Commands and Explanations

#### Simple Host Discovery
```bash
nmap <target>
```
- This basic command scans the specified target IP address or hostname.

#### SYN Scan
```bash
nmap -sS <target>
```
- This command performs a SYN scan, which is less likely to be logged by the target system.

#### OS Detection
```bash
nmap -O <target>
```
- This command attempts to determine the operating system of the target.

#### Service Version Detection
```bash
nmap -sV <target>
```
- This command probes open ports to determine the service and version running on the target.

#### Using Nmap Scripts
```bash
nmap --script vuln <target>
```
- This command uses the `vuln` script to check for vulnerabilities on the target system.

#### Saving Scan Results
```bash
nmap -oN output.txt <target>
```
- This command saves the scan results in a normal text format.

### Practical Exercise Example

**Exercise: Scanning a Corporate Network**
1. Scan the subnet `192.168.1.0/24` to discover live hosts.
   ```bash
   nmap -sn 192.168.1.0/24
   ```
2. Perform a SYN scan on the discovered hosts.
   ```bash
   nmap -sS -iL discovered_hosts.txt
   ```
3. Detect operating systems and service versions.
   ```bash
   nmap -A -iL discovered_hosts.txt
   ```
4. Save the results in XML format.
   ```bash
   nmap -oX scan_results.xml -iL discovered_hosts.txt
   ```
