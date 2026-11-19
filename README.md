# ct-plates

Cinematic Tutor study plates, served at **https://plates.iconstudio.tech**.

| Path | What |
|---|---|
| `/` | Index of plates |
| `/plate01/` | Plate 01 · The microphone (single self-contained file) |
| `/plate01/host-demo.html` | Mr. Aly reference host (drives the plate with postMessage) |
| `/plate01/INTEGRATION.md` | Integration guide for the Cinematic Tutor app |

Deployment: `deploy/docker-compose.yml` runs on the ICON Studio VPS as the Docker project `plates`.
An nginx container downloads these files from this repo at a pinned commit and serves them
behind the existing Traefik proxy (automatic HTTPS). To publish a new version: push, then
update the commit in the compose file and redeploy the project.
