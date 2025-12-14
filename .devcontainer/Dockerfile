FROM ubuntu:24.04

ARG USERNAME=ubuntu
ARG UID=1000
ARG GID=1000
ARG DOCKER_GID=1001

# Avoid interactive prompts during build
ENV DEBIAN_FRONTEND=noninteractive

# Update and install base packages
RUN apt-get update && apt-get install -y --no-install-recommends \
  build-essential \
  ca-certificates \
  curl \
  git \
  gnupg \
  lsb-release \
  openssh-client \
  software-properties-common \
  sudo \
  tini \
  vim \
  wget \
  && rm -rf /var/lib/apt/lists/*

# Install Node.js LTS (latest stable)
RUN curl -fsSL https://deb.nodesource.com/setup_lts.x | bash - && \
  apt-get install -y nodejs && \
  rm -rf /var/lib/apt/lists/*

# Create non-root user (or use existing ubuntu if it exists)
RUN if ! id -u ${USERNAME} >/dev/null 2>&1; then \
      groupadd -g ${GID} ${USERNAME} && \
      useradd -m -u ${UID} -g ${GID} -s /bin/bash ${USERNAME}; \
    fi && \
  usermod -aG sudo ${USERNAME} && \
  echo "${USERNAME} ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# Add user to docker group (for Docker socket access)
RUN groupmod -g ${DOCKER_GID} docker 2>/dev/null || groupadd -g ${DOCKER_GID} docker && \
  usermod -aG docker ${USERNAME}

# Set up SSH agent socket symlink in entrypoint
RUN mkdir -p /home/${USERNAME}/.ssh && \
  chown -R ${USERNAME}:${USERNAME} /home/${USERNAME}/.ssh

# Use tini as init to handle signals properly
ENTRYPOINT ["/usr/bin/tini", "-g", "--"]
CMD ["/bin/bash"]

USER ${USERNAME}
WORKDIR /workspace
