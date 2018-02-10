# Adding Security

## Block root access

Open the SSH configuration:  
```
sudo nano /etc/ssh/sshd_config
```

Search for the string "PermitRootLogin" and change the value from "yes" to "no":  
```
PermitRootLogin yes
```

Search for the string "PasswordAuthentication", remove the hash and change the value from "yes" to "no":  
```
PasswordAuthentication
```

Restart the SSH service:  
```
sudo service ssh restart
```

## Update packages

Update the default packages:  
```
sudo apt-get update && sudo apt-get dist-upgrade
```

## Install fail2ban

Install fail2ban and sendmail:  
```
sudo apt-get install fail2ban sendmail
```

Create local fail2ban.conf file:  
```
sudo nano /etc/fail2ban/fail2ban.local
```

Paste the following settings:  
```
[Definition]

# Set the log level output.
#
#   CRITICAL
#   ERROR
#   WARNING
#   NOTICE
#   INFO
#   DEBUG

loglevel = WARNING
```

Create a local jail.conf file:  
```
sudo nano /etc/fail2ban/jail.local
```

Paste the following settings:  
```
[DEFAULT]

# "bantime" is the number of seconds that a host is banned.
bantime = 1200

# A host is banned if it has generated "maxretry" during the last "findtime" seconds.
findtime = 600

# "maxretry" is the number of failures before a host get banned.
maxretry = 3

# Destination email address used
destemail = {your email address}

# Sender email address used solely for some actions
sender = root@{your hostname}

[ssh]

enabled  = true
port     = ssh
filter   = sshd
logpath  = /var/log/auth.log
maxretry = 5

```

Restart the fail2ban service:  
```
sudo service fail2ban restart
```

## Install and configure a firewall

Install the uncomplicated firewall ([ufw](https://wiki.ubuntu.com/UncomplicatedFirewall)):
```
sudo apt-get install ufw
```

Run the following commands to disallow all traffic that is not coming to a webserver or for the SSH connection:

```bash
sudo ufw allow ssh
sudo ufw allow 80
sudo ufw allow 443
```

Enable the firewall and accept the warning, that you might kick yourself out, as we won't do that.

```
sudo ufw enable
```

Check the configuration:  
```
sudo ufw status
```

The result should look similar to this:  
```
Status: active

To                         Action      From
--                         ------      ----
22                         ALLOW       Anywhere
80                         ALLOW       Anywhere
443                        ALLOW       Anywhere
22 (v6)                    ALLOW       Anywhere (v6)
80 (v6)                    ALLOW       Anywhere (v6)
443 (v6)                   ALLOW       Anywhere (v6)
```

If you want to see all ufw commands, run the following:  
```
sudo ufw --help
```

## Install logwatch

```
sudo apt-get install logwatch
```

Open the configuration:  
```
sudo nano /usr/share/logwatch/default.conf/logwatch.conf
```

Search for the string "MailTo" and change the value from "root" to your own email address:
```
MailTo = {your email}
```

Search for the string "MailFrom" and change the value to include the hostname:
```
MailFrom = Logwatch on {your hostname}
```

## Harden the network layer

Open the network configuration file:  
```
sudo nano /etc/sysctl.conf
```

Uncomment the following lines:  
```
net.ipv4.conf.default.rp_filter=1
net.ipv4.conf.all.rp_filter=1

net.ipv4.tcp_syncookies=1

net.ipv4.conf.all.accept_redirects = 0
net.ipv6.conf.all.accept_redirects = 0

net.ipv4.conf.all.accept_source_route = 0
net.ipv6.conf.all.accept_source_route = 0

net.ipv4.conf.all.log_martians = 1
```


<div style="border-top: 2px solid #a0a0a0; margin-top: 2.5rem; padding-top: 1rem;">
    <div style="float: left">
        &laquo;
        <a href="./adding-a-new-user.md">
            Adding a new user
        </a>
    </div>
    <div style="float: right;">
        <a href="./install-nodejs.md">
            Install Node.js
        </a>
        &raquo;
    </div>
</div>