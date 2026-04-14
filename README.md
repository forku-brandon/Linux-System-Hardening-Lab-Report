
# Linux System Hardening Lab

## Project Overview

This repository documents a comprehensive Linux system hardening lab that implements industry-standard security controls for production server environments. The lab covers user access management, sudo configuration, SSH security, password policy enforcement, account lifecycle management, and security banner implementation. All configurations were tested on a Linux server environment and verified using standard system administration commands.

## Lab Objectives

- Establish centralized administrative access control using dedicated groups
- Configure granular sudo privileges with custom timeouts and user aliases
- Secure remote access by disabling direct root login via SSH
- Enforce strong password complexity requirements using PAM
- Implement password aging policies with automated expiration
- Secure file permissions through system-wide umask configuration
- Establish proper employee offboarding procedures
- Display legal login banners for compliance purposes

## Technologies Used

- **Operating System:** Linux (Ubuntu / CentOS / RHEL)
- **Key Configuration Files:** 
  - `/etc/ssh/sshd_config` - SSH security
  - `/etc/sudoers.d/` - Sudo privileges
  - `/etc/security/pwquality.conf` - Password policy
  - `/etc/profile` - System-wide umask
  - `/etc/issue` - Login banner
- **Management Tools:** useradd, usermod, passwd, chage, visudo, systemctl

## Lab Tasks Summary

| Task | Description | Status |
|------|-------------|--------|
| Task 1 | Create Administrative Group (sysadmins) with users alice and bob | ✅ Complete |
| Task 2 | Configure sudo access for sysadmins group using visudo | ✅ Complete |
| Task 3 | Disable direct root login via SSH (PermitRootLogin no) | ✅ Complete |
| Task 4 | Set sudo authentication timeout to 3 minutes | ✅ Complete |
| Task 5 | Create sudo User_Alias ADMINS containing alice and bob | ✅ Complete |
| Task 6 | User Hardening (Part 1) | ⏳ Pending |
| Task 7 | Configure system-wide umask to 027 (files = 640) | ✅ Complete |
| Task 8 | Enforce strong password policy (minlen=12, complexity) | ✅ Complete |
| Task 9 | Set password expiration (90 days, 7-day warning) | ✅ Complete |
| Task 10 | Lock vacation account (intern user) | ✅ Complete |
| Task 11 | Employee offboarding with backup (tempuser) | ✅ Complete |
| Task 12 | Create login security banner | ✅ Complete |

## Key Commands Executed

### User and Group Management
```bash
sudo groupadd sysadmins
sudo useradd -m alice
sudo useradd -m bob
sudo usermod -aG sysadmins alice
sudo usermod -aG sysadmins bob
Sudo Configuration
bash
echo "%sysadmins ALL=(ALL) ALL" | sudo tee -a /etc/sudoers.d/sysadmins
echo "Defaults timestamp_timeout=3" | sudo tee -a /etc/sudoers.d/timeout
echo -e "User_Alias ADMINS = alice, bob\nADMINS ALL=(ALL) ALL" | sudo tee -a /etc/sudoers.d/admins
SSH Security
bash
sudo sed -i 's/^#*PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
sudo systemctl restart ssh
Password Policy and Expiration
bash
echo -e "minlen = 12\nucredit = -1\nlcredit = -1\ndcredit = -1" | sudo tee -a /etc/security/pwquality.conf
sudo chage -M 90 -W 7 alice
sudo chage -M 90 -W 7 bob
Umask Configuration
bash
echo "umask 027" | sudo tee -a /etc/profile
source /etc/profile
Account Locking and Offboarding
bash
sudo passwd -l intern
sudo passwd -l tempuser
sudo usermod -L tempuser
sudo tar -czf tempuser_backup.tar.gz /home/tempuser
Security Banner
bash
echo -e "Authorized access only.\nAll activities monitored." | sudo tee /etc/issue
Verification Results
Each configuration was verified using appropriate validation commands:

Configuration	Verification Command	Expected Result
Admin Group	getent group sysadmins	sysadmins:x:1001:alice,bob
Sudo Access	sudo -l -U alice	(ALL) ALL
Root Login	sudo sshd -T | grep permitrootlogin	permitrootlogin no
Sudo Timeout	sudo sudo -V | grep timestamp_timeout	Timestamp timeout: 3 minutes
Umask	umask	0027
Password Expiry	sudo chage -l alice	Max: 90 days, Warning: 7 days
Lock Status	sudo passwd -S intern	intern L ...
Security Banner	cat /etc/issue	Authorized access only...
Directory Structure
text
linux-hardening-lab/
├── README.md
├── screenshots/
│   ├── task1_group_creation.png
│   ├── task2_sudo_config.png
│   ├── task3_ssh_config.png
│   ├── task4_sudo_timeout.png
│   ├── task5_sudo_alias.png
│   ├── task7_umask.png
│   ├── task8_password_policy.png
│   ├── task9_chage.png
│   ├── task10_lock_account.png
│   ├── task11_offboarding.png
│   └── task12_banner.png
├── backups/
│   └── tempuser_backup.tar.gz
├── configs/
│   ├── sshd_config.backup
│   ├── pwquality.conf.backup
│   └── profile.backup
└── report/
    └── Linux_System_Hardening_Lab_Report.pdf
Troubleshooting Notes
Issue: SSH service restart failed with "Unit sshd.service not found"

Cause: Ubuntu/Debian systems name the SSH service ssh instead of sshd

Resolution: Used sudo systemctl restart ssh instead

Issue: Sudoers syntax error preventing sudo access

Prevention: Always use visudo which performs syntax checking before saving

Recovery: Boot into recovery mode or use physical console access

References
Garn, D. (2025, April 14). How to create custom sudo configuration files in /etc/sudoers. TechTarget. https://www.techtarget.com/searchsecurity/tutorial/How-to-create-custom-sudo-configuration-files-in-etc-sudoers

LabEx. (2025, August 30). How to secure root access? https://labex.io/questions/how-to-secure-root-access-941062

man7.org. (2026, January 16). chage(1) — Linux manual page. Linux Programmer's Manual. https://www.man7.org/linux/man-pages/man1/chage.1.html

OpenDev / StarlingX Project. (2025, January 31). Setting a secure umask value (CIS 5.4.2.6). https://opendev.org/starlingx/config-files/src/commit/5df711b1fb9eff45219c1e65ff5f740093d8d741/base-files-config/source/umask.sh

Learning Outcomes
After completing this lab, you should be able to:

Implement role-based access control using Linux groups and sudo

Harden SSH remote access against unauthorized root login attempts

Configure PAM-based password complexity requirements

Manage password aging and expiration policies using chage

Set appropriate system-wide default file permissions

Execute proper employee offboarding procedures including account locking and data backup

Deploy login banners for legal and security compliance

How to Use This Repository
Clone the repository

bash
git clone https://github.com/yourusername/linux-hardening-lab.git
cd linux-hardening-lab
Review the configuration files in the /configs directory

Examine the lab report in the /report directory for detailed procedures

View screenshots in the /screenshots directory for visual verification

Prerequisites
Linux system with root or sudo access

Basic familiarity with Linux command line

Understanding of user account management concepts

Disclaimer
This lab is intended for educational purposes in a controlled environment. Always test security configurations in a non-production environment first and ensure compliance with your organization's security policies before implementing in production.

Author
Forku Brandon - Cyber Security Student at the center of cybersecury and mathemaitcal cryptology

Date
April 14, 2026

License
This project is for educational purposes only.

Acknowledgments
Linux Professional Institute Certification (LPI) for security standards

CIS Benchmarks for system hardening guidelines

Open Source community for documentation and tools

Status: 11 of 12 tasks completed (Task 6 pending)

Last Updated: April 14, 2026

