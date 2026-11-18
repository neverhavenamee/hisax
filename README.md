# HiSAX

**HiSAX detects APT (advanced persistent threat) activity in network flow data, stage by stage, and explains every alert.**

Upload a CSV of network flows and HiSAX shows which hosts look compromised, at which attack stage, and why. It runs on
your own server: no telemetry, no calls to the outside, no data leaves your network.

- **Stage-aware detection**: alerts are tied to attack stages, not just "anomalous".
- **Explanations**: each alert shows which flow features drove it.
- **Self-hosted**: one signed bundle, installs offline, one command.
- **Your login**: sign in through your own OIDC provider (Keycloak, Entra ID, Okta...) in Enterprise mode.

## Download

From the [latest release](../../releases/latest) download:

| File | Purpose |
|---|---|
| `hisax-<version>.tar` | The installation bundle (all images included) |
| `hisax-<version>.tar.sha256` | Checksum |
| `release-public-key.pem` | Public key to verify the signature |

Verify before installing:

```sh
sha256sum -c hisax-<version>.tar.sha256
openssl pkey -pubin -in release-public.pem -outform DER | openssl dgst -sha256
# must print: 78d837dbd7f4e65bb7fd848253366e66b4095d87d9a24d8b6ffd1b1fa334bd0a
tar -xf hisax-<version>.tar && (cd hisax-<version> && bash verify_release.sh .)   # "Bundle verified"
```

## Install

You need a Linux server with Docker Engine and the `docker compose` plugin, 4 GB RAM and 10 GB free disk.

```sh
sudo bash hisax-<version>/install.sh hisax-<version>
```

The installer asks which mode to set up:

| Mode | For | Needs |
|---|---|---|
| **1. Quickstart** (default) | Trying it out, ready in a minute | Nothing. No sign-in and plain HTTP, so it listens on this machine only unless you confirm otherwise |
| **2. Enterprise** | Real use | OIDC issuer URL, client ID and secret, TLS certificate and key. HTTPS and sign-in |

It prints the address to open when it is done. Unattended install: pass a config file as the second argument
(`install.sh hisax-<version> hisax.conf`; a template is in the bundle).

## Use

Open the address, upload a flow CSV (or run the built-in sample scan), and browse Overview, Alerts, Hosts and the
explanations.

Manage the install with `hisax` (run with `sudo`):

```sh
sudo hisax status                    # version, services, disk
sudo hisax stop                      # stop; data is kept
sudo hisax start                     # start again
sudo hisax logs                      # follow logs (Ctrl-C to quit)
sudo hisax backup                    # back up the database
sudo hisax upgrade hisax-<new version>.tar   # verify, back up, upgrade; rolls back by itself if unhealthy
sudo hisax rollback                  # go back to the previous version
sudo hisax help                      # all commands
```

Configuration is in `/opt/hisax/hisax.conf`, the install log in `/opt/hisax/install.log`. For help, run
`sudo hisax support-bundle` (secrets are redacted; review it before sending).
