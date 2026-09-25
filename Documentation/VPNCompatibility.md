# VPN Compatibility

This page records VPN configurations that have actually been tested alongside Roam Control's LocalDevVPN.

A service supporting IKEv2 does not automatically mean that it will use the Personal VPN section of iOS.

## Confirmed configurations

| Provider or client | Tested configuration | Account tested | Personal VPN | LocalDevVPN coexistence |
| --- | --- | --- | --- | --- |
| Windscribe | Native iOS app using IKEv2 | Pro | Confirmed | Confirmed |
| PrivadoVPN | Native iOS app using IKEv2 | Free | Confirmed | Confirmed |
| hide.me | Native iOS app | Free | Confirmed | Confirmed |
| IKEv2 by Brooog LTD | Self-hosted strongSwan server | Self-hosted | Confirmed | Confirmed |

## Manual iOS IKEv2 profiles

Manual IKEv2 profiles created directly in iOS Settings are not currently considered a supported Roam Control coexistence method.

Testing showed that these profiles are placed under **Device VPN**, where they use the same VPN path as LocalDevVPN.

## Third-party IKEv2 clients

Manual VPN credentials from commercial VPN providers are not considered compatible simply because they use IKEv2.

Credentials that work with Apple's built-in IKEv2 client may require options that another IKEv2 app does not expose.

Compatibility should only be marked as confirmed after an actual connection test.

## How to contribute a compatibility result

If you test another VPN provider, include:

- provider name
- iOS app version
- protocol selected
- whether it appears under Personal VPN
- whether LocalDevVPN remains connected
- whether normal internet access works

Do not include passwords, private keys, account IDs or other credentials.

## Support boundary

Roam Control documents VPN configurations that have been tested alongside LocalDevVPN.

Problems involving a commercial provider's account, servers, credentials, certificates, subscription, protocol availability or provider-specific connection failures should be handled through that provider's support service.
