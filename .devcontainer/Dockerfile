FROM mcr.microsoft.com/devcontainers/rust:0-1-bullseye
# FROM mcr.microsoft.com/devcontainers/rust:1.0.9-bullseye

# Include lld linker to improve build times either by using environment variable
# RUSTFLAGS="-C link-arg=-fuse-ld=lld" or with Cargo's configuration file (i.e see .cargo/config.toml).
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install clang lld lshw libssl-dev \
    && apt-get autoremove -y && apt-get clean -y

# Install Docker CE CLI
RUN apt-get update \
    && apt-get install -y apt-transport-https ca-certificates curl gnupg2 lsb-release \
    && curl -fsSL https://download.docker.com/linux/$(lsb_release -is | tr '[:upper:]' '[:lower:]')/gpg | apt-key add - 2>/dev/null \
    && echo "deb [arch=amd64] https://download.docker.com/linux/$(lsb_release -is | tr '[:upper:]' '[:lower:]') $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list \
    && apt-get update && apt-get install -y docker-ce-cli

# Install Docker Compose
RUN export LATEST_COMPOSE_VERSION=$(curl -sSL "https://api.github.com/repos/docker/compose/releases/latest" | grep -o -P '(?<="tag_name": ").+(?=")') \
    && curl -sSL "https://github.com/docker/compose/releases/download/${LATEST_COMPOSE_VERSION}/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose \
    && chmod +x /usr/local/bin/docker-compose

RUN apt-get install -y \
    curl make build-essential zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev \
    llvm libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev \
    liblzma-dev gnupg2 openssh-client gcc g++ sqlite3 zip unzip gnupg software-properties-common

RUN curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg \
    && echo "deb [signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
    && apt update \
    && apt install -y gh

RUN apt-get update && apt-get install -y protobuf-compiler

# Install Terraform, TFLint, Terragrunt, and Terraform Docs
COPY library-scripts/*.sh /tmp/library-scripts/
ARG TERRAFORM_VERSION=1.2.0
ARG TFLINT_VERSION=0.44.1
ARG TERRAGRUNT_VERSION=0.43.0
RUN bash /tmp/library-scripts/terraform-debian.sh "${TERRAFORM_VERSION}" "${TFLINT_VERSION}" "${TERRAGRUNT_VERSION}" "${TERRAFORM_DOCS_VERSION}"

# Install Python
RUN git clone https://github.com/pyenv/pyenv.git /opt/pyenv
ENV PYENV_ROOT="/opt/pyenv"
ENV PATH="/opt/pyenv/shims:/opt/pyenv/bin:$PATH"
ENV PYENV_SHELL="bash"
RUN pyenv install 3.10.11
RUN pyenv global 3.10.11
RUN pip install poetry==1.8.2

# Install Node
RUN curl -L https://raw.githubusercontent.com/tj/n/master/bin/n -o n
RUN bash n 18
