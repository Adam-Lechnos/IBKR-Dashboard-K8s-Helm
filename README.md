# IBKR-Dashboard-K8s-Helm
K8s manifest and Helm Chart for creating a IBKR Dashboard which includes risk data and current positions

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) ![GitHub Release](https://img.shields.io/github/v/release/adam-lechnos/IBKR-Dashboard-K8s-Helm)

[Docker Compose Edition](https://github.com/Adam-Lechnos/IBKR-Dashboard)

# Prerequisites
* [Helm Secrets](https://github.com/jkroepke/helm-secrets/releases/tag/v4.6.0)
* GnuPG (`sudo apt-get install gnupg`)
* [SOPS](https://github.com/getsops/sops?ref=blog.gitguardian.com)

# Create GnuPG Key

Execute the following commands in bash:
``` bash
export KEY_NAME="Adam Lechnos"
export KEY_COMMENT="test key for sops"

gpg --batch --full-generate-key <<EOF
%no-protection
Key-Type: 1
Key-Length: 4096
Subkey-Type: 1
Subkey-Length: 4096
Expire-Date: 0
Name-Comment: ${KEY_COMMENT}
Name-Real: ${KEY_NAME}
EOF
```

Get the GPG key fingerprint:
``` bash
$ gpg --list-secret-keys "${KEY_NAME}"

sec   rsa4096 2023-10-17 [SCEA]
      BE574406FE117762E9F4C3301CB98A820DCBA0FC
uid           [ultimate] Adam L ('test key for sops')
ssb   rsa4096 2023-10-17 [SEA]
```

In this example, the fingerprint is `BE574406FE117762E9F4C3301CB98A820DCBA0FC`

# Create SOPS Config
The SOPS config enables SOPS to make use of the previously created PGP key. Create a .sops.yaml within the  `charts` directory:

``` yaml
creation_rules:
    - pgp: >-
        BE574406FE117762E9F4C3301CB98A820DCBA0FC
```

# Create and Encrypt Secrets Data
* ibkr-dashboard - credentials.yaml
    * contains the IBKR Login info.
    * **Security Recommendation**: It is recommend to first create an additional read-only user with a strong password. Additional IP restriction is also recommended if feasible. Refer to the official Interactive Brokers documenting [here](https://www.ibkrguides.com/clientportal/uar/addingauser.htm).
    * creates a `credentials.yaml.dec`
        ``` yaml
        username: <IBKR Username>
        password: <IBKR Password>
        ```
    * Execute `helm secrets encrypt credentials.yaml.dec > credentials.yaml` command to encrypt the file.

* ibkr-dashbaord - certs.yaml
    * contains the TLS certificate data for ibkr-dash pod
    * creates a `certs.yaml.dec`
        ``` yaml
        cert: |
            <cert data output>
        key: |
            <key data output>
        ```
    * Execute `helm secrets encrypt certs.yaml.dec > certs.yaml` command to encrypt the file.

* ibkr-dashboard - users.yaml
    * contains the basic authentication accounts for ibkr-dash pod in [HTPasswd](https://www.web2generators.com/apache-tools/htpasswd-generator) format
    * creates a `users.yaml.dec`
        ``` yaml
        htpasswd: <htpasswd base64>
        ```
    * Execute `helm secrets encrypt users.yaml.dec > users.yaml` command to encrypt the file.

# Helm Commands
* Dry Run `helm secrets install ibkr-dash -f helm/ibkr-dashboard/charts/my-values.yaml -f helm/ibkr-dashboard/charts/credentials.yaml -f helm/ibkr-dashboard/charts/certs.yaml -f helm/ibkr-dashboard/charts/users.yaml helm/ibkr-dashboard/charts --dry-run`
* Install `helm secrets install ibkr-dash -f helm/ibkr-dashboard/charts/my-values.yaml -f helm/ibkr-dashboard/charts/credentials.yaml -f helm/ibkr-dashboard/charts/certs.yaml -f helm/ibkr-dashboard/charts/users.yaml helm/ibkr-dashboard/charts`