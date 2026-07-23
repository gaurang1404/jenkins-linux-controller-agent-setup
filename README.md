# Jenkins Linux Controller Agent Setup

A step-by-step guide to configuring a **distributed Jenkins environment** with a **controller** and a **Linux (CentOS) worker node** running on separate virtual machines.

This repository demonstrates how to install Jenkins, configure an NGINX reverse proxy, securely connect a worker node using SSH, and execute pipelines on dedicated build agents. The guide follows a production-inspired architecture where the controller manages Jenkins while build workloads are delegated to worker nodes.

Whether you're learning Jenkins for the first time or exploring how distributed builds work, this repository provides a practical, hands-on walkthrough.

---

## What You'll Learn

- Install Jenkins on a Linux controller server
- Configure NGINX as a reverse proxy
- Understand the responsibilities of a Jenkins controller and worker node
- Configure a Linux (CentOS) worker node
- Establish secure SSH authentication between the controller and worker
- Configure the **Known Hosts Verification Strategy**
- Add SSH credentials in Jenkins
- Register and connect a worker node
- Configure Jenkins to run builds exclusively on the worker
- Create and execute your first Jenkins pipeline

---

## Architecture

```text
                        HTTP Requests
                              │
                              ▼
                      ┌────────────────┐
                      │     NGINX      │
                      │ Reverse Proxy  │
                      └───────┬────────┘
                              │
                              ▼
                   Jenkins Controller VM
              (Configuration & Orchestration)
                              │
                    SSH Authentication
                              │
                              ▼
                  Jenkins Worker Node VM
                 (Pipeline Execution Only)
```

---

## Prerequisites

Before starting this guide, ensure you have:

- Two Linux virtual machines (CentOS/RHEL-compatible)
  - Jenkins Controller
  - Jenkins Worker Node
- Network connectivity between both VMs
- Administrative (`sudo`) access on both machines
- Basic familiarity with Linux command-line operations

---

## Guide

Follow the guides in order:

1. Install Jenkins on the Controller Server
2. Configure an NGINX Reverse Proxy
3. Configure a Linux CentOS Worker Node
4. Set Up Jenkins in the Web Interface
5. Run Your First Pipeline

---

## Technologies Used

- Jenkins
- NGINX
- OpenSSH
- Java (OpenJDK 21)
- Git
- systemd
- firewalld
- SELinux
- Linux (CentOS)

---

## Why Use a Separate Worker Node?

Running builds on the Jenkins controller is suitable for small environments but is not recommended for production deployments.

Using dedicated worker nodes provides several advantages:

- Improved security by isolating build execution
- Better scalability through distributed builds
- Reduced load on the controller
- Easier management of build environments
- Support for multiple operating systems and build configurations

---

## What You'll Build

By the end of this guide, you will have a fully functional distributed Jenkins setup capable of:

- Managing pipelines from a dedicated controller
- Executing builds on a separate Linux worker node
- Communicating securely over SSH
- Running Declarative Pipelines
- Serving Jenkins through an NGINX reverse proxy

---

## Contributing

Contributions, improvements, and suggestions are welcome.

If you find an issue or would like to improve the guide, feel free to open an issue or submit a pull request.

---

## ▶️ Getting Started

Start from the **`commands`** directory.

The files are numbered in the order they should be followed.

**It's recommended not to skip any steps**, as each section builds upon the previous one.

If you encounter any issues, check the corresponding guide inside the **`troubleshooting`** directory.

### Start here → [01 - Virtual Machine Configuration](commands/01-vm-config.md)

---

## ⭐ If this repository helped you...

If you found this guide useful, consider giving the repository a ⭐.

It motivates me to create more practical, beginner-friendly resources for the developer community.

Happy learning!

---

## License

This repository is provided for educational purposes. Feel free to use and adapt the content for your own learning.