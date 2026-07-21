# Configure NGINX Reverse Proxy

## Description

NGINX is a high-performance web server and reverse proxy commonly used to handle incoming HTTP requests. It provides several benefits, including cleaner URLs, improved security, SSL termination, and the ability to efficiently handle a large number of concurrent requests.

Instead of allowing users to directly access the Jenkins service running on **port 8080**, we configure NGINX to act as a reverse proxy. Incoming requests are received on **port 80** and then forwarded internally to the Jenkins service.

This architecture is commonly used in production environments because it separates public-facing traffic from the application itself.

---

## Steps

### Step 1: Install NGINX

Install the NGINX package using the system package manager.

```bash
sudo dnf install nginx -y
```

---

### Step 2: Enable and Start NGINX

Enable NGINX so it starts automatically during system boot, then start the service.

```bash
sudo systemctl enable nginx
sudo systemctl start nginx
```

---

### Step 3: Allow HTTP Traffic Through the Firewall

If HTTP traffic is not already allowed, add the HTTP service to the firewall and reload the configuration.

```bash
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --reload
```

---

### Step 4: Create the NGINX Configuration

Create a new configuration file for Jenkins.

```bash
sudo vi /etc/nginx/conf.d/jenkins.conf
```

Paste the following configuration and replace `<ADD_IP>` with the IP address of your Jenkins controller server.

```nginx
server {

    listen 80;

    server_name <ADD_IP>;

    location / {

        proxy_pass http://127.0.0.1:8080;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-Port $server_port;
        proxy_set_header X-Forwarded-Host $host;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

#### Understanding the Configuration

- **`listen 80`** tells NGINX to listen for incoming HTTP requests on the default HTTP port.
- **`server_name <ADD_IP>`** specifies the IP address that NGINX should respond to. Replace this with the IP address of your Jenkins controller.
- **`proxy_pass http://127.0.0.1:8080`** forwards all incoming requests to the Jenkins service running locally on port **8080**.

Since Jenkins only listens locally, clients never communicate directly with the Jenkins service. Instead, all requests pass through NGINX.

---

### Step 5: Test the NGINX Configuration

Verify that the configuration file contains no syntax errors.

```bash
sudo nginx -t
```

If the test is successful, restart NGINX.

```bash
sudo systemctl restart nginx
```

---

### Step 6: Configure Jenkins to Listen Only on Localhost

Since NGINX is responsible for handling incoming requests, configure Jenkins to listen only on the local interface.

Edit the Jenkins systemd override.

```bash
sudo systemctl edit jenkins
```

Paste the following configuration.

```ini
[Service]
Environment="JENKINS_LISTEN_ADDRESS=127.0.0.1"
```

Reload the systemd configuration and restart Jenkins.

```bash
sudo systemctl daemon-reload
sudo systemctl restart jenkins
```

---

### Step 7: Allow NGINX to Connect to Jenkins (SELinux)

On systems with SELinux enabled, NGINX is prevented from connecting to backend services by default.

Allow NGINX to proxy requests to Jenkins.

```bash
sudo setsebool -P http_can_network_connect 1
```

Restart NGINX.

```bash
sudo systemctl restart nginx
```

---

### Step 8: Verify the Setup

Open a web browser and navigate to:

```text
http://<IP_OF_YOUR_VM>
```

If everything has been configured correctly, the Jenkins web interface should load successfully.

Your Jenkins controller is now accessible through NGINX instead of directly exposing port **8080**.

---

## Conclusion

You have successfully configured NGINX as a reverse proxy for your Jenkins controller. Incoming HTTP requests are now forwarded to the Jenkins service running locally on **127.0.0.1:8080**, providing a cleaner and more secure architecture for accessing Jenkins.

---

## Next Steps

➡️ **[Set Up a Worker Node](./05-setup-worker-node.md)**

