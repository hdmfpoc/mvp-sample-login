FROM node:alpine
# Add an docker user, make work dir
RUN adduser --disabled-password --gecos "" docker && \
  echo "docker ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers && \
  mkdir -p /home/docker/login-api && \
  chown docker:docker /home/docker/login-api

WORKDIR /home/docker/login-api

COPY . ./

RUN npm install

# RUN as docker user
USER docker
CMD node server