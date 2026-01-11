## SSH and Telnet Exposure
The MiniSOC environment deliberately exposes **SSH and Telnet services** to emulate common attack surfaces in the wild. These protocols are frequent targets for automated scanning, brute-force attacks, and credential theft. By running Cowrie honeypots on these services, the system can capture and log realistic attack patterns, providing actionable threat intelligence without exposing the real administrative infrastructure.

## Non-Standard Ports
The real administrative SSH service has been moved to a non-standard port (e.g., 52222) to reduce exposure to automated attacks that target default ports like 22. This relocation does not inherently improve security against a targeted attacker but improves signal-to-noise ratio, allowing the honeypot to capture only relevant attack attempts and keeping logs cleaner.

## Threat Assumptions
The threat model assumes the following:
1. **Automated attacks are frequent:** Internet-facing services will encounter scanning, brute-force login attempts, and exploitation attempts.
2. **Attackers may attempt to exploit** misconfigurations: Services running with elevated privileges or default credentials are high-value targets.
3. **Adversaries may be non-targeted:** Many attacks are opportunistic rather than sophisticated.
4. **System compromises should be contained:** Any honeypot compromise should not affect the real administrative SSH service, which runs separately on a non-standard port with key-based authentication.
5. **Detection is more valuable than prevention:** Capturing attack data and monitoring intrusion attempts is prioritized over trying to make services invisible or “unhackable.”




 # DIAGRAM
 
                Internet
                   |
          ---------------------
          |                   |
     Attackers             Automated Scanners
          |                   |
          v                   v
      +--------+          +--------+
      | Port 22 | <----- Cowrie Honeypot
      | (SSH/Telnet)      - Captures attacks
      | (non-root via     - Logs attempts
      |  authbind)        - Isolated from real SSH
      +--------+
          |
          |
     ------------------
     | Real Admin SSH |
     | Port 52222     | <-- Only accessible via key-based auth
     | Root login     |
     | disabled       |
     ------------------
