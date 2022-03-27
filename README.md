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

# Reboot

sudo reboot
```

## Run manually

Run [Pi-hole](https://pi-hole.net/) in Docker Compose.

```sh
docker-compose up --detach
docker-compose logs # to see the default random password
```

Then, navigate to http://localhost/ to go to the admin dashboard.

To test Pi-hole as DNS server:

```sh
sudo apt install dnsutils

dig github.com @localhost
```

## Run as a service

Modify `pihole.service` to point to the correct directory.

```sh
chmod +x service
sudo ln -s /home/pi/repos/pihole/pihole.service /etc/systemd/system/pihole.service
sudo systemctl enable pihole.service
sudo systemctl start pihole
```

To update Pi-hole, modify `/etc/crontab` to reboot, or restart the Docker Compose service on a schedule. When the service is started, it will pull latest [`pihole/pihole`](https://hub.docker.com/r/pihole/pihole) image and rebuild `cloudflared` with latest versions.

### Change password

To update password, run:

```sh
docker-compose exec pihole pihole -a -p
```

### Update Gravity Database

By default, Gravity Database will be updated on boot. To update manually, while the container is running, run:

```sh
docker-compose exec pi-hole pihole updateGravity
```

## References

- [Pi-hole Docker image](https://github.com/pi-hole/docker-pi-hole)
- [Configuring `cloudflared` in Pi-hole](https://docs.pi-hole.net/guides/dns/cloudflared/#configuring-cloudflared-to-run-on-startup)
- [`cloudflared` on Docker Hub](https://hub.docker.com/r/cloudflare/cloudflared)
- [Compose file reference v3](https://docs.docker.com/compose/compose-file/compose-file-v3/)
