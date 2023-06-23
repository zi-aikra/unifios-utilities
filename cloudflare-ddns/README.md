# Cloudflare Dynamic DNS

## Features

- Update Multiple Subdomains
- Proxy your traffic through cloudflare
- Set a ttl

Complete feature list and documentation can be found [here](https://github.com/timothymiller/cloudflare-ddns)

## Requirements

1. You have successfully setup the on boot script described [here](https://github.com/unifi-utilities/unifios-utilities/tree/main/on-boot-script)
2. You must have a cloudflare profile with at least one domain.
3. You must have a valid cloudflare api token with correct permissions (see [complete documentation](https://github.com/timothymiller/cloudflare-ddns) for details)

## Customization

Update [config.json](configs/config.json) with the following options:

- your cloudflare api token
- your zone id
- each subdomain you'd like to point at your udm-pro
- Set the proxied flag if you'd like cloudflare to proxy the records
- Set the ttl value you'd like for your records

## Steps 

### (UnifiOS $\leq$ 2.x)

1. Make a directory for your configuration, check if you have `/mnt/data/` or `/data/` and adjust accordingly.

   ```sh
   mkdir -p /data/cloudflare-ddns
   ```

2. Create a [cloudflare-ddns configuration](configs/config.json) in `/data/cloudflare-ddns` and update the configuration to meet your needs.
3. Copy [30-cloudflare-ddns.sh](on_boot.d/30-cloudflare-ddns.sh) to `/data/on_boot.d`.
4. Execute the script `. /data/on_boot.d/30-cloudflare-ddns.sh`


### Useful commands

```sh
# view cloudflare-ddns logs to verify the container is running without error (ipv6 warnings are normal).
podman logs cloudflare-ddns
```

### (UnifiOS $\geq$ 3.x)

###  Option 1: If you want to use udm-boot
1. Make sure you used [on-boot-script-2.x](https://github.com/unifi-utilities/unifios-utilities/tree/main/on-boot-script), not [on-boot-script](https://github.com/unifi-utilities/unifios-utilities/tree/main/on-boot-script)
2. Follow Steps 1 - 4 from [UnifiOS-2.x steps](#steps-unifios-leq-2x) above.
3. [30-cloudflare-ddns.sh](on_boot.d/30-cloudflare-ddns.sh) will run once and terminate. This is normal. Script will run again on next udm-boot tick.

###  Option 2: If you want to use systemd service and timer for more timing control
1. Update [cloudflare-ddns.timer](systemd-service/cloudflare-ddns.timer) to meet your needs.
   - OnBootSec: How long to wait after boot before starting the service
   - OnUnitActiveSec: How long to wait after the service has stopped before restarting it
2. Copy [cloudflare-ddns.service](systemd-service/cloudflare-ddns.service) and [cloudflare-ddns.timer](systemd-service/cloudflare-ddns.timer) to `/etc/systemd/system/`
3. Run `mkdir -p /data/cloudflare-ddns`
4. Copy [config.json](configs/config.json), [30-cloudflare-ddns.sh](on_boot.d/30-cloudflare-ddns.sh) to `/data/cloudflare-ddns`
5. Update [config.json](configs/config.json) to meet your needs.
6. Execute the script `. /data/30-cloudflare-ddns.sh`

### Useful commands

```sh
# view cloudflare-ddns logs to verify the service is running without error (ipv6 warnings are normal).
journalctl -eu cloudflare-ddns
```