DISCLAIMER: Karena keterbatasan, saya tidak memakai AWS dikarenakan saya tidak mempunyai CC untuk melakukan registrasi free-tier di aws, catatan saya dibawah ini menggunakan WSL dengan distro CentOS 8.

Sebagai bahan pertimbangan, saya sertakan Portfolio DevOps Engineer saya.
- [DevOps #1](https://github.com/aureezzhenx/TaskDevOps) (VMWare & Cloud Computing, Manage Code & Database, Docker & CI/CD, Monitoring & Ansible)
- [DevOps #2](https://github.com/aureezzhenx/Jouzie-Final-Task-Dumbways-Batch-4) (Deploy React on AWS)

### Konfigurasi server frontend
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

Clone repository aplikasi frontend nodejs dari https://github.com/aureezzhenx/wayshub-frontend
![alt text](https://github.com/aureezzhenx/devops3/blob/main/konfigurasi/9.png)

Jalankan npm install 
![alt text](https://github.com/aureezzhenx/devops3/blob/main/konfigurasi/10.png)

Jalankan npm start di background `(npm start &)`
![alt text](https://github.com/aureezzhenx/devops3/blob/main/konfigurasi/11.png)

Aplikasi frontend jalan di port 3000
![alt text](https://github.com/aureezzhenx/devops3/blob/main/konfigurasi/14.png)

Konfigurasi proxy pass dan SSL di NGINX
SSL menggunakan mkcert https://words.filippo.io/mkcert-valid-https-certificates-for-localhost/

Instalasi SSL di direktory /etc/nginx `mkcert --install`
![alt text](https://github.com/aureezzhenx/devops3/blob/main/konfigurasi/12.png)

Melakukan konfigurasi proxy_pass di nginx.conf, update listener menjadi 443 yang sebelumnya 80, dan update ssl_certificate
![alt text](https://github.com/aureezzhenx/devops3/blob/main/konfigurasi/13.png)

Verifikasi konfigurasi NGINX dengan `nginx -t` lalu restart NGINX dengan cara `systemctl restart nginx`. SSL sudah terpasang
![alt text](https://github.com/aureezzhenx/devops3/blob/main/konfigurasi/15.png)


