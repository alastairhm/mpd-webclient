# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [Unreleased]

### Added

- `CLAUDE.md` documenting the repo for Claude Code.

### Fixed

- Pinned the base image to `alastairhm/alpine-lighttpd:3.3` (was `:latest`). The
  `:latest` tag now resolves to a current Alpine release whose repos no longer carry
  `php-mcrypt` and `php-xmlrpc` (both dropped from Alpine after PHP moved on from
  bundling mcrypt/XML-RPC), so the build failed outright. `3.3` matches the PHP 5.6
  package set the Dockerfile was originally written against.

## [1.0.0] - 2016-04-25

### Added

- `Dockerfile` building an Alpine/Lighttpd/PHP-FPM image that clones
  [sn0opy/MPD-Webinterface](https://github.com/sn0opy/MPD-Webinterface) and patches it
  to reach MPD/Icecast via the `icy` hostname instead of `localhost`.
- `lighttpd.conf` serving the web client on port 80.
- `docker-compose.yml` example stack linking the web client to an Icecast/MPD backend.
- `README.md`.
- Switched the base image to `alastairhm/alpine-lighttpd` on Docker Hub.
