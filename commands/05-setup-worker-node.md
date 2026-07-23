# Configure a Linux CentOS Worker Node

## Description

Although the Jenkins controller is capable of executing builds, it is considered a best practice to separate the **controller** and **worker nodes** in production environments.

The Jenkins controller is responsible for storing pipeline configurations, credentials, plugins, build history, and deployment configurations for the entire organization. If the controller is compromised, it can have a significant impact on all CI/CD operations.

To improve security and scalability, builds are typically executed on dedicated **worker nodes**. The controller communicates with these worker nodes over **SSH**, allowing build workloads to be isolated from the controller.

In this guide, we will configure a Linux CentOS worker node and establish a secure SSH-based authentication mechanism between the Jenkins controller and the worker node.

---

## Steps

### Step 1: Install Java and Git

The worker node requires Java to run the Jenkins agent and Git to clone repositories during builds.

Update the system packages and install the required software.

```bash
sudo dnf update -y
sudo dnf install java-21-openjdk git -y
```

---

### Step 2: Create a Jenkins User

Create a dedicated user that the Jenkins controller will use to connect to the worker node over SSH.

```bash
sudo useradd -m -s /bin/bash jenkins
```

Assign a password to the user.

```bash
sudo passwd jenkins
```

---

### Step 3: Verify the SSH Service

Ensure the SSH service is installed and running on the worker node.

```bash
sudo systemctl status sshd
```

If the service is not installed or running, refer to:

**[SSH Service Not Running](../troubleshooting/sshd-service-not-running.md)**

---

### Step 4: Generate an SSH Key Pair on the Controller

Return to the Jenkins controller.

Generate a public/private SSH key pair using the **Ed25519** algorithm.

```bash
sudo -u jenkins ssh-keygen -t ed25519
```

This creates a private key that remains on the controller and a public key that will be shared with the worker node.

---

### Step 5: Configure the Worker to Trust the Controller

Copy the controller's public key to the worker node.

```bash
ssh-copy-id jenkins@<IP_OF_WORKER_NODE>
```

Example:

```bash
ssh-copy-id jenkins@192.168.1.10
```

The worker node now stores the controller's public key in the Jenkins user's `authorized_keys` file.

From this point onward, the controller can securely authenticate to the worker node without requiring a password.

---

### Step 6: Configure the Controller to Trust the Worker

So far, the **worker trusts the controller**.

However, the controller must also verify that it is connecting to the correct worker node.

By default, Jenkins can be configured to trust any host it connects to. While this may be acceptable in a lab environment, it is not recommended for production systems.

Instead, we will use the **Known Hosts Verification Strategy**, where the controller only connects to hosts whose fingerprints are present in its `known_hosts` file.

From the controller server, connect to the worker node as the Jenkins user.

```bash
sudo -u jenkins ssh jenkins@<IP_OF_WORKER_NODE>
```

Example:

```bash
sudo -u jenkins ssh jenkins@192.168.1.10
```

The first time you connect, SSH will display the worker node's fingerprint and ask whether you trust the host.

Type:

```text
yes
```

The fingerprint is then stored in:

```text
/var/lib/jenkins/.ssh/known_hosts
```

A fingerprint is a cryptographic hash of the worker node's public host key. It provides a concise way to verify the identity of the remote host before establishing an SSH connection.

Once the fingerprint has been added to the `known_hosts` file, the controller will trust future connections to that worker node.

---

## Conclusion

You have successfully configured a Linux CentOS worker node and established secure SSH authentication between the Jenkins controller and the worker.

The worker now trusts the controller through its authorized SSH key, while the controller verifies the worker's identity using the SSH `known_hosts` file. This mutual trust forms the foundation for secure communication between the Jenkins controller and its worker nodes.

---

## Next Steps

➡️ **[Set Up a Worker Node](./06-setup-jenkins-web.md)**