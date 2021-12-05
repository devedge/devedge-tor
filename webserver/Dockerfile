#
# Jekyll server for devedge.github.io
#

# Use jekyll builder image
FROM jekyll/builder:4.0

# Git repo will be downloaded here
WORKDIR /webcontent

# Clone the repo, fix ownership permissions, install deps and build
RUN git clone https://github.com/devedge/devedge.github.io.git \
  && chown -R jekyll:jekyll /webcontent \
  && cd devedge.github.io/ \
  && bundle update \
  && jekyll build

# Run container as jekyll user
USER jekyll

# Set the webserver root directory
WORKDIR /webcontent/devedge.github.io

# Serve the webserver using Jekyll
CMD jekyll serve --config _config.yml,_config-onion.yml --host 0.0.0.0

# To rebuild in order to get the latest version of the website:
# $ docker-compose build --no-cache webserver
# $ docker-compose up --detach --force-recreate webserver
