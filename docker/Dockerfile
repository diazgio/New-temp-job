#
# https://hub.docker.com/r/iamteacher/rails7/tags
#
# ARM:
# docker build -t iamteacher/rails7:2023.arm64 -f Dockerfile --build-arg BUILDPLATFORM="linux/arm64" ../
# docker push iamteacher/rails7:2023.arm64
# docker run -ti iamteacher/rails7:2023.arm64
#
# AMD:
# docker build -t iamteacher/rails7:2023.amd64 -f Dockerfile --build-arg BUILDPLATFORM="linux/amd64" ../
# docker push iamteacher/rails7:2023.amd64
# docker run -ti iamteacher/rails7:2023.amd64
#
# 386:
# (crashed on M1 with) `qemu: uncaught target signal 6 (Aborted) - core dumped`
# docker build -t iamteacher/rails7:2023.386 -f Dockerfile --build-arg BUILDPLATFORM="linux/386" ../
# docker run -ti iamteacher/rails7:2023.386

# Debian GNU/Linux 11 (bullseye)
FROM --platform=$BUILDPLATFORM ruby:3.2
ARG BUILDPLATFORM
RUN echo "$BUILDPLATFORM" > /BUILDPLATFORM

SHELL ["/bin/bash", "--login", "-c"]

RUN gem update --system 3.4.2

# =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
# My Lucky User
# =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

# CREATE GROUP `lucky`
RUN groupadd -g 7777 lucky

# CREATE USER `lucky`
RUN adduser lucky \
    -u 7777 \
    --ingroup lucky \
    --home /home/lucky \
    --shell /bin/bash \
    --disabled-password --gecos ''

# Assign user / groups
RUN usermod -G lucky root
RUN usermod -G lucky lucky

RUN chown -R lucky:lucky /usr/local/bin/ruby
RUN chown -R lucky:lucky /usr/local/bundle

# rails credentials:edit
RUN apt-get update && apt-get install vim --yes
ENV EDITOR="vim"

# =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
# NODE.JS
# =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

ENV NVM_DIR="/opt/.nvm"
RUN mkdir /opt/.nvm
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.2/install.sh | bash
RUN nvm install 18.12.1

RUN chown -R lucky:lucky /opt/.nvm

# =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
# Loging as Lucky user
# =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

USER lucky
ENV EDITOR="vim"
WORKDIR /home/lucky/app
SHELL ["/bin/bash", "--login", "-c"]

# =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
# Register NODE.JS for this user
# =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

RUN touch ~/.bashrc
ENV NVM_DIR="/opt/.nvm"
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.2/install.sh | bash

# =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
# Install Gems
# =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

COPY Gemfile Gemfile
COPY --chown=lucky:lucky Gemfile.lock Gemfile.lock
RUN bundle
