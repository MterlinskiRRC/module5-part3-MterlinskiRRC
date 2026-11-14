# Pixel River Financial Bank Services – Multi-language Microservices (Module 5 – Part 3)

| Service | Tech | Internal port | Purpose |
|---|---|---|---|
| `studentportfolio` (compose name `frontend`) | static HTML + Bootstrap on nginx:alpine | 80 | Portfolio page + link to the bank |
| `backend` | Python 3.10 / Flask | 5000 | Register, login, dashboard, deposit, withdraw, logout, session-based `/api/transactions` |
| `transactions` | Node 18 / Express + MongoDB driver | 3000 | Monthly-grouped transaction report: `GET /api/transactions/<userId>` |
| `mongo` | mongo:6 | 27017 | Shared database `bank_app` |
| `nginx` | nginx:alpine | **80 (public)** | Reverse proxy / single entry point |

## Nginx routing (`nginx.conf`)
| Request | Goes to |
|---|---|
| `/api/transactions/<userId>` | `transactions:3000` (JavaScript service) |
| `/api/...` (everything else, incl. `/api/transactions`) | `backend:5000` (Flask) |
| `/` and static files | `frontend:80` |

## Run locally
```powershell
cd part3
docker compose up --build -d
```
Visit http://localhost/ → Banking App → register / login / deposit / withdraw.
Get a user id: `docker compose exec mongo mongosh bank_app --eval "db.users.findOne()"`, then open
`http://localhost/api/transactions/<that _id>`.

## CI
`.github/workflows/ci.yml` runs on every push / PR to `main`: installs backend dependencies, runs the
pytest suite (mongomock – no database needed), installs the Node service dependencies, and builds every Docker image.

## Vagrant
`Vagrantfile` provisions an Ubuntu VM with Docker and starts the stack:
```powershell
cd part3
vagrant up          # http://localhost:8080 on the host -> nginx in the VM
vagrant ssh -c "cd /vagrant && docker compose ps"
vagrant destroy -f
```
