# Ackee (LazyCat LPK)

Self-hosted, cookie-free website analytics.

- Upstream: https://github.com/electerious/Ackee
- Image: `electerious/ackee:3.7.1` (Docker Hub, mirror mode, digest-matched)
- License: MIT (full text in `content/licenses/LICENSE`)
- Containers: Ackee + MongoDB 7

## Env contract (read from `src/utils/config.js`)

The app reads a runtime Proxy over `process.env`, so these names are exact:

| var | used for |
|---|---|
| `ACKEE_MONGODB` | Mongo connection URI (falls back to `MONGODB_URI`) |
| `ACKEE_USERNAME` / `ACKEE_PASSWORD` | UI login |
| `ACKEE_PORT` | listen port (default 3000) |
| `ACKEE_AUTO_ORIGIN` | derive tracking origin from the request host |

## Notes

- Port 3000, user `ackee`, entry `docker-entrypoint.sh` → `npm run start`.
- Login comes from deploy params (`ACKEE_USERNAME` / `ACKEE_PASSWORD`); no built-in credential.
- **Mongo is deliberately credential-free in the URI.** A generated password can contain
  `@`, `/`, `?` and YAML templates cannot percent-encode it, which would corrupt the URI. The
  database therefore has no published port and no upstream — it is reachable only on the app's
  internal container network — and the UI itself is gated by the deploy-param login.
- `--bind_ip 127.0.0.1,mongo` keeps the database listener on the internal network only.
- `ACKEE_AUTO_ORIGIN=true` so the generated tracking snippet uses the visiting host instead of
  requiring a manually typed domain.
- Mongo data persists on `/lzcapp/var/mongo`.
- Not yet verified on a real device.
