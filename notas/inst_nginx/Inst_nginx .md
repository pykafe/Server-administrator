# Setup server ho nginx iha ubuntu 16.04
#### 1. Introdusaun

Nginx mak web server ida nebe popular iha mundu no responsabiliza ba hosting husi situs highest-traffic site nian no internet nebe ass liu, diak liu kompara ho apache nebe hetan kazu barak no uza hanesan web server ou proxy.

Iha kontextu ida nee ita sei koalia konaba maneira nebe uza ka hetan iha server Ubuntu 16.04.

#### 2. Requirementu/Kriteria
1. Antes atu hahu mata dalan ida nee, ita tenki iha regular non-root user  nebe ho estimeua sudo iha konfigurasaun server. 

2. Ita presiza hatene oinsa mak atu halo konfigurasaun user account tuir matan dalan server hahu ho 16.04.

#### 3. Hahu kria server
**Step 1. kria public/private ssh key** 

1.  Prosesu kria public ssh keys
2.  print ssh key

   `$ cat ~/.ssh/id_rsa.pub`
```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCgKBUVlVVNnGBigJzvAa/ryOHMRPGxZiY66SMxh5RrVsA05kj4IUb6U0Cqf3pgBJSxF5tY2s6g8m7wM923RNRdvC5gsORKpe0FMgIQ== joanicobarros@gmail.com
```
2. Exekuta ka login ba ita boot nia server hanesan root user

`$ ssh root@server_ip_address`

**Step 2 . kria user foun**

Uza command `adduser` hodi adisiona user ba ita nia sistema.

`$ adduser username`

- konfirma user foun nia password iha prompt

```
Set password prompts:
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
```
- Halo tuir prompt hodi set ba user foun nia informasaun. ida nee diak no aseita detaillu ba informasaun.

  ```
  User information prompts:
  Changing the user information for username
  Enter the new value, or press ENTER for the default
    	Full Name []:
    	Room Number []:
    	Work Phone []:
    	Home Phone []:
    	Other []:
  Is the information correct? [Y/n]
  ```
  Uza command usermod hodi hatama user ba iha grupu sudo nian.

  `$ usermod -aG sudo username`

**Step 3. install nginx no konfigura nginx**

`$ sudo apt-get install nginx`

Depois prosesu install nginx hotu tuir bele check web server ho maneira hanesan tuir mai nee:

Ita bele check ho `systemd` init system hodi exekuta tuir tipu nebe iha.

Hapara ou  web server, bele run tipu ida nee:
```
$ sudo systemctl stop nginx
```
Hahu web server wainhira ita hapara ho tipu ida nee:

```
$ sudo systemctl start nginx
```
Hapara no depois hahu fila fali ho tipu ida nee:
```
$ sudo systemctl restart nginx
```
Karik ita muda konfigurasaun Nginx bele reload bebeik la ho dropping connection, bele halo ho command ida nee:
```
$ sudo systemctl reload nginx
```
Check server ho tipu ida nee:
```
$ sudo systemctl status nginx
```
```
Output
● nginx.service - A high performance web server and a reverse proxy server
   Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
   Active: active (running) since Mon 2016-04-18 16:14:00 EDT; 4min 2s ago
 Main PID: 12857 (nginx)
   CGroup: /system.slice/nginx.service
           ├─12857 nginx: master process /usr/sbin/nginx -g daemon on; master_process on
           └─12858 nginx: worker process
```

**Step 4. kria root dokumentu file**

depois install nginx run :

`$ /etc/nginx/sites-available/` ho `/etc/nginx/sites-enable/`

automatika kria file path hanesan tuir mai nee;

`$ /var/www/html`

exemplu tuir kria file:
```
$ sudo mkdir -p /var/www/test.com/
```
**Kria index page**

Depois kria root directory, bele kria index page kada situs ida-idak nian.
Kria `index.html` file iha domain:

```
$ nano /var/www/test.com/index.html
```
Bele halo mudansa iha file `index.html` nia laran.
```
<html>
    <head>
        <title>Welcome to Test.com!</title>
    </head>
    <body>
        <h1>Success!  The test.com server block is working!</h1>
    </body>
</html>
```

**Step 5. Kria  file server .**

loke file server tuir kria file server kada domain ida.
   ```
   $ cd /etc/nginx/sites-available
      - default
   $ touch default
      - default.bak
      - default
   $ nano default
   ```
Edit server file server tuir file nebe ita kria ona ho naran domain nebe iha :																																																																																																																																																																																																																																																																																																			

```
server {
        listen 80; ## Hare a ipv4; Iha line ida nee deafault port 80
        #listen   [::]:80 default ipv6only=on;
        server_name test;
        root /var/www/text.com; # Nee root ba directory nebe ita kria index page niaa.
        index index.html;

        location / { 
                try_files $uri $uri/ =404; 
        }
}
```
Hamos file default enabled
```
$ cd /etc/nginx/sites-enabled
$ rm default
$ ln -s /etc/nginx/sites-available/default  /etc/nginx/sites-enabled/default
```
Depois edit hotu tuir mai reload no run status hodi hare error

`$ sudo nginx -s reload`

`$ sudo systemctl status nginx`

```
● nginx.service - A high performance web server and a reverse proxy server
   Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: en
   Active: active (running) since Sat 2018-01-27 03:27:05 UTC; 4s ago
  Process: 2207 ExecStop=/sbin/start-stop-daemon --quiet --stop --retry QUIT/5 -
  Process: 2336 ExecStart=/usr/sbin/nginx -g daemon on; master_process on; (code
  Process: 2332 ExecStartPre=/usr/sbin/nginx -t -q -g daemon on; master_process 
 Main PID: 2341 (nginx)
    Tasks: 2
   Memory: 1.7M
      CPU: 31ms
   CGroup: /system.slice/nginx.service
           ├─2341 nginx: master process /usr/sbin/nginx -g daemon on; master_pro
           └─2342 nginx: worker process                           
```
Depois ita kria hotu bele run server IP address http://172.166.100.4/ iha browser hodi hare nia rezultadu.



### Notas 

Diferensia entre server configuration sites-enable ho site-available ?

- sites-enable mak file ida nebe ita link husi file original site-available nian.
- site-available mak file original nebe ita rai iha file directory.

### Referensia :

1. https://www.digitalocean.com/community/tutorials/how-to-create-a-sudo-user-on-ubuntu-quickstart
2. https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-16-04
