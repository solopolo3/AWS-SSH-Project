# AWS SSH EC2 Server Setup-
#
# Launch a default Amazon Linux instance. During setup, create a key pair via the Launch Instance menu, and download the private key and move it to the .ssh directory on the client device. Connect to the server instance via SSH Client in local terminal:
# ssh -i <local private key location> <server user@IP>
#
# Server Side: Setup automatic updates on the server:
# sudo yum install dnf-automatic
# Edit /etc/dnf/automatic.conf to enable automatic updates, and enable the automatic updater:
# sudo systemctl enable --now dnf-automatic.timer
#
# Client Side: Create 2 new keys with unique file names and set passphrases for both:
# ssh-keygen -t ed25519

# ssh-copy-id cannot be run because the EC2 instance has no user password to connect via password authentication, so the keys must be copied manually by connecting to the server via the default key pair.
#
# Server Side: Manually copy both public keys into the ~/.ssh/authorized_keys file. Edit /etc/ssh/sshd_config, changing to a custom port, disabling root login and keeping password authentication disabled. Create a new security inbound rule in AWS portal (AWS's firewall), changing port range to 2048 so that connections to that port aren't blocked, then refresh ssh in terminal to apply changes:
# sudo systemctl restart sshd
# sudo systemctl daemon-reload
#
# Client Side: Edit ~/.ssh/config, creating a new alias for the server instance, setting the new custom port, and attaching a private key to the alias:
# Host <alias>
#    Hostname <server IP>
#    Port <custom port>
#    user <user>
#    IdentityFile <private key location>
#    IdentitiesOnly yes
#
# Connect to the server with each new key, entering passphrase when prompted:
# ssh -i <private key location> <server alias>
#
# Attempt connection to the server without a key argument, as it is attached to the alias:
# ssh <server alias>
#
# Install and enable fail2ban to block brute force attack attempts:
# sudo dnf install fail2ban
# sudo systemctl enable fail2ban
#
# Make a copy of the /etc/fail2ban/jail.conf file called jail.local - Edit the jail.local file, adding autoban settings under sshd:
# enabled = true
# port    = <custom ssh port>
# backend = systemd
# maxretry = 3
# findtime = 300
# bantime = 3600
# ignoreip = 127.0.0.1
#
# Restart fail2ban and verify that it is active. If the service is not enabled, run the enable command.
# sudo systemctl restart fail2ban
# sudo systemctl status fail2ban
# sudo systemctl enable fail2ban
