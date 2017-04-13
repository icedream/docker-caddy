# Caddy Docker image

This image simply provides Caddy precompiled and -installed, ready to use out
of the box. Additionally, it allows for simple extension with plugins.

## Available tags

All available tags are always listed [in Docker Hub](https://hub.docker.com/r/icedream/caddy/tags), the list below explains the maintained tags:

- `latest`: Latest version of this image (intended to target `master` branch of Caddy).
- `stable`, `0`, `0.9`, `0.9.5`: Latest stable version (last release of Caddy).

## About Caddy

Caddy is a modern implementation of a web server with a focus on security and
automation with ease of configuration.

You can find out more about Caddy on the official website at
https://caddyserver.com/.

## Features

- Based on Alpine Linux
- Transparent usage (the Caddy binary itself is the entrypoint of this image)
- Easy installation of plugins through scripts shipped with the image

## Usage

This image does not tweak Caddy itself in any way so it will technically run
exactly as if you were running Caddy directly on the machine aside from the
usual Docker isolation.

Caddy needs at least a folder containing a website in order to run in a way
that makes sense. You can mount such a website in as a volume like this:

```sh
docker run -d -p "2015:2015" -w /data -v /path/to/your/website:/data icedream/caddy
```

Your website should then be available at http://localhost:2015 according to the
[official Getting Started guide](https://caddyserver.com/docs/getting-started).
Configuration can then be done as usual via your own `Caddyfile` in the working
directory.

## Extension

### Custom image

At some point you may want to put your website code into a custom Docker image
to go along with Caddy itself. You can realize this idea using a new `Dockerfile`
which contains at least the following lines:

```dockerfile
FROM icedream/caddy

WORKDIR /data
COPY . /data/
```

### Installing plugins

Additionally to just copying your website into the Docker image you can install
a custom set of plugins and let the Docker image automatically rebuild Caddy
with all these plugins for you:

```dockerfile
RUN docker-caddy-install-plugin \
  "https://github.com/captncraig/cors.git" \
  "https://github.com/pyed/ipfilter.git#6b25e48ff3eef8d894f6fca240463f726ee7f7eb" &&\
  rm -r "${GOPATH}"
```

Supported syntaxes for sources where to install plugins from are:

- `protocol://server.com/user/repository.git` (will use latest commit on default branch)
- `protocol://server.com/user/repository.git#reference` (reference can be any branch name, tag, commit hash, etc. to select a specific version of a plugin)

Supported protocols are the same that Git supports (`git`, `https` and `http`).

The last line makes sure to delete the Go workspace folder as an attempt on
saving space in the final image output.
