# Advanced: Self-hosted IKEv2 on Linux

> [!IMPORTANT]
> This is an advanced, optional setup.
>
> You do not need to run an IKEv2 server to use Roam Control.
>
> This guide is for users who specifically want a self-hosted Personal VPN that can remain connected alongside LocalDevVPN.

This guide creates an IPv4 full-tunnel IKEv2 VPN using strongSwan on Linux.

On iPhone, the VPN is connected using **IKEv2 by Brooog LTD**. This creates a Personal VPN connection, allowing it to remain connected alongside Roam Control's LocalDevVPN.

This configuration has been tested successfully using:

- Debian 13
- Raspberry Pi
- strongSwan 6
- `charon-systemd`
- `swanctl`
- EAP-MSCHAPv2 authentication
- NAT-T
- an IPv4 VPN address pool
- Pi-hole as the VPN DNS server
- iOS 27
- IKEv2 by Brooog LTD
- LocalDevVPN connected simultaneously

Other Linux distributions should also be capable of running this configuration, but package names, firewall management and service configuration may differ.

## 1. Before you begin

You need:

- a Linux machine that remains online
- root or sudo access
- a fixed LAN IP for the Linux server
- a public internet connection capable of receiving inbound connections
- a DNS or DDNS hostname pointing to your connection
- access to your router's port-forwarding settings
- an iPhone or iPad
- the IKEv2 app by Brooog LTD

The examples below use:

```text
VPN hostname: vpn.example.com
VPN username: vpnuser
VPN subnet:   10.123.178.0/24
VPN pool:     10.123.178.10-10.123.178.250
VPN DNS:      192.168.1.2
Network NIC:  eth0
```

Do not copy these values blindly.

Replace `vpn.example.com` with your own DNS or DDNS hostname, replace `192.168.1.2` with the DNS server you want VPN clients to use, and check whether your network interface is actually called `eth0`.

If your ISP uses CGNAT and does not provide a reachable public IPv4 address, normal router port forwarding may not work.

## 2. Choose a VPN subnet

The VPN address pool must not overlap with:

- your home LAN
- another VPN
- Docker networks
- another private network the client may need to reach

Check existing routes:

```bash
ip route
```

Do not reuse a range that already appears there.

For example, if your home LAN is `192.168.1.0/24`, a separate range such as `10.123.178.0/24` can be used for IKEv2 clients.

## 3. Preflight checks

Check the operating system and network configuration:

```bash
cat /etc/os-release
ip -br addr
ip route
```

Find the interface used for the default route:

```bash
ip route | grep '^default'
```

Check whether UDP ports 500 and 4500 are already in use:

```bash
sudo ss -lunp | grep -E ':(500|4500)\b' || \
  echo "UDP 500 and 4500 currently unused"
```

If another IPsec or IKE service already owns these ports, stop and investigate before continuing.

## 4. Install strongSwan

On Debian 13 and similar Debian-based systems:

```bash
sudo apt update

sudo apt install -y \
  charon-systemd \
  strongswan-swanctl \
  strongswan-pki \
  libcharon-extauth-plugins \
  libcharon-extra-plugins \
  iptables \
  openssl
```

Check that strongSwan is available:

```bash
swanctl --version
```

## 5. Define the VPN settings

Run:

```bash
VPN_HOST="vpn.example.com"
VPN_USER="vpnuser"

VPN_SUBNET="10.123.178.0/24"
VPN_POOL="10.123.178.10-10.123.178.250"

VPN_DNS="192.168.1.2"
WAN_IF="eth0"

VPN_PASSWORD="$(openssl rand -hex 24)"

printf '\nVPN password: %s\n\n' "$VPN_PASSWORD"
```

Save the generated VPN password somewhere secure.

The variables are only available in the current shell session. If you close the terminal before completing the setup, define them again before continuing.

### DNS

`VPN_DNS` is the DNS server supplied to VPN clients.

For Pi-hole users, this can be the reachable LAN address of the Pi-hole server.

## 6. Create the certificate authority

Create a private working directory:

```bash
PKI_DIR="$HOME/ikev2-pki"

install -d -m 700 "$PKI_DIR"
cd "$PKI_DIR"

umask 077
```

Generate the CA private key:

```bash
pki --gen \
  --type rsa \
  --size 3072 \
  --outform pem \
  > ikev2-ca-key.pem
```

Create the root CA certificate:

```bash
pki --self \
  --ca \
  --lifetime 3650 \
  --in ikev2-ca-key.pem \
  --type rsa \
  --dn "CN=Roam Control IKEv2 Root CA" \
  --outform pem \
  > ikev2-ca-cert.pem
```

## 7. Create the VPN server certificate

Generate the server private key:

```bash
pki --gen \
  --type rsa \
  --size 3072 \
  --outform pem \
  > ikev2-server-key.pem
```

Issue the server certificate:

```bash
pki --pub \
  --in ikev2-server-key.pem \
  --type rsa \
| pki --issue \
  --lifetime 1825 \
  --cacert ikev2-ca-cert.pem \
  --cakey ikev2-ca-key.pem \
  --dn "CN=${VPN_HOST}" \
  --san "${VPN_HOST}" \
  --flag serverAuth \
  --outform pem \
  > ikev2-server-cert.pem
```

Check the certificate:

```bash
pki --print --in ikev2-server-cert.pem
```

Confirm that the hostname shown in the certificate matches the hostname the iPhone will use to connect.

## 8. Install the certificates

```bash
sudo install -m 600 \
  "$PKI_DIR/ikev2-server-key.pem" \
  /etc/swanctl/private/ikev2-server-key.pem

sudo install -m 644 \
  "$PKI_DIR/ikev2-server-cert.pem" \
  /etc/swanctl/x509/ikev2-server-cert.pem

sudo install -m 644 \
  "$PKI_DIR/ikev2-ca-cert.pem" \
  /etc/swanctl/x509ca/ikev2-ca-cert.pem
```

### Protect the CA private key

`ikev2-ca-key.pem` is the private key for your certificate authority.

Do not distribute it or install it on your iPhone.

Make a secure offline backup. You will need it if you later need to issue or renew certificates.

After confirming the backup, remove the server copy if you do not need it there.

## 9. Configure strongSwan

Create the VPN configuration:

```bash
sudo tee /etc/swanctl/conf.d/roam-ikev2.conf >/dev/null <<EOF
connections {
    roam-ikev2 {
        version = 2
        local_addrs = 0.0.0.0

        pools = roam-ikev2-pool

        send_cert = always
        fragmentation = yes
        mobike = yes

        local {
            auth = pubkey
            certs = ikev2-server-cert.pem
            id = ${VPN_HOST}
        }

        remote {
            auth = eap-mschapv2
            eap_id = %any
        }

        children {
            roam-ikev2 {
                local_ts = 0.0.0.0/0
                remote_ts = dynamic
                dpd_action = clear
            }
        }
    }
}

pools {
    roam-ikev2-pool {
        addrs = ${VPN_POOL}
        dns = ${VPN_DNS}
    }
}

secrets {
    eap-user {
        id = ${VPN_USER}
        secret = ${VPN_PASSWORD}
    }
}
EOF

sudo chmod 600 /etc/swanctl/conf.d/roam-ikev2.conf
```

## 10. Enable IPv4 forwarding

```bash
echo 'net.ipv4.ip_forward=1' \
  | sudo tee /etc/sysctl.d/99-ikev2.conf

sudo sysctl --system

sysctl net.ipv4.ip_forward
```

Expected:

```text
net.ipv4.ip_forward = 1
```

## 11. Configure VPN NAT

Create a persistent systemd service:

```bash
sudo tee /etc/systemd/system/ikev2-nat.service >/dev/null <<EOF
[Unit]
Description=IKEv2 VPN NAT
After=network-online.target
Wants=network-online.target

[Service]
Type=oneshot
RemainAfterExit=yes

ExecStart=/bin/sh -c '/usr/sbin/iptables -t nat -C POSTROUTING -s ${VPN_SUBNET} -o ${WAN_IF} -m comment --comment ikev2-nat-rule -j MASQUERADE 2>/dev/null || /usr/sbin/iptables -t nat -A POSTROUTING -s ${VPN_SUBNET} -o ${WAN_IF} -m comment --comment ikev2-nat-rule -j MASQUERADE'

ExecStop=/bin/sh -c '/usr/sbin/iptables -t nat -C POSTROUTING -s ${VPN_SUBNET} -o ${WAN_IF} -m comment --comment ikev2-nat-rule -j MASQUERADE 2>/dev/null && /usr/sbin/iptables -t nat -D POSTROUTING -s ${VPN_SUBNET} -o ${WAN_IF} -m comment --comment ikev2-nat-rule -j MASQUERADE || true'

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable --now ikev2-nat.service

sudo iptables -t nat -S POSTROUTING | grep ikev2-nat-rule
```

## 12. Check forwarding firewall policy

```bash
sudo iptables -S FORWARD
```

If the FORWARD policy is restrictive, your firewall must permit traffic from the VPN subnet out through the internet-facing interface and established or related return traffic back to VPN clients.

Do not disable your firewall simply to make the VPN work.

Firewall configuration differs between distributions and firewall managers, so adapt these rules to your existing firewall rather than installing a second firewall system on top of it.

## 13. Allow IKEv2 through the host firewall

The Linux server must accept:

```text
UDP 500
UDP 4500
```

If UFW is already being used:

```bash
sudo ufw allow 500/udp
sudo ufw allow 4500/udp
```

Do not install or enable UFW solely for this guide if your machine already uses another firewall system.

## 14. Configure the router

Forward:

```text
UDP 500  -> Linux VPN server
UDP 4500 -> Linux VPN server
```

Use the server's fixed LAN address as the destination.

Do not expose unrelated ports.

## 15. Start strongSwan

```bash
sudo systemctl enable --now strongswan

sudo swanctl --load-all

sudo swanctl --list-conns
sudo swanctl --list-certs
sudo swanctl --list-pools
```

The configured connection should appear as `roam-ikev2`.

## 16. Export the CA certificate for iOS

```bash
cd "$PKI_DIR"

openssl x509 \
  -in ikev2-ca-cert.pem \
  -outform DER \
  -out ikev2-ca-cert.cer
```

Transfer only `ikev2-ca-cert.cer` to the iPhone.

Never transfer either private key.

## 17. Trust the CA on iPhone

Open `ikev2-ca-cert.cer` on the iPhone and install the configuration profile.

Then open:

**Settings → General → About → Certificate Trust Settings**

Enable full trust for the IKEv2 root CA.

Only install and trust a CA certificate that you created and control.

## 18. Configure the iPhone client

Install **IKEv2 by Brooog LTD** from the App Store:

[IKEv2 by Brooog LTD](https://apps.apple.com/gb/app/ikev2/id1542583818)

This is the client used for the tested Roam Control self-hosted configuration.

Create a connection using:

```text
Server:   vpn.example.com
Username: vpnuser
Password: your generated VPN password
```

Use the same hostname that appears in the VPN server certificate.

Connect the VPN.

## 19. Confirm Personal VPN coexistence

Open the VPN section of iOS Settings.

The intended configuration is:

```text
Personal VPN
    IKEv2        Connected

Device VPN
    LocalDevVPN  Connected
```

Both should remain connected.

Test normal internet access.

For this full-tunnel configuration, an external IPv4 check should show the public IPv4 address of the network hosting the Linux VPN server.

## 20. Verify the connection on Linux

```bash
sudo swanctl --list-sas
```

A successful connection should contain an IKE SA marked `ESTABLISHED`, a CHILD_SA and a client address allocated from the configured VPN pool.

## 21. Final verification

Before considering the setup complete, confirm:

- IKEv2 connects successfully from cellular
- the connection appears under Personal VPN
- LocalDevVPN remains connected under Device VPN
- normal internet access works
- the external IPv4 address matches the VPN server's internet connection
- DNS resolves through the intended DNS server
- `swanctl --list-sas` shows an established IKE SA
- a client address is assigned from the configured VPN pool
- the connection still works after rebooting the Linux server

## 22. Check the logs

```bash
sudo journalctl \
  -u strongswan \
  --since "5 minutes ago" \
  --no-pager
```

A successful EAP-MSCHAPv2 connection normally reaches stages equivalent to:

```text
EAP_MSCHAPV2 succeeded
authentication with EAP successful
assigning virtual IP
IKE_SA established
CHILD_SA established
```

## 23. Troubleshooting

### Nothing reaches the server

```bash
sudo ss -lunp | grep -E ':(500|4500)\b'
```

Confirm:

- DDNS resolves to the correct public IP
- UDP 500 is forwarded
- UDP 4500 is forwarded
- the Linux firewall permits both ports
- the ISP is not blocking inbound connections
- the connection is not behind unusable CGNAT

### Authentication fails

```bash
sudo journalctl \
  -u strongswan \
  --since "5 minutes ago" \
  --no-pager \
  | grep -Ei 'auth|eap|mschap|failed'
```

Verify the username and password match the EAP secret in `roam-ikev2.conf`.

### Certificate validation fails

Check that:

- the CA certificate is installed on iOS
- full trust is enabled for the CA
- the connection uses the hostname contained in the server certificate
- the DDNS hostname resolves correctly

Do not work around certificate failures by disabling certificate validation.

### VPN connects but internet does not work

```bash
sysctl net.ipv4.ip_forward

sudo iptables -t nat -S POSTROUTING | grep ikev2

sudo iptables -S FORWARD
```

### VPN connects but DNS fails

```bash
sudo swanctl --list-pools
```

If using Pi-hole, make sure Pi-hole accepts queries originating from the VPN path.

### Repeated half-open IKE_SA messages

A client that repeatedly retries an incomplete connection can temporarily create several half-open IKE sessions.

Stop repeated connection attempts, allow the old sessions to time out, then investigate the authentication or certificate failure causing the retries.

Do not increase strongSwan's half-open limits simply to hide a broken connection attempt.

## 24. IPv6

This tested configuration is intentionally IPv4-only.

The VPN traffic selector is:

```text
0.0.0.0/0
```

and the VPN address pool contains IPv4 addresses only.

IPv6 was disabled in the environment used to develop and test this setup because it interfered with the intended DNS and ad-blocking behaviour.

This does not mean that every Roam Control user should disable IPv6.

If your normal connection provides IPv6, do not assume that IPv6 traffic is being carried through this VPN.

Either configure proper IPv6 support in strongSwan or make an informed decision about disabling IPv6 in your own environment.

Test both IPv4 and IPv6 behaviour before relying on this VPN for filtering or privacy.

## 25. Reboot test

```bash
sudo reboot
```

After the server returns:

```bash
systemctl is-active strongswan
systemctl is-active ikev2-nat.service

sudo swanctl --list-conns

sudo iptables -t nat -S POSTROUTING \
  | grep ikev2-nat-rule
```

Reconnect the iPhone and verify internet access again.

Do not consider the installation complete until the VPN survives a server reboot.

## 26. Remove the VPN server

Disable strongSwan:

```bash
sudo systemctl disable --now strongswan
```

Disable and remove the NAT service:

```bash
sudo systemctl disable --now ikev2-nat.service

sudo rm -f /etc/systemd/system/ikev2-nat.service
sudo systemctl daemon-reload
```

Remove the Roam Control IKEv2 configuration:

```bash
sudo rm -f /etc/swanctl/conf.d/roam-ikev2.conf
```

Remove the installed VPN certificate and private key if no longer required:

```bash
sudo rm -f \
  /etc/swanctl/private/ikev2-server-key.pem \
  /etc/swanctl/x509/ikev2-server-cert.pem \
  /etc/swanctl/x509ca/ikev2-ca-cert.pem
```

Do not delete your offline CA backup unless you are certain that you will never need certificates issued by that CA again.

Remove router port forwards and firewall rules that were created specifically for this VPN.
