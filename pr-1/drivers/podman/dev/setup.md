---
title: Podman Driver
description: Run OpenShell sandboxes as rootless Podman containers on the gateway host.
url: /openshell-driver-podman/pr-1/drivers/podman/dev/setup/
docset: driver-podman
version: dev
latest_version: 0.1.0
source: "pimlock/openshell-driver-podman@PR #1, b3c43eb:docs/setup.mdx"
---

> Documentation index: /openshell-driver-podman/pr-1/drivers/podman/dev/llms.txt. This page is for Podman driver dev.
> Preview build of pimlock/openshell-driver-podman (PR #1, b3c43eb); not published. Published version (0.1.0): https://pimlock.github.io/openshell-docs/drivers/podman/0.1.0/setup.md

# Podman Driver

[Podman](https://podman.io/) runs sandboxes as rootless containers on the gateway host. It requires Podman 5.x, cgroups v2, and an active Podman user socket:

```shell
systemctl --user start podman.socket
```

```toml
[openshell.gateway]
compute_driver = "podman"
```

Common options in `[openshell.drivers.podman]` are `socket_path`, `network_name`, `sandbox_runtime_image`, `supervisor_image`, `image_pull_policy`, `stop_timeout_secs`, and `grpc_endpoint`. When `socket_path` is unset, the driver finds the socket automatically, including `podman machine` sockets on macOS.

On macOS, set `host_gateway_ip` only if your Podman machine uses a non-standard host-loopback address. Set `grpc_endpoint` when the gateway is remote.

For networks that require a corporate proxy, set `https_proxy`, `no_proxy`, and related `proxy_*` options. See the [Gateway Configuration File](https://pimlock.github.io/openshell-docs/dev/how-it-works/gateways/configuration/) reference.

To trust a private CA for direct, policy-inspected HTTPS egress without a corporate proxy — for example, because the supervisor container cannot inherit a private CA baked into the workload image — set `additional_ca_bundle`. Both the supervisor and the workload trust the CA for all destinations, so use a CA you control. See the [Gateway Configuration File](https://pimlock.github.io/openshell-docs/dev/how-it-works/gateways/configuration/) reference for details.

## Mounts

Podman supports the same `volume`, `tmpfs`, and `bind` mounts as [Docker](https://pimlock.github.io/openshell-docs/drivers/docker/dev/setup/#mounts), using the `podman` key in driver config, and the same bind-mount warning applies. Podman `volume` mounts do not support `subpath`.

Podman also supports `image` mounts, which mount a container image read-only at `target`. Image mounts require disabling resource admission.

Podman 5.x is required.
