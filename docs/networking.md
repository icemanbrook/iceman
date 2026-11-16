# Networking and remote access

## Local access

Immich is available on the home network through the host's configured address and port. The local MagicDNS name used in this deployment is:

~~~text
http://iceman:2283
~~~

Do not expose this private address as a public endpoint or assume it resolves outside the home network/tailnet.

## Tailscale

Tailscale is used to connect enrolled family devices to the server without directly publishing Immich to the public internet.

- Install Tailscale on the server and each client device.
- Authenticate devices into the intended tailnet.
- Use MagicDNS for convenient host-name resolution.
- Keep device access limited to trusted family devices and users.
- Review and remove devices that are no longer trusted.

Tailscale encrypts traffic between connected devices. Its coordination/control service still participates in device authentication and connection setup and can process device/network metadata. Treat Tailscale as a trusted service; it is not the storage location for the photo library.

## Firewall

UFW is enabled on the host. Maintain a restrictive inbound policy and allow only the ports and interfaces actually required by the deployment. Verify the current rules on the server before changing them:

~~~bash
sudo ufw status verbose
~~~

Do not copy firewall commands from a generic guide without checking the active SSH and Immich access paths first; an incorrect rule can lock out remote administration.

## Troubleshooting

On Windows, check the Tailscale client:

~~~powershell
tailscale status
tailscale ping iceman
~~~

If the client reports that Tailscale is stopped, start the Tailscale application or its Windows service. On the server, check:

~~~bash
sudo systemctl status tailscaled
tailscale status
~~~

SSH access in this deployment uses the host name:

~~~bash
ssh akshat@iceman
~~~

Use the actual account and host name configured on your own deployment.
