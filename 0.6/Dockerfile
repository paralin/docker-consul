FROM container4armhf/armhf-alpine:edge
MAINTAINER James Phillips <james@hashicorp.com> (@slackpad)

# This is the release of Consul to pull in.
ENV CONSUL_VERSION=0.6.4

# This is the release of https://github.com/hashicorp/docker-base to pull in order
# to provide HashiCorp-built versions of basic utilities like dumb-init and gosu.
ENV DOCKER_BASE_VERSION=0.0.4

# Create a consul user and group first so the IDs get set the same way, even as
# the rest of this may change over time.
RUN addgroup consul && \
    adduser -S -G consul consul && \
    apk add --no-cache ca-certificates

ADD dumb-init /usr/bin/dumb-init
ADD consul /usr/bin/consul

# The /consul/data dir is used by Consul to store state. The agent will be started
# with /consul/config as the configuration directory so you can add additional
# config files in that location.
RUN mkdir -p /consul/data && \
    mkdir -p /consul/config && \
    chown -R consul:consul /consul

# Expose the consul data directory as a volume since there's mutable state in there.
VOLUME /consul/data

# Server RPC is used for communication between Consul clients and servers for internal
# request forwarding.
EXPOSE 8300

# Serf LAN and WAN (WAN is used only by Consul servers) are used for gossip between
# Consul agents. LAN is within the datacenter and WAN is between just the Consul
# servers in all datacenters.
EXPOSE 8301 8301/udp 8302 8302/udp

# CLI, HTTP, and DNS (both TCP and UDP) are the primary interfaces that applications
# use to interact with Consul.
EXPOSE 8400 8500 8600 8600/udp

ENTRYPOINT ["/usr/bin/dumb-init", "/usr/bin/consul"]

# By default you'll get a single-node development server that stores everything
# in RAM, exposes a web UI, and bootstraps itself. Don't use this configuration
# for production.
CMD ["agent", "-dev"]
