#
# Run tor and expose the control port
#

# Latest stable slim debian image
FROM debian:stretch-slim

# Copy the Tor Project's apt source URLs
COPY tor-sources.list /tmp

# Update packages and install tor
# Source: https://2019.www.torproject.org/docs/debian.html.en
RUN apt-get update && apt-get install -y \
  # The apt source lines with https require apt-transport-https
  apt-transport-https \
  # Download gnupg2 and curl to retrieve the Tor Project's official keys
  gnupg2 \
  curl \
  # Now that apt-transport-https is downloaded, the tor apt sources can be moved
  # to the apt sources directory
  && mv /tmp/tor-sources.list /etc/apt/sources.list.d/ \
  # Retrieve and import the gpg key. Force gpg to trust the key, so packages can be verified
  # with it
  && curl https://deb.torproject.org/torproject.org/A3C4F0F979CAA22CDBA8F512EE8CBC9E886DDD89.asc | gpg --import --trust-model always \
  && gpg --export A3C4F0F979CAA22CDBA8F512EE8CBC9E886DDD89 | apt-key add - \
  # Remove curl, since it is no longer needed and can be a security risk
  && apt-get remove --purge -y \
  curl \
  # Refresh the sources to pull the tor debian repo, and install both tor and
  # the official keyring
  && apt-get update && apt-get install -y \
  tor \
  deb.torproject.org-keyring

# Expose ports for the control port
EXPOSE 9051

# Copy custom torrc
COPY torrc.conf /etc/tor/torrc

# Copy HiddenService keys into the container
COPY onionv3/* /var/lib/tor/hidden_service/

# Create the tor user, group, auth cookie, and chown everything under /etc/tor
RUN groupadd -r tor \
  && useradd --no-log-init -r -g tor tor \
  && chown -R tor:tor /etc/tor /var/lib/tor /var/log/tor \
  && chmod 700 /var/lib/tor/hidden_service/ \
  && chmod 600 /var/lib/tor/hidden_service/hs_ed25519_secret_key

# Run container as tor user
USER tor

# Container entrypoint command is tor
ENTRYPOINT [ "tor" ]

# On container run, load custom torrc
CMD [ "-f", "/etc/tor/torrc" ]
