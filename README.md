# Docker on GitHub's Codespaces

This repository is meant to be used as a template for connecting to dokcer on GitHub's Codespaces. This is pretty useful for cases when you need a super fast internet connection to build docker images.

# Commands

The devcontainer.json here is not really necesary. You just need an empty (or non-empty, up to you) codespace. Run the following in your terminal (open the codespace in VSCode).

```sh
sudo apt update && sudo apt install socat # This command is run for you in the devcontainer.json `postCreateCommand`
socat TCP-LISTEN:2375,reuseaddr,fork UNIX-CONNECT:/var/run/docker.sock
```
