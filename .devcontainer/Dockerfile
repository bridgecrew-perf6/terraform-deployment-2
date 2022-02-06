FROM golang:1.17.6 AS golang
FROM mcr.microsoft.com/vscode/devcontainers/base:focal

ARG TF_VERSION=1.1.5 \
    TF_DOCS_VERSION=0.16.0 \
    TF_LINT_VERSION=0.34.1 \
    TF_SEC_VERSION=1.0.11 \
    AWS_VERSION=0.12.0 \
    AZURERM_VERSION=0.14.0

COPY --from=golang /usr/local/go/ /usr/local/go/
ENV PATH="/usr/local/go/bin/:${PATH}" \
    HTTPS_PROXY="" \
    HTTP_PROXY=""

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get install -y --no-install-recommends gnupg software-properties-common curl wget vim graphviz \
# Install Terraform, pre-commit and checkov
    && curl -fsSL https://apt.releases.hashicorp.com/gpg | apt-key add - \
    && apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main" \
    && add-apt-repository ppa:deadsnakes/ppa \
    && apt-get update && apt-get install -y --no-install-recommends \
       terraform="${TF_VERSION}" \
       terraform-ls \
       python3.7 \
       python-is-python3 \
       python3-pip \
    && pip install --no-cache-dir \
       pre-commit \
       changelog-cli \
       checkov \
# Install tflint, terraform-docs, tfsec
    && wget -O terraform-docs.tgz https://github.com/terraform-docs/terraform-docs/releases/download/v"${TF_DOCS_VERSION}"/terraform-docs-v"${TF_DOCS_VERSION}"-linux-amd64.tar.gz \
    && tar -xzf terraform-docs.tgz terraform-docs \
    && rm terraform-docs.tgz \
    && chmod +x terraform-docs \
    && mv terraform-docs /usr/bin/ \
    && wget -O tflint.zip https://github.com/terraform-linters/tflint/releases/download/v"${TF_LINT_VERSION}"/tflint_linux_amd64.zip \
    && unzip tflint.zip \
    && rm tflint.zip \
    && mv tflint /usr/bin/ \
    && mkdir -p /home/vscode/.tflint.d/plugins \
    && wget -O tflint-ruleset-aws.zip  https://github.com/terraform-linters/tflint-ruleset-aws/releases/download/v"${AWS_VERSION}"/tflint-ruleset-aws_linux_amd64.zip \
    && unzip tflint-ruleset-aws.zip -d /home/vscode/.tflint.d/plugins \
    && rm tflint-ruleset-aws.zip \
    && wget -O tflint-ruleset-azurerm.zip  https://github.com/terraform-linters/tflint-ruleset-azurerm/releases/download/v"${AZURERM_VERSION}"/tflint-ruleset-azurerm_linux_amd64.zip \
    && unzip tflint-ruleset-azurerm.zip -d /home/vscode/.tflint.d/plugins \
    && rm tflint-ruleset-azurerm.zip \
    && wget -O tfsec  https://github.com/aquasecurity/tfsec/releases/download/v"${TF_SEC_VERSION}"/tfsec-linux-amd64  \
    && chmod +x tfsec \
    && mv tfsec /usr/bin/ \
# Install AWS CLI
    && curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" \
    && unzip awscliv2.zip \
    && ./aws/install \
    && rm -fr ./aws \
# Install gitleaks
    && wget -q https://github.com/zricethezav/gitleaks/releases/download/v7.6.1/gitleaks-linux-amd64 -O /bin/gitleaks \
    && chmod +x /bin/gitleaks \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
