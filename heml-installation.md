# HELM INSTALLATION ON UBUNTU OS

- Go with the official website for helm installation. `https://helm.sh/docs/intro/install/`

## From Script
- Helm now has an installer script that will automatically grab the latest version of Helm and install it locally
- You can fetch that script, and then execute it locally. It's well documented so that you can read through it and understand what it is doing before you run it.
```
    $ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
    $ chmod 700 get_helm.sh
    $ ./get_helm.sh
```

## From Apt (Debian/Ubuntu)
```
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm

```