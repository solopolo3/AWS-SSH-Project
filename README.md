# AWS SSH EC2 Server Setup-

# Launch a default Amazon Linux Instance
# During setup, create a key pair via the Launch Instance menu, and download the private key to client device
# Move .pem private key file to .ssh directory on client device
# Connect to Instance via SSH Client in local terminal
# ssh -i <local private key location> <server user@IP>

# Server Side:
# Setup automatic updates on server
# sudo yum install dnf-automatic
# edit /etc/dnf/automatic.conf file to enable automatic updates

# enable updater
# sudo systemctl enable --now dnf-automatic.timer

# Client Side:
# create 2 new keys with unique file names and setting passphrases for both
# ssh-keygen -t ed25519

# ssh-copy-id cannot be run because the EC2 instance has no user password to connect via password authentication, so the keys must be copied manually by connecting to the server via the default key pair

# Server Side:
# manually copy both public keys into the ~/.ssh/authorized_keys file
# edit /etc/ssh/sshd_config, changing to a custom port, disabling root login and keeping password authenticaiton disabled
# create a new security inbound rule in aws portal (firewall), changing port range to 2048
# refresh ssh to apply changes
# sudo systemctl restart sshd
# sudo systemctl daemon-reload

# Client Side:
# edit ~/.ssh/config file, creating a new alias for the server instance, setting the new custom port, and attaching a private key to the alias
# Host aws
#    Hostname <server IP>
#    Port <custom port>
#    user <user>
#    IdentityFile <private key location>
#    IdentitiesOnly yes

# connect to server with each new key, entering passphrase when prompted
# ssh -i <private key location> <server alias>

# connect to server without requiring a key argument, as it is attached to the alias
# ssh <server alias>

# install and enable fail2ban to block brute force attack attempts
# sudo dnf install fail2ban
# sudo systemctl enable fail2ban

# make a copy of the /etc/fail2ban/jail.conf file called jail.local
# edit the jail.local file, adding autoban settings under sshd

# enabled = true
# port    = <custom ssh port>
# backend = systemd
# maxretry = 3
# findtime = 300
# bantime = 3600
# ignoreip = 127.0.0.1

# restart fail2ban and verify that it is active
# sudo systemctl restart fail2ban
# sudo systemctl status fail2ban

# if the service is not enabled, run the enable command
# sudo systemctl enable fail2ban
