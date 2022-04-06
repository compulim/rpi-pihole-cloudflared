# pihole-cloudflared

This will run both [Pi-hole](https://pi-hole.net/) and [`cloudflared`](https://hub.docker.com/r/cloudflare/cloudflared) at the same time.

`cloudflared` uses DNS-over-HTTPS, so no one could peak into what you resolved.

## Install Docker and Docker Compose

Note: we will install Docker Compose from PIP3 because it is newer than `apt`.

```sh
# Install Docker

curl -sSL https://get.docker.com | sh
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker pi

# Install Docker Compose

sudo apt update
sudo apt install python3-pip
sudo pip3 install docker-compose

# Cache a version of cloudflared just in case if no DNS is available

cd external
curl -LO https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-arm64.deb

# Reboot

sudo reboot
```

## Configure Pi-hole

Update the `.env` file with corresponding settings.

## Run manually

Run [Pi-hole](https://pi-hole.net/) in Docker Compose.

```sh
docker-compose up --detach
docker-compose logs # to see the default random password
```

### Update password

```sh
docker-compose exec pihole pihole -a -p
```

### Test DNS server

You will need `dig` which can be installed from `dnsutils`.

```sh
sudo apt install dnsutils

dig github.com @localhost
```

### Update Gravity Database

Although Gravity Database is updated on Pi-hole start, to update-on-demand, run:

```sh
docker-compose exec pihole pihole updateGravity
```

### Sync configuration between primary and secondary Pi-hole

There is a `sync` file which help synchronizing configuration between Pi-holes thru `scp`. Please make sure SSH keys are properly set through `~/.ssh/authorized_keys`.

## Run as a service

Modify `pihole.service` to point to the correct directory.

```sh
chmod +x service
sudo ln -s /home/pi/repos/pihole/pihole.service /etc/systemd/system/pihole.service
sudo systemctl enable pihole
sudo systemctl start pihole
```

To update Pi-hole, modify `/etc/crontab` to reboot, or restart the Docker Compose service on a schedule. When the service is started, it will pull the latest [`pihole/pihole`](https://hub.docker.com/r/pihole/pihole) image and rebuild `cloudflared` with latest version.

## References

- [Pi-hole Docker image](https://github.com/pi-hole/docker-pi-hole)
- [Configuring `cloudflared` in Pi-hole](https://docs.pi-hole.net/guides/dns/cloudflared/#configuring-cloudflared-to-run-on-startup)
- [`cloudflared` on Docker Hub](https://hub.docker.com/r/cloudflare/cloudflared)
- [Compose file reference v3](https://docs.docker.com/compose/compose-file/compose-file-v3/)
