# Ansible Role: Traefik

Ansible role for deploying Traefik reverse proxy via Docker Compose v2.

## Requirements

- [community.docker](https://galaxy.ansible.com/ui/repo/published/community/docker) Ansible collection

On the remote host:

- Docker API >= 1.25
- Docker CLI with Docker compose plugin 2.18 or later
- Python `requests` library

## Role Variables

### General

| Variable | Type | Default | Description |
| --- | --- | --- | --- |
| `traefik_image_registry` | _string_ | `docker.io` | Docker registry |
| `traefik_image_repository` | _string_ | `traefik` | Image repository |
| `traefik_image_tag` | _string_ | `v3.6.13` | Image tag |
| `traefik_docker_network` | _string_ | `proxy` | Docker network to create and attach Traefik to |
| `traefik_compose_dir` | _string_ | `/opt/traefik` | Directory for compose and config files |

### Dashboard

| Variable | Type | Default | Description |
| --- | --- | --- | --- |
| `traefik_api_dashboard` | _bool_ | `false` | Enable dashboard |
| `traefik_dashboard_host` | _string_ | `traefik.example.com` | Dashboard hostname |
| `traefik_dashboard_tls` | _bool_ | `true` | Enable TLS for dashboard |

### TLS

```yaml
traefik_tls_certificates:
  - cert: cert.pem
    key: key.pem
    subdir: traefik.example.com # optional
```

### Extra labels

Additional Docker labels to apply to the Traefik container:

```yaml
traefik_extra_labels:
  traefik.http.middlewares.dashboard-auth.basicauth.users: "user:$$apr1$$..."
  traefik.http.routers.dashboard.middlewares: "dashboard-auth@docker"
```

For full list of variables see [defaults/main.yml](defaults/main.yml) and [meta/argument_specs.yml](meta/argument_specs.yml).

## Example Playbook

Common playbook:

```yaml
- hosts: all
  vars:
    traefik_api_dashboard: true
    traefik_dashboard_host: traefik.example.org
    traefik_metrics_prometheus: true
    traefik_tls_certificates:
      - cert: cert.pem
        key: key.pem
    traefik_extra_labels:
      traefik.http.middlewares.dashboard-auth.basicauth.users: "user:$apr1$H6uskkkW$IgXLP6ewTrSuBkTrqE8wj/"
      traefik.http.routers.dashboard.middlewares: "dashboard-auth@docker"
  roles:
    - mephs.traefik
```

Used private registry:

```yaml
- hosts: all
  vars:
    traefik_image_registry: nexus.example.com
    traefik_image_repository: proxy/traefik
    traefik_image_tag: v3.5.2
  roles:
    - mephs.traefik
```

## License

[MIT](LICENSE)

## Author Information

Created and maintained by Mikhail Vorontsov (@mephs) <mvorontsov@tuta.io>
