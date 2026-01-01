# sudo nerdctl build . -t ghcr.io/master-hash/naiveproxy:2.10.2-cloudflare --platform linux/amd64,linux/arm64/v8,linux/ppc64le,linux/riscv64,linux/s390x --output type=oci,compression=zstd,compression-level=22,force-compression=true
# sudo nerdctl push ghcr.io/master-hash/naiveproxy:2.10.2-cloudflare

FROM --platform=${BUILDPLATFORM} caddy:2.11.0-beta.1-builder-alpine AS builder
ARG TARGETOS
ARG TARGETARCH

RUN --mount=type=cache,target=/root/.cache/go-build \
	go env -w GOMODCACHE=/root/.cache/go-build && \
	# go env -w GOPROXY=https://goproxy.io,direct && \
	go env -w GOAMD64=v3 && go env -w GO111MODULE=on && \
	go env -w GOEXPERIMENT=greenteagc && \
	GOOS=${TARGETOS} GOARCH=${TARGETARCH} xcaddy build v2.11.0-beta.1 \
	--with github.com/caddy-dns/cloudflare \
	# --with github.com/caddyserver/forwardproxy=github.com/klzgrad/forwardproxy@naive
	--with github.com/caddyserver/forwardproxy=github.com/zedifen/forwardproxy@naive
# --with github.com/caddyserver/forwardproxy=github.com/imgk/forwardproxy@udpinhttp
# --with github.com/caddyserver/forwardproxy=github.com/aUsernameWoW/forwardproxy@udpintcp


COPY ./Caddyfile /etc/caddy/Caddyfile

# RUN \
# 	caddy fmt /etc/caddy/Caddyfile --overwrite

FROM --platform=${TARGETPLATFORM} caddy:2.11.0-beta.1-alpine

COPY --link --from=builder /usr/bin/caddy /usr/bin/caddy
# COPY ./localhost.direct.key /localhost.direct.key
# COPY ./localhost.direct.crt /localhost.direct.crt
COPY --link --from=builder /etc/caddy/Caddyfile /etc/caddy/Caddyfile
ADD index.html.* /usr/share/caddy/
