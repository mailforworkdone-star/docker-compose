# Docker Compose Practical

## 1. Aim
To create and run multiple Docker containers using Docker Compose and configure communication between them.

## 2. Requirements
- Debian Linux
- Docker Engine
- Docker Compose
- Git and GitHub

## 3. Project Structure

```text
compose-lab/
├── compose.yaml
├── web1/
│   ├── index.html
│   └── default.conf
└── web2/
    └── index.html
```

## 4. Create Project Directories

```bash
mkdir -p ~/compose-lab/web1 ~/compose-lab/web2
cd ~/compose-lab
```

## 5. Create HTML Pages

Create the first webpage:

```bash
nano web1/index.html
```

Add:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Web 1</title>
</head>
<body>
    <h1>Hello from Web 1</h1>
    <a href="/hi/">Visit Web 2</a>
</body>
</html>
```

Create the second webpage:

```bash
nano web2/index.html
```

Add:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Web 2</title>
</head>
<body>
    <h1>Hi from Web 2</h1>
</body>
</html>
```

## 6. Configure Nginx Reverse Proxy

Create the configuration file:

```bash
nano web1/default.conf
```

Add:

```nginx
server {
    listen 80;

    location / {
        root /usr/share/nginx/html;
        index index.html;
    }

    location /hi/ {
        proxy_pass http://web2/;
    }
}
```

**Explanation:**
- `listen 80`: Nginx listens on port 80 inside the container.
- `location /`: Serves the Web 1 webpage.
- `location /hi/`: Forwards requests to Web 2.
- `proxy_pass http://web2/`: Uses the Docker Compose service name `web2` to reach the second container.

## 7. Create the Docker Compose File

```bash
nano compose.yaml
```

Add:

```yaml
services:
  web1:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./web1/index.html:/usr/share/nginx/html/index.html:ro
      - ./web1/default.conf:/etc/nginx/conf.d/default.conf:ro
    depends_on:
      - web2

  web2:
    image: nginx:alpine
    volumes:
      - ./web2/index.html:/usr/share/nginx/html/index.html:ro
```

**Explanation:**
- `services`: Defines the containers managed by Compose.
- `image`: Specifies the image used to create each container.
- `ports`: Maps host port 80 to Web 1's container port 80.
- `volumes`: Mounts local files inside the containers.
- `depends_on`: Starts Web 2 before Web 1; it does not guarantee Web 2 is fully ready.
- Both containers join the default Compose network and can communicate using their service names.

## 8. Start the Containers

```bash
sudo docker compose up -d
```

Check the running containers:

```bash
sudo docker compose ps
```

View downloaded images:

```bash
sudo docker images
```

View logs:

```bash
sudo docker compose logs
```

## 9. Test the Application

Open a browser and visit:

```text
http://localhost/
```

Expected output: **Hello from Web 1**

Then visit:

```text
http://localhost/hi/
```

Expected output: **Hi from Web 2**

The second request reaches Web 2 through the Nginx reverse proxy.

## 10. Stop and Remove the Containers

```bash
sudo docker compose down
```

This removes the project's containers and default network. It does not delete the HTML files or Compose configuration.

## 11. Upload the Project to GitHub

Initialize Git:

```bash
git init
git add .
git commit -m "Add Docker Compose practical"
```

Set the branch name:

```bash
git branch -M main
```

Add your GitHub repository:

```bash
git remote add origin https://github.com/USERNAME/REPOSITORY.git
```

Push the project:

```bash
git push -u origin main
```

Replace `USERNAME` and `REPOSITORY` with your GitHub details. If `origin` already exists, update it using `git remote set-url origin URL` instead of adding it again.

For HTTPS authentication, use a GitHub personal access token or configure SSH; GitHub account passwords are not accepted for Git operations.

## 12. Result

Successfully created and managed two Nginx containers using Docker Compose. Configured an Nginx reverse proxy to access both webpages through the same host port.


