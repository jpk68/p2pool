## Running P2Pool with I2P

There are several command-line options that should be used for I2P setup:

- `--socks5 IP:port` to specify your I2P SOCKS proxy address (likely `127.0.0.1:4447` if you followed the guide below.)
- `--no-dns` to disable all DNS queries and prevent DNS leaks. P2Pool only ever makes DNS requests to get a list of seed nodes, to resolve your Monero node's domain (if it's not set as an IP address), and to resolve manually added peers
- `--no-upnp` to disable UPnP requests (they are sent to your router, so use this option if you are not on your home network)
- `--i2p-address` your hidden service's address (without port number). This address will be broadcast to other peers when you mine a share in P2Pool. This is to prevent address spamming - you have to mine a real share to be able to broadcast your I2P address.
- `--no-clearnet-p2p` to never connect to clearnet P2Pool nodes. This also makes sure that your P2Pool traffic doesn't exit the I2P network (and is not seen/modified by the exit nodes)

## Setting up a proxy and hidden service for P2Pool (i2pd on Linux)

You will need to modify your `tunnels.conf` file, usually found in `/var/lib/i2pd/`.

First, [create a SOCKS tunnel](https://docs.i2pd.website/en/latest/user-guide/tunnels/#socks-proxy) for outbound connections. Add these lines to `tunnels.conf`:

```
[socks]
type = socks
address = 127.0.0.1
port = 4447
keys = socks-keys.dat
```

Next, create an inbound tunnel to allow peers to connect to your P2Pool node. Add these lines to `tunnels.conf`:

```
[p2pool]
type = server
host = 127.0.0.1
port = 28723
keys = p2pool.dat
```

Restart i2pd: `sudo systemctl restart i2pd`

Use the following command to get your hidden service's address:
```
curl -s http://127.0.0.1:7070/?page=i2p_tunnels | grep -Eo "[a-zA-Z0-9./?=_%:-]*" | grep "28723"
```

This will give you the address to use when running P2Pool.

## Command line example
```
./p2pool --host MONERO_NODE_IP --wallet YOUR_WALLET --socks5 127.0.0.1:9050 --no-dns --no-upnp --i2p-address ADDRESS_FROM_CURL_COMMAND
```
