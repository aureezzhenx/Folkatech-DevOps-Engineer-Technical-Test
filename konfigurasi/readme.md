Konfigurasi server frontend

NGINX sudah terinstall dari terraform
![alt text](https://github.com/aureezzhenx/devops3/blob/main/konfigurasi/4.png)

Install Node Version Manager (NVM) https://github.com/nvm-sh/nvm

![alt text](https://github.com/aureezzhenx/devops3/blob/main/konfigurasi/5.png)
![alt text](https://github.com/aureezzhenx/devops3/blob/main/konfigurasi/6.png)
![alt text](https://github.com/aureezzhenx/devops3/blob/main/konfigurasi/7.png)

Install & Update Script
```
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```

Running either of the above commands downloads a script and runs it. The script clones the nvm repository to ~/.nvm, and attempts to add the source lines from the snippet below to the correct profile file (~/.bash_profile, ~/.zshrc, ~/.profile, or ~/.bashrc).
```
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```

Install NodeJS 14
```
nvm install 14
```
