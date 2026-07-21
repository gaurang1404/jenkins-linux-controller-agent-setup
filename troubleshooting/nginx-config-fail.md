# Issue: NGINX configuration test fails

**Cause**

There is a syntax error in the NGINX configuration file.

**Solution**

Verify the configuration using:

```bash
sudo nginx -t
```

Correct any reported errors before restarting NGINX.

---

### Issue: Jenkins page does not load

**Cause**

Possible reasons include:

- Jenkins service is not running.
- NGINX service is not running.
- Firewall is blocking HTTP traffic.
- SELinux is preventing NGINX from connecting to Jenkins.

**Solution**

Verify the status of both services.

```bash
sudo systemctl status nginx
sudo systemctl status jenkins
```

Ensure the HTTP service is allowed through the firewall and that SELinux has been configured using:

```bash
sudo setsebool -P http_can_network_connect 1
```