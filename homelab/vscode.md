---
title: Running Visual Studio Code on LXC
---

VSCode's service install script force its tunnel running in unprivileged systemd, which cannot running in lxc. 

## Manual installation

```bash
# install vscode first
$ code --user-data-dir=~/root tunnel service install # ignore its error
# ...
$ mv ~/.vscode/cli/code-tunnel.service /etc/systemd/system

$ systemctl daemon-reload

$ sudo systemctl enable code-tunnel.service --now
```

---