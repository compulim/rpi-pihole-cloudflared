# pihole-cloudflared

## First-run

Run Pi-hole in Docker Compose.

```sh
docker-compose up -d
docker-compose logs # to see the default random password
```

Pi-hole DNS server will be hosted at tcp://localhost:53/.

Then, navigate to http://localhost/ to go to the admin dashboard.

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
