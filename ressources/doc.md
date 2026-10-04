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

#
Junie:

ERROR
ERROR 🔴 build failed err=build image: build failed: failed to solve: failed to compute cache key: failed to calculate checksum of ref mjl2lpobmyyswn69aem7c9d4s::yuyh0f3y80jz54eduxn6fuy2m: "/znuny-7.3.1.tar.gz": not found
Ask Zeabur Agent
Copy
{
"message": "\u001b[31mERROR\u001b[0m 🔴 build failed \u001b[2merr=build image: build failed: failed to solve: failed to compute cache key: failed to calculate checksum of ref mjl2lpobmyyswn69aem7c9d4s::yuyh0f3y80jz54eduxn6fuy2m: \"/znuny-7.3.1.tar.gz\": not found\u001b[0m",
"timestamp": "2026-10-04T11:43:18.130134Z",
"__typename": "BuildLog"
}
### Summary
- Fixed Docker build failure caused by missing `/znuny-7.3.1.tar.gz` in build context.

### Changes
- Updated `Dockerfile` to download the Znuny archive directly from `https://download.znuny.org/releases/znuny-${ZNUNY_VERSION}.tar.gz` via `curl` instead of using `COPY`.
- Updated `Dockerfile.production` similarly to fetch the archive online within its single combined build step.

### Verification
- Executed `docker build -f Dockerfile -t znuny:test .` and verified successful image compilation and extraction.