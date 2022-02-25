---
Title: Powershell on Windows 11
description: >
  Install Hugo with a simple Powershell Script and chocolatey
---

It is possible to use a powershell script and Chocolatey to install and other dependencies. Execute the following script:

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072
Invoke-Expression ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
choco install -y nodejs
choco install -y hugo-extended
```

Once complete, in root of of the repository execute the following commands

```powershell
git submodule update --init --recursive --depth 1
npm install
```
