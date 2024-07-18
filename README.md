# IBKR-Dashboard-K8s-Helm
K8s manifest and Helm Chart for creating a IBKR Dashboard which includes risk data and current positions

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) ![GitHub Release](https://img.shields.io/github/v/release/adam-lechnos/IBKR-Dashboard-K8s-Helm)

[Docker Compose Edition](https://github.com/Adam-Lechnos/IBKR-Dashboard)

# Prerequisites
* [Helm Secrets](https://github.com/jkroepke/helm-secrets/releases/tag/v4.6.0)
* GnuPG (`sudo apt-get install gnupg`)
* [SOPS](https://github.com/getsops/sops?ref=blog.gitguardian.com)

# Create GnuPG Config

# Create SOPS Config

# Create and Encrypt Secrets Data
* ibkr-dashboard - credentials.yaml
* ibkr-dashbaord - certs.yaml
* ibkr-dashboard - users.yaml

# Helm Commands
* Dry Run `helm secrets install <release name> -f helm/ibkr-dashboard/my-values.yaml -f helm/ibkr-dashboard/credentials.yaml -f helm/ibkr-dashboard/certs.yaml -f helm/ibkr-dashboard/users.yaml helm/ibkr-dashboard --dry-run`
* Install `helm secrets install <release name> -f helm/ibkr-dashboard/my-values.yaml -f helm/ibkr-dashboard/credentials.yaml -f helm/ibkr-dashboard/certs.yaml -f helm/ibkr-dashboard/users.yaml helm/ibkr-dashboard`
