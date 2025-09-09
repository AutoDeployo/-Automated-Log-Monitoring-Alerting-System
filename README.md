# Automated Log Monitoring Alerting System
A small-scale security project that keeps an eye on Linux authentication logs (/var/log/auth.log) in order to identify unsuccessful attempts at SSH login. 

---

🔹 Phase 1: Setup Environment
Provision an Ubuntu 22.04 VM (AWS / VirtualBox / VMware).
Update system:
sudo apt update && sudo apt upgrade -y
Install mail utilities:
sudo apt install mailutils -y

---

🔹 Phase 2: Identify Logs to Monitor
Security logs on Ubuntu:
/var/log/auth.log → records login attempts

Test with:
   sudo tail -f /var/log/auth.log

From another machine:
   ssh wronguser@<server-ip>

---

🔹 Phase 3: Write Log Monitoring Script
Create script:
sudo nano /usr/local/bin/log_monitor.sh

Add content:#!/bin/bash
LOG_FILE="/var/log/auth.log"
ALERT_EMAIL="your_email@example.com"

   FAILED_LOGINS=$(grep "Failed password" $LOG_FILE | grep "$(date --date='10 minutes ago' '+%b %e %H:')")

   if [ ! -z "$FAILED_LOGINS" ]; then
       echo -e "⚠️ ALERT: Failed SSH login attempts detected!\n\n$FAILED_LOGINS" \
       | mail -s "SSH Login Alert on $(hostname)" $ALERT_EMAIL
   fi

Make it executable:
sudo chmod +x /usr/local/bin/log_monitor.sh

---

🔹 Phase 4: Automate with Cron
Edit cron:
crontab -e

Add line:/10 * * * /usr/local/bin/log_monitor.sh

Verify:
crontab -l

---

🔹 Phase 5: Test the System
From another machine, attempt wrong SSH logins:
ssh wronguser@<server-ip>
Wait 10 mins → script triggers email.
Check logs:
tail -f /var/log/mail.log

---

⚡ This project demonstrates Linux logs, shell scripting, cron jobs, and server security automation.
