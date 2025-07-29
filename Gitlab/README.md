# Gitlab Installation

## VM Setup

Create a VM with 8 vCPU, 16GB RAM, 512 GB Disk using Ubuntu Server (not minimal)

Configure network as follows:

- IP Address: 192.168.128.150
- Netmask: 255.255.255.0
- Gateway: 192.168.128.1
- DNS: 192.168.128.1

```shell
sudo apt install qemu-guest-agent
```

You also need to enable QEMU guest agent in the VM options, and you will likely need to stop and restart the VM.

## Install Gitlab

Follow the basic instructions here: https://about.gitlab.com/install/#ubuntu (Don't need to install a mail server as we'll use the
relay on Synology)

```shell
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.deb.sh | sudo bash
```

Install (this takes a few minutes and will end with an error about ACME certificates)
```shell
 sudo EXTERNAL_URL="https://gitlab.jbcodes.net" apt-get install gitlab-ee
```

## Change the default root password
 
Get the defaut password:
```shell
sudo cat /etc/gitlab/initial_root_password
```

Signin as `root` with this password, then use the UI to change the password to VMware1!

## Setup SMTP

```shell
sudo vim /etc/gitlab/gitlab.rb
```

```ruby
gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = 'synology.jbcodes.net'
gitlab_rails['smtp_port'] = 25
gitlab_rails['smtp_user_name'] = 'jeff'
gitlab_rails['smtp_password'] = 'jeff'
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_domain'] = 'gitlab.jbcodes.net'
gitlab_rails['smtp_tls'] = false
gitlab_rails['smtp_openssl_verify_mode'] = 'none'
gitlab_rails['smtp_enable_starttls_auto'] = false
gitlab_rails['smtp_ssl'] = false
gitlab_rails['smtp_force_ssl'] = false

gitlab_rails['gitlab_email_from'] = 'gitlab@gitlab.jbcodes.net'
gitlab_rails['gitlab_email_display_name'] = 'Gitlab'
gitlab_rails['gitlab_email_reply_to'] = 'noreply@gitlab.jbcodes.net'
```

```shell
sudo gitlab-ctl reconfigure
```

### Test Email Setup

```shell
gitlab-rails console
Notify.test_email('jeffgbutler@gmail.com', 'Gitlab SMTP Test', 'This is a test').deliver_now
```

## Update the certificates

```shell
sudo mkdir -p /etc/gitlab/ssl
sudo chmod 755 /etc/gitlab/ssl/
```

copy certs to `/etc/gitlab/ssl/gitlab.jbcodes.net.key` and `/etc/gitlab/ssl/gitlab.jbcodes.net.crt`

```shell
sudo vim /etc/gitlab/ssl/gitlab.jbcodes.net.crt
```

```shell
sudo vim /etc/gitlab/ssl/gitlab.jbcodes.net.key
```

```shell
sudo vim /etc/gitlab/gitlab.rb
```

```ruby
letsencrypt['enable'] = false
```

```shell
sudo gitlab-ctl reconfigure
```

If the new certs still aren't showing, than:

```shell
sudo gitlab-ctl hup nginx
sudo gitlab-ctl hup registry
```


## SMTP Setup

Encode the userid/password for SMTP:
```shell
echo -en "\0jeff\0jeff" | base64
```

```shell
telnet synology.jbcodes.net 25
EHLO gitlab
AUTH PLAIN    -- then add the encoded userid/password from above
MAIL FROM: <xxx@gmail.com>
RCPT TO: <xxx@gmail.com>
DATA
From: xxx@gmail.com
To: xxx@gmail.com
Subject: Relay Test

This is the body.

.
```
