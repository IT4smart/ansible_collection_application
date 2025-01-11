FROM python:3.9.18-slim-bullseye

ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=${USER_UID}

LABEL maintainer "Raphael Lekies <raphael.lekies@it4smart.eu> (IT4smart GmbH)"

COPY files/requirements.txt /tmp/requirements.txt
COPY files/requirements-dev.txt /tmp/requirements-dev.txt

RUN apt-get update -qq \
    && apt-get install -y --no-install-recommends \
        unzip \
        jq \
        git \
        sshpass \
        openssh-client \
        sudo \
        ca-certificates \
        curl \
        rsync \
    && groupadd --gid ${USER_GID} ${USERNAME} \
    && useradd --uid ${USER_UID} --gid ${USER_GID} -m ${USERNAME} -s /bin/bash \
    && groupadd docker \
    && usermod -aG docker ${USERNAME} \
    && usermod -aG sudo ${USERNAME} \
    && echo 'vscode ALL=(ALL) NOPASSWD: /bin/chmod' >> /etc/sudoers.d/${USERNAME} \
    && chmod 0440 /etc/sudoers.d/${USERNAME} \
    && install -m 0755 -d /etc/apt/keyrings \
    && curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc \
    && chmod a+r /etc/apt/keyrings/docker.asc \
    && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null \ 
    && apt-get update -qq \
    && apt-get install -y --no-install-recommends \
        docker-ce-cli \
    && apt-get autoremove --purge -y \
    && apt-get -y clean \
    && rm -rf /var/lib/apt/lists/* \
    && pip install --upgrade pip setuptools \
    && pip install -r /tmp/requirements-dev.txt --no-cache-dir

USER ${USERNAME}

RUN mkdir ~/.ssh \
    && echo "IdentityFile /mnt/ssh/id_ed25519" >> ~/.ssh/config