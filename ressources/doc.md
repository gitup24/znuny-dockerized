root@localhost
changeme

IMAGE_TAG=latest docker compose up -d

#AI Junie :
Root cause: in docker-compose.yml, the PostgreSQL service uses:
image: andreynsafonov/znuny:${IMAGE_TAG}
When IMAGE_TAG is unset, Compose expands it to an empty string, so Docker tries to pull andreynsafonov/znuny: — invalid. The MySQL service already uses the safe default:
image: andreynsafonov/znuny:${IMAGE_TAG:-latest}
Fix it like this:
services:
znuny-postgres:
image: andreynsafonov/znuny:${IMAGE_TAG:-latest}