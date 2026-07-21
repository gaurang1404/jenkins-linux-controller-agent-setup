## Troubleshooting

### Issue: SSH service is not installed or running

**Cause**

The OpenSSH server package is not installed, or the SSH daemon (`sshd`) is not running on the worker node. Without the SSH service, the Jenkins controller cannot establish a connection to the worker node.

**Solution**

First, verify the status of the SSH service.

```bash
sudo systemctl status sshd
```

If the service is not installed, install the OpenSSH server package.

```bash
sudo dnf install openssh-server -y
```

Enable the service so it starts automatically on boot, then start it.

```bash
sudo systemctl enable sshd
sudo systemctl start sshd
```

Finally, verify that the service is running.

```bash
sudo systemctl status sshd
```

If the worker node is protected by a firewall, ensure that SSH traffic is allowed.

```bash
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --reload
```