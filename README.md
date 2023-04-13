# Telemetry generator

## Prerequisites & setup

### Non-development Workflow: Run a published image

If you just want to run (vs build or develop), you can run the most recent image published to this repository's container registry.

For defaults, see `Dockerfile`.

```shell
export LS_ACCESS_TOKEN=your token
```
```shell
# can override to any other OTLP endpoint
export OTEL_EXPORTER_OTLP_TRACES_ENDPOINT=ingest.lightstep.com:443
```
```shell
docker run -e LS_ACCESS_TOKEN --rm ghcr.io/lightstep//telemetry-generator:latest
```

### OpenTelemetry collector builder
Install the `opentelemetry-collector-builder`; this is deprecated but its replacement does not work with the old version of the collector we're still pinned to.
   1. `$ cd /tmp` (or wherever you like to keep code)
   1. `$ git clone https://github.com/open-telemetry/opentelemetry-collector-builder`
   1. `$ cd opentelemetry-collector-builder`
   1. `$ git checkout v0.35.0`
   1. `$ go get -u golang.org/x/sys`
   1. `$ go install .`

### Get the code
1. Clone the [telemetry generator repo](https://github.com/lightstep/telemetry-generator) to a directory of your choosing:
   1.  `$ cd ~/Code` (or wherever)
   1.  `$ git clone https://github.com/lightstep/telemetry-generator`
   1.  `$ cd telemetry-generator`
1. Copy `hipster_shop.yaml` to `dev.yaml` for local development. Not strictly necessary but will potentially save heartache and hassle 😅 This file is in .gitignore, so it won't be included in your commits. If you want to share config changes, add them to a new example config file.
   `$ cp examples/hipster_shop.yaml examples/dev.yaml`

## Environment variables

### Access token

To send telemetery data to Lightstep, you'll need an access token associated with the lightstep project you want to use. Go to ⚙ -> Access Tokens to copy an existing one or create a new one. Then:

```shell
export LS_ACCESS_TOKEN="<your token>"
```

### Collector endpoint

The env var `OTEL_EXPORTER_OTLP_TRACES_ENDPOINT` determines the endpoint for traces and metrics. To send data to Lightstep, use:

```shell
export OTEL_EXPORTER_OTLP_TRACES_ENDPOINT=ingest.lightstep.com:443
```

### Topo file (generatorreceiver config)

The env var `TOPO_FILE` determines which config file the generatorreceiver uses.

If you use the opentelemetry-collector-builder you'll want to point to `examples/<filename.yaml>`:

```shell
export TOPO_FILE=examples/dev.yaml
```

For Docker builds, these files are copied to `/etc/otel/`, so set `TOPO_FILE` like this:

```shell
export TOPO_FILE=/etc/otel/dev.yaml
```

## Build and run the collector

There are two options here, but if possible we recommend using the opentelemetry-collector-builder, which is much faster and lets you test config changes without rebuilding. With the Docker build method, you need to rebuild the image for all changes, code or config, and the build process takes much longer.

### Build and run with the opentelemetry-collector-builder (recommended)

(You must first install the `opentelemetry-collector-builder`; see Prerequisites above.)
```shell
opentelemetry-collector-builder --config config/builder-config.yml
```
```shell
build/telemetry-generator --config config/collector-config.yml
```

When using the builder, you only need to re-run the first command for code changes; for config changes just re-run the second command. To run with a different topo file, change the `TOPO_FILE` environment variable.

If you run into errors while building, please open [an issue](https://github.com/lightstep/telemetry-generator).

### Build and run with Docker (alternative)
```shell
docker build -t lightstep/telemetry-generator:latest .
```
```shell
docker run --rm -e LS_ACCESS_TOKEN -e OTEL_EXPORTER_OTLP_TRACES_ENDPOINT -e TOPO_FILE lightstep/telemetry-generator:latest
```

When building with Docker, you need to re-run both steps for any code *or* config changes. If you run into errors while building, please open [an issue](https://github.com/lightstep/telemetry-generator).

## Troubleshoot

- getting error `error   service/collector.go:230        Asynchronous error received, terminating process       {"error": "listen tcp :8888: bind: address already in use"}`
  - you probably already have an otel-collector process running, stop it with command:
  ```shell
  sudo systemctl stop otelcol-contrib
  ```
  or
  ```shell
  sudo systemctl stop otelcol
  ```

- getting error: `Error: failed to compile the OpenTelemetry Collector distribution: exit status 2. Output: "# github.com/lightstep/telemetry-generator/generatorreceiver/internal/topology\n../generatorreceiver/internal/topology/pickable.go:18:6: missing function body\n../generatorreceiver/internal/topology/pickable.go:18:23: syntax error: unexpected [, expecting (\n../generatorreceiver/internal/topology/pickable.go:20:2: syntax error: non-declaration statement outside function body\n../generatorreceiver/internal/topology/pickable.go:39:2: syntax error: non-declaration statement outside function body\nnote: module requires Go 1.18\n"`
  - upgrade to go version 1.18

- getting error: `xcrun: error: invalid active developer path (/Library/Developer/CommandLineTools), missing xcrun at: /Library/Developer/CommandLineTools/usr/bin/xcrun`
  - if on MAC OS, download and install the latest `Command line tools OS X` for your MACOS from site: https://developer.apple.com/download/all/

- getting error: `make: builder: No such file or directory
make: *** [build] Error 1`
  - no solution found, except to reinstall and launch directly `make build` without installing the prerequiste first (as it is included in make build)


- getting error `cannot use path@version syntax in GOPATH mode`
  - Install latest version of go with instructions here https://nextgentips.com/2021/12/23/how-to-install-go-1-18-on-ubuntu-20-04/ (tested working with version 1.18)
  - you may have to install wget for this to work using `sudo apt-get install wget`
