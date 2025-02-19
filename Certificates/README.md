# Generate Certificates

Create a screts file `.secrets/certbot/cloudflare.ini`. Set the contents to:
```
dns_cloudflare_api_token = ******
```

Check to see if the Cloudflare DNS plugin for certbot is installed:
```shell
source certbotenv/bin/activate
python3 -m pip list
```

If not installed...
```shell
mkdir certbotenv
python3 -m venv certbotenv
source certbotenv/bin/activate
python3 -m pip install certbot-dns-cloudflare
```

GitLab...
```shell
sudo certbot certonly \
  --server https://acme-v02.api.letsencrypt.org/directory \
  -d "gitlab.jbcodes.net" \
  --dns-cloudflare \
  --dns-cloudflare-credentials ~/.secrets/certbot/cloudflare.ini
```

Develocity...
```shell
sudo certbot certonly \
  --server https://acme-v02.api.letsencrypt.org/directory \
  -d "develocity.jbcodes.net" \
  --dns-cloudflare \
  --dns-cloudflare-credentials ~/.secrets/certbot/cloudflare.ini
```

Harbor...
```shell
sudo certbot certonly \
  --server https://acme-v02.api.letsencrypt.org/directory \
  -d "harbor.jbcodes.net" \
  --dns-cloudflare \
  --dns-cloudflare-credentials ~/.secrets/certbot/cloudflare.ini
```

# Places to Renew

## Develocity

## Gitlab

## Harbor

1. Login to the Harbor VM `ssh jeff@192.168.128.133`  VMware1!
1. Copy new certificates as follows:

   - new /etc/letsencrypt/live/harbor.jbcodes.net/fullchain.pem ->  /etc/docker/certs.d/harbor.jbcodes.net/harbor.jbcodes.net.cert
   - new /etc/letsencrypt/live/harbor.jbcodes.net/privkey.pem -> /etc/docker/certs.d/harbor.jbcodes.net/harbor.jbcodes.net.key

1. `cd /harbor`
1. `sudo docker compose down -v`
1. `sudo ./prepare --with-trivy`
1. `sudo docker compose up -d`

## MacOS Keychain

If the LetsEncrypt! intermediate certificates have changed or are not trusted, you will not be able to `docker login`
to Harbor from MacOS. To fix it:

1. Open Mac's Keychain Access Application
1. Select the `login` section
1. Drag the LetsEncrypt! Intermediate certificate into the window (.../chain.pem)
1. Restart Docker
