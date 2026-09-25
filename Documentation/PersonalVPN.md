# Using a Personal VPN with Roam Control

Roam Control's LocalDevVPN can be used at the same time as a separate internet VPN, provided that the second VPN is created by an app using the Personal VPN section of iOS.

This allows LocalDevVPN to remain available for Roam Control while another VPN handles normal internet traffic, privacy, remote access or DNS filtering.

A separate internet VPN is optional and is not required to use Roam Control.

## How the two VPNs coexist

In a working configuration, iOS shows two separate VPN connections:

- **Personal VPN**: your internet or remote-access VPN
- **Device VPN**: LocalDevVPN

Both can remain connected at the same time.

This arrangement has been tested successfully with Roam Control on iOS 27.

## Using a commercial VPN provider

For a commercial VPN provider:

1. Use the provider's own iOS app.
2. Select **IKEv2** if the app provides a protocol selector.
3. Connect the VPN.
4. Open **Settings → VPN**.
5. Check that the provider appears under **Personal VPN**.
6. Confirm LocalDevVPN remains available under **Device VPN**.
7. Confirm normal internet access still works.

If both VPNs remain active and internet access works, the configuration can be used alongside Roam Control.

## Tested VPNs

See [VPN Compatibility](VPNCompatibility.md) for the current list of configurations that have actually been tested alongside LocalDevVPN.

A provider appearing in that list only means that the VPN configuration has been tested for coexistence with LocalDevVPN.

Roam Control does not provide support for the VPN provider itself.

## Manual IKEv2 configurations

Creating an IKEv2 connection directly through:

**Settings → General → VPN & Device Management → VPN → Add VPN Configuration**

is not the recommended Roam Control coexistence method.

During testing, manually created IKEv2 configurations appeared under **Device VPN** instead of **Personal VPN**.

Because LocalDevVPN also uses the Device VPN path, this does not provide the simultaneous VPN arrangement described in this guide.

If your VPN provider supports IKEv2 inside its own iOS app, use the provider's app instead.

## Manual VPN credentials

Some VPN providers offer manual IKEv2 server addresses and credentials.

Roam Control does not assume that these credentials will work with third-party IKEv2 clients.

IKEv2 implementations can differ in areas including:

- authentication methods
- Remote ID requirements
- certificates
- cryptographic proposals
- server configuration

Unless a particular combination has been tested, it should be treated as unconfirmed.

## Self-hosting

Advanced users can run their own IKEv2 VPN server using strongSwan on Linux and connect to it using the tested IKEv2 client.

See [Advanced: Self-hosted IKEv2 on Linux](SelfHostedIKEv2.md).

## Support boundary

Roam Control documents VPN configurations that have been tested for coexistence with LocalDevVPN.

Problems involving a commercial provider's account, servers, credentials, certificates, subscription, protocol availability or provider-specific connection failures should be handled through that provider's support service.
