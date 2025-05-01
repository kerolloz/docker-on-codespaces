# Docker on GitHub Codespaces

[![GitHub - Template Repository](https://img.shields.io/badge/template-repo-blue?logo=github)](https://github.com/kerolloz/docker-on-codespaces/generate)

This repository serves as a **template for enabling Docker access within GitHub Codespaces**.  
It's especially useful when you want to **build Docker images using GitHub's fast internet connection**, while still controlling Docker from your local machine.

## 🧰 Requirements

- GitHub Codespaces access
- VS Code with the **GitHub Codespaces** extension
- Docker installed on your local machine

## ⚙️ Setup

> [!NOTE]
> The `devcontainer.json` here is optional. You can start with any codespace (empty or not).

Open your Codespace in **VS Code**, then run:

> [!TIP]
> If you're using the provided `devcontainer.json`, the `socat` installation is handled automatically via the `postCreateCommand`.

```sh
sudo apt update && sudo apt install -y socat
socat TCP-LISTEN:2375,reuseaddr,fork UNIX-CONNECT:/var/run/docker.sock
```

In VS Code, **forward port `2375`** to your local machine.  
This allows your local Docker CLI to communicate with the Docker daemon running inside the Codespace.

## 🔄 Architecture

```
┌──────────────┐            ┌────────────────────────┐
│ Your Machine │◄──VSCode──►│ GitHub Codespace (VM)  │
└──────────────┘            │  ┌──────────────────┐  │
                            │  │ Docker Daemon    │  │
                            │  │ unix:///var/run/ │  │
                            │  └──────────────────┘  │
                            └────────────────────────┘
                                 ▲
                                 │ socat bridges TCP:2375 ⇄ UNIX socket
```

## 🚀 Usage

There are two main ways to make the Codespace your **remote Docker host**.  
**Choose one:**

### 1. Prepend your Docker commands

```sh
DOCKER_HOST=tcp://127.0.0.1:2375 docker pull redis:alpine
```

### 2. Use Docker contexts (recommended)

```sh
docker context create codespace \
  --docker "host=tcp://127.0.0.1:2375"

docker context use codespace

docker run hello-world
```

To switch back to your local Docker:
```sh
docker context use default
```

## ✅ Example Output

```sh
$ DOCKER_HOST=tcp://127.0.0.1:2375 docker pull hello-world
Using default tag: latest
latest: Pulling from library/hello-world
Digest: sha256:...
Status: Downloaded newer image for hello-world:latest
```

## 🛑 Security Warning

> [!CAUTION]
> **Never expose port 2375 to the public internet.**  
The Docker socket gives full control over the host system.  
This setup is safe when port forwarding is securely handled via VS Code.

## 🙌 Credits

Inspired by real-world Docker development needs where remote build speed is essential.
