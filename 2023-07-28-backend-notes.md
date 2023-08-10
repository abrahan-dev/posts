# Backend notes

Some notes about backend development. At the end we get a simple nodejs web application running on a server with nginx, some security config, continuous integration etc...

## Command line

The command line is a text interface for the computer's operating system. It is a program that takes commands from the keyboard and gives them to the operating system to perform.

You need to know a few commands to be able to use the command line, out of the scope of this post.

```bash
# Show the current directory
pwd
# List the files in the current directory
ls -la
# Remove a directory and its content
rm -rf foo
# Create a directory arborescence
mkdir -p a/b/c
# Use man to read a program documentation
man ls
```

## Vim

Use vim to edit files in the command line. It is a text editor that is installed by default on most Unix systems. Again out of the scope of this post. But we need to know a few commands to be able to use it.

```bash
# Open a file
vim foo.txt
# Go to insert mode (edit mode)
i
# Go to command mode
Esc
# Go to the end of the file (in command mode)
G
# Save and quit (in command mode)
:wq
# Quit without saving (in command mode)
:q!
# Undone the last change (in command mode)
:u
```

## Shell

The shell is the program that runs in the terminal and actually executes commands. Example of shells are bash or zsh.

```bash
# Show the current shell
echo $SHELL
```

## Cloud computing

Virtualization is the process of creating a virtual version of something, including virtual computer hardware platforms, storage devices, and computer network resources. Cloud providers divide server resources into virtual computers.

Virtual machine is a digital version of a physical computer.

VPS is a virtual private server.

## Operating system

Once you have a virtual machine, you need to install an operating system on it. The operating system is the software that manages the computer hardware and provides common services for computer programs. The operating system is the interface between the user and the computer hardware.

There are a few kinds of operating systems we can use to serve our applications:

- Windows
    - Windows server
- Unix
    - Solaris
    - Linux: Ubuntu, Debian, Red Hat, Fedora, CentOS, Arch Linux, etc.
    - BSD: FreeBSD -> macOS

Unix systems are made of three parts:
- Shell: the interface between the user and the kernel
- Kernel: the core of the operating system
- Hardware: the physical machine

## SSH

We get into the server using ssh (secure socket shell). It is a cryptographic network protocol for operating network services securely over an unsecured network.

We use hash functions to encrypt the data. A hash function is a function that maps data of arbitrary size to data of fixed size. The result of a hash function is called a hash value, hash code, or simply hash.

An example of hash function is MD5. It is a widely used cryptographic hash function producing a 128-bit hash value. It is used to verify data integrity. But it is not recommended to use it for cryptographic purposes because it has been found to suffer from extensive vulnerabilities. The same input produces the same output, hackers can use a rainbow table to find the input from the output.

We prefer to hash with salt. Salt is a random data that is used as an additional input to a one-way function that hashes data, a password or passphrase. Salts are used to safeguard passwords in storage.

The golden standard now is SHA-256. It is a cryptographic hash function that takes an input of any length and returns a 256-bit fixed-length hash value.

If we want to hash a file, we can use the following command:
```bash
openssl sha256 file.txt
```

ssh uses public-key cryptography. It is a cryptographic system that uses pairs of keys: public keys which may be disseminated widely, and private keys which are known only to the owner. The generation of such keys depends on cryptographic algorithms based on mathematical problems to produce one-way functions. Effective security requires keeping the private key private; the public key can be openly distributed without compromising security.

How to access the server:
```bash
ssh -i ~/.ssh/id_rsa.pub user@ip
```
The first connection is made with the user `root`, not a good idea. Check below.

You can add your key to the keychain so you don't have to type your password every time you connect to the server.

Verify your .ssh/config file contains the following:
```bash
  AddKeysToAgent yes
  UseKeychain yes
```

And add your key to the keychain (the flag is only for macOS):
```bash
ssh-add --apple-use-keychain ~/.ssh/id_rsa
```

From now on, you can connect to the server with:
```bash
ssh user@ip
```

## Internet

How the internet works:
- It is built on cooperation and rules: We agree on standards and protocols.
- A simple diagram: 
    - Computer -> Network Card -> Router -> ISP -> Tier 1 ISP (Backbones) -> Datacenter -> Server cluster -> Load balancer -> Server
- The internet is a network of networks
- An intranet is a private network
- LAN is a local area network (no latency)
- WAN is a wide area network
- IP is the internet protocol
- IP address is a unique identifier for a computer on a network, there are two versions: IPv4 and IPv6

How to check the status of a network host:
```bash
ping google.com
```
How to follow the path of a request:
```bash
traceroute google.com
```
How to show the network status:
```bash
netstat -lt | less
```

TCP/UDP:  

- TCP is the transmission control protocol
- UDP is the user datagram protocol
- ICMP is the internet control message protocol: on top of tcp or udp, used for diagnostic and control purposes
- Packet is a small amount of data transmitted over a network

The differences between TCP and UDP:
- TCP is connection-oriented (handshake), UDP is connectionless
- TCP is reliable (verifies data get its destination), UDP is unreliable
- TCP is ordered, UDP is unordered
- TCP is heavyweight, UDP is lightweight
- UDP is usefull for streaming and faster than TCP

DNS/Nameserver:

- DNS is the domain name system
- Nameserver holds the DNS records to translate domain names into IP addresses

The computer talks to the nameserver to get the IP address of the domain name I want to access.

DNS records:
- A record: maps a domain name to an IP address
- CNAME record: maps a domain name to another domain name

We can look up the nameservers for a domain name with the following command:
```bash
nslookup google.com
```

We can look up the DNS records of a domain name with the following command:
```bash
dig google.com
```

Anatomy of an URL:

Url means uniform resource locator. It is a reference to a web resource that specifies its location on a computer network and a mechanism for retrieving it. A URL is a specific type of URI, although many people use the two terms interchangeably.

- Protocol: http, https, ftp, etc.
- Subdomain: www, api, etc.
- Domain name: google, facebook, etc.
- Top level domain: com, org, etc.
- Port: 80, 443, etc.
- Path: /, /about, etc.
- Query string: ?q=hello, etc.
- Fragment: #about, etc.

## Configure the server

Once you get a server and you can ssh into it, it is time to configure it. For this matter my server has Ubuntu 22 LTS installed.

First we upgrade and update the server:
```bash
apt update
apt upgrade
```

Then we shut down and restart the server:
```bash
shutdown -r now
```

Create a new user:

`root` is the default user, it has all the privileges. It is not a good idea to use it for security reasons.

We create a new user and give it the sudo (super user do) privileges so we can run programs and commands as root.

```bash
adduser john
# we add the new user to the sudo group
usermod -aG sudo john
# we switch to the new user (su)
su john
# we check the privileges running a command that requires sudo
sudo cat /var/log/auth.log
```

Once we have the new user, we can disable the root access:
- We create a file .ssh/authorized_keys in the new user home directory.
- We copy the public key of the user into the file.
- We exit the server and try to connect with the new user.
- We change the file permissions of the authorized_keys file to 644. `chmod 644 .ssh/authorized_keys`
- We disable the root access. `sudo vim /etc/ssh/sshd_config`
- We change the line `PermitRootLogin yes` to `PermitRootLogin no`
- We restart the ssh service. `sudo service sshd restart`
- We can use root again with `sudo -i`
- Now can can exit twice and test that we cannot connect with root anymore. Also test that we can connect with the new user.

## Setup nginx

Nginx (engine-x) is a web server write in C that can also be used as a reverse proxy or forward proxy.

A reverse proxy is a server that sits in front of web servers and forwards client requests to those web servers. Reverse proxies are typically implemented to help increase security, performance, and reliability.

We need first to instal nginx in our server:
```bash 
sudo apt install nginx
sudo service nginx start
```
If we go to the ip address of our server, we should see the nginx welcome page.

We can check out the nginx default site config file:
```bash
cat /etc/nginx/sites-available/default
```

Now we can install nodejs LTS version in our server using [nodesource](https://github.com/nodesource/distributions):
```bash
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash - &&\
sudo apt-get install -y nodejs
```

Now we change the ownership of the `/var/www/` directory to the new user so we do not need to sudo every time:
```bash
sudo chown -R $USER:$USER /var/www
```

Then we create a new directory for our app, we cd into it, initialize a new git repository and initialize a new npm project:
```bash
mkdir /var/www/app
cd /var/www/app
git init
npm init
```

We create a new file `app.js` with a basic nodejs server using http package:
```js
const http = require('http');

const server = http.createServer((req, res) => {
  res.end('Hello world!');
});

server.listen(3000);
console.log('Server listening on port 3000');
```

Now we need to tell nginx to proxy the requests to our nodejs server using the proxy pass directive.

We create a file for the virtual server in the sites-enabled directory:

```bash
sudo vim /etc/nginx/sites-enabled/app
```

The content of the file is something like this:

```nginx
server {
  listen 80 default_server;
  listen [::]:80 default_server;

  server_name example.com;

  location / {
    proxy_pass http://localhost:3000;
  }
}
```
We replace example.com with our domain name.  
[::]:80 is the ipv6 localhost address.

We can check if the nginx config is correct with:
```bash
sudo nginx -t
```

In that case we still need to point nginx to our new virtual server.

```bash
sudo vim /etc/nginx/nginx.conf
```
And include the new virtual server:
```nginx
##
# Virtual Host Configs
##

include /etc/nginx/conf.d/*.conf;
include /etc/nginx/sites-enabled/app; #we modify this line
```
We test again the nginx config:
```bash
sudo nginx -t
```

And if the config is ok we restart nginx:
```bash
sudo service nginx restart
```

And we run our nodejs server:
```bash
node app.js
```

But if we close the terminal now, our nodejs server will stop. We need to run it as a service, for that we will install pm2.

```bash
sudo npm install -g pm2
```

And run our nodejs server with pm2:
```bash
pm2 start app.js
# we can check the list of processes with
pm2 list
```

If we restart the server we want our application to run automatically. For that we need to save the pm2 processes and run them on startup:
```bash
pm2 save
pm2 startup
# copy and paste the command that pm2 gives us
```
