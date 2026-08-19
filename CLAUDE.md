# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

This repo does **not** contain the web client's application code. It is a thin Docker
packaging layer that builds an Alpine/Lighttpd/PHP-FPM image and, at build time, clones
the actual web client (https://github.com/sn0opy/MPD-Webinterface) into the image. The
entire repo is four files:

- `Dockerfile` — builds `FROM alastairhm/alpine-lighttpd:latest`, installs the PHP
  extensions the web client needs, clones `sn0opy/MPD-Webinterface` into `/var/www2`,
  and patches `index.php` (`sed 's/localhost/icy/g'`) so the client connects to the
  Icecast/MPD host by its Docker Compose service name (`icy`) instead of `localhost`.
- `lighttpd.conf` — serves `/var/www2` on port 80 via `www-data`, with PHP handled
  through `mod_fastcgi.conf` (included from the base image, not present in this repo).
- `docker-compose.yml` — example stack: an `icecast` service (`alastairhm/docker-icecast`,
  ports 8000/6600, mounts a music library) linked as `icy`, plus this image's `frontend`
  service on port 80.
- `README.md` — one-line description and a pointer to the upstream client.

There is no application source, no test suite, no linter, and no package manifest in
this repo. Changes here are about the Docker image build and runtime config, not about
web client behavior — to change client behavior, changes belong upstream in
sn0opy/MPD-Webinterface, or as an additional `sed`/patch step in the `Dockerfile`.

## Common commands

Build the image:
```
docker build -t mpd-webclient .
```

Run the example stack (edit the music volume path in `docker-compose.yml` first):
```
docker compose up
```

There is no build/lint/test tooling to run beyond a Docker build — verify changes by
building the image and checking the container serves pages on port 80.

## Key details to keep in mind when editing

- The `icy` hostname baked into `index.php` via `sed` must match the Icecast/MPD service
  name used in whatever `docker-compose.yml` (or link/network) the image is run with. If
  the compose service alias changes, the `sed` pattern in the `Dockerfile` needs to change
  too.
- `server.document-root` in `lighttpd.conf` (`/var/www2/`) must match where the
  `Dockerfile` clones/moves the web client files.
- The base image `alastairhm/alpine-lighttpd` and `mod_fastcgi.conf` it provides are
  external dependencies not visible in this repo.
