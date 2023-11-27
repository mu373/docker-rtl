# docker-rtl

Run [Ride The Lightning](hhttps://github.com/Ride-The-Lightning/RTL/) independently as a container on Docker.

## Prerequisites
- `bitcoind` running on a separate container
    - Use [mu373/docker-bitcoind](https://github.com/mu373/docker-bitcoind)
    - Container name: `bitcoind` (we access RPC using this hostname)
    - Docker network: `bitcoin-nw`
- `LND` running on a separate container
    - Use [mu373/docker-lnd](https://github.com/mu373/docker-lnd)
    - Container name: `lnd` (we access RPC using this hostname)
    - RPC: HTTP(S) REST at port 8080
    - Docker network: `bitcoin-nw`
- [traefik proxy](https://doc.traefik.io/traefik/) running on a separate container
    - Use [mu373/traefik](https://github.com/mu373/traefik)
    - Docker network: `traefik-nw`

## Setup
Prepare configuration for `rtl`
```sh
cp docker-compose-template.yml docker-compose.yml
vim docker-compose.yml # Set lnd path and your custom hostname
cp Sample-RTL-Config.json RTL-Config.json
vim RTL-Config.json # Set the app password under `multiPass` field. Once you login using this password, it will automatically be converted to hashed password.
```

Start the container
```sh
docker compose up -d
```

Access the shell inside the container
```sh
# In host
docker ps # Check container id
docker exec -it container_id bash
```

See logs
```sh
docker logs --tail 100 container_id
```

## Accessing the node
- If you don't need reverse proxy and want to directly access RTL with HTTP:
    - Comment out all the traefik related configs in `docker-compose.yml`
    - Uncomment `ports` config in `docker-compose.yml`
    - RTL should be available at `http://YOUR_IP_ADDRESS:3070`
- When the traefik is properly setup, you can access the RTL at `https://yourrtl.example.com` at port 443.
- Some practical hints
    - Setup [Tailscale](https://tailscale.com/) in the host machine
    - Create `A` and `AAAA` record at `yourserver.example.com`, pointing to the host Tailscale IP
    - Create `CNAME` record at `yourrtl.example.com`, pointing to `yourserver.example.com`
    - Tweak traefik proxy configs in `docker-compose.yml`
    - This way, you don't have to configure https and certificates within the RTL container yourself. Traefik works as a reverse proxy and does all the complicated stuffs for you.

## References
- Options for `RTL-Config.json` is listed [here](https://github.com/Ride-The-Lightning/RTL/blob/d083be11965d285c39070c2e04e7e3f86570c6e8/.github/docs/Application_configurations.md)

## License
[MIT](https://github.com/mu373/docker-rtl/blob/main/LICENSE)
