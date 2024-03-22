# Rancher Desktop playground

Installation and sample use of rancher desktop

## What you need

* Azure subscription - most likely part of your Visual Studio subscription
* Github account - free to signup if you don't have one
* Microsoft Remote Desktop - [links to downloads](https://learn.microsoft.com/nl-nl/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)
* some time to tinker

## Setup

Create a new Virtual Machine in azure. Pay attention to these settings:

* __Instance details__
  * __Security type__ standard
  * __Image__ Windows 11 Pro
  * __Size__ Standard_D4s_v4
* __Inbound port rules__
  * __Public inbound ports__ Allow selected ports
  * __Select inbound ports__ RDP (3389)

## Install

After the VM is created make a remote desktop connection and run these installations with powershell (as administrator).

```powershell
# install chocolatey
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
# install openssl
choco install openssl -y
# install git
choco install git -y
# install base64 to decrypt secrets
choco install base64 -y
# install rancher desktop
choco install rancher-desktop -y
# install visual studio code
choco install vscode -y
choco install vscode-yaml -y

```

## Reboot

After installing rancher desktop a reboot is needed.

## Final steps

Fork this repository and clone it:

```bash
git clone https://github.com/<YOUR_NAME_HERE>/rancher-desktop-playground.git
```

Start Rancher desktop with the shortcut on the desktop. Accept the defaults for kubernetes and runtime. Now install [ingress-nginx](ingress-nginx) and [argocd](argocd) and you're ready for playing.

## Custom apps

Now you have a basic kubernetes setup with argocd for gitops deployments. Here are some custom apps you can deploy and tinker with.

The basic flow is adding an application yaml to the apps/localdev folder that points to the localdev overlay. After a git commit/push it will be deployed with argocd.

* [website](website): simple nginx website with a custom index.html

