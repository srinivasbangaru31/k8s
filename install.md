# AWS EKS Setup Guide

## Reference Documentation
Always refer to the official AWS documentation for the latest updates and detailed steps:
[Amazon EKS Setup Guide](https://docs.aws.amazon.com/eks/latest/userguide/setting-up.html)

---

## Installing kubectl

`kubectl` is the command-line tool used for interacting with Kubernetes clusters. It allows you to deploy applications, inspect and manage cluster resources, and view logs.

### Installing kubectl on Linux (amd64)

Download the `kubectl` binary:

```sh
curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.32.0/2024-12-20/bin/linux/amd64/kubectl
```

Make the downloaded file executable:

```sh
chmod +x ./kubectl
```

#### Moving kubectl to a Directory in Your PATH
To ensure `kubectl` is accessible from anywhere in the terminal, move it to a directory included in your `PATH`. If you have a version of `kubectl` already installed, it is recommended to place the new binary in `$HOME/bin` and update your `PATH` accordingly.

```sh
mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$HOME/bin:$PATH
```

Persist the changes by adding the updated `PATH` to your `.bashrc`:

```sh
echo 'export PATH=$HOME/bin:$PATH' >> ~/.bashrc
```

To apply the changes, run:

```sh
source ~/.bashrc
```

---

## Installing eksctl

`eksctl` is a command-line tool for creating and managing Amazon EKS clusters. It simplifies cluster creation and automates many manual steps.

Refer to the official installation guide: [eksctl Installation](https://eksctl.io/installation/)

### Download and Install the Latest Release

Set the architecture for your system (default is `amd64`). If using an ARM-based system, set `ARCH` to `arm64`, `armv6`, or `armv7`:

```sh
ARCH=amd64
PLATFORM=$(uname -s)_$ARCH
```

Download the latest `eksctl` binary:

```sh
curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$PLATFORM.tar.gz"
```

(Optional) Verify the checksum to ensure file integrity:

```sh
curl -sL "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_checksums.txt" | grep $PLATFORM | sha256sum --check
```

Extract the binary and move it to `/usr/local/bin` for system-wide access:

```sh
tar -xzf eksctl_$PLATFORM.tar.gz -C /tmp && rm eksctl_$PLATFORM.tar.gz

sudo mv /tmp/eksctl /usr/local/bin
```

To verify the installation, check the version:

```sh
eksctl version
```

---

## Conclusion
By following these steps, you have successfully installed `kubectl` and `eksctl`, which are essential tools for managing Kubernetes clusters on AWS EKS. Always refer to the official documentation for the latest updates and best practices.
