# Apache (web server)

## Background

This guide describes how to set up a static web page on Ubuntu using Apache.
Apache is popular web server.
It has been around since 1995 and is therefore stable in the world of website
hosting.

A static web page means that files are served over HTTP as is.
All users visiting the site, will see the exact same page.
This is in contrast to dynamic web page, in which content of the page will show
up differently based on user input.
Just like when you visit Facebook or YouTube, the content shown will be very
different from what your mom sees.

A static website is sufficient for a basic marketing website.
Though many real world marketing website use a content management system (CMS),
like Wordpress, to allow none technical people to change the content.

## What you will learn

Following this guide, you will learn the basics of:

- Updating and installing software
- Management of services
- Managing Apache virtual hosts
- Firewall rules on Ubuntu

## Prerequisite

You will need root access to a server running Ubuntu.
This guide has been tested with version 26.04, but should work with other
versions of Ubuntu as well.

You don't need a lot of server resources for this.
1 core and 2GiB RAM and 10GiB disk should be enough.

SSH access is recommended, but not strictly required.

## Install Apache

Start by making sure your server is up-to-date.

```bash
sudo apt update
sudo apt upgrade -y
```

The `sudo apt update` command will update the list of available packages.
Then `sudo apt upgrade -y` will install any upgrades to the current installed
packages.
The `-y` argument ensures the packages gets upgraded without further questions,
as `-y` means answer yes to all questions.

Apache is available under the package name `apache2`, so you can install it with the following command:

```bash
sudo apt install apache2 -y
```

Services on most Linux distributions are managed with the `systemctl` command.
A service is a process that is meant to run in the background without any
direct user interaction.
Services are commonly started automatically when the OS boots, or when required
by other software.
Apache runs as a service, since it is meant to be access through a web browser
rather than directly through the terminal.

You can verify that the `apache2` service is running with:

```bash
sudo systemctl status apache2
```

It should say `active (running)` in green, somewhere in the output.
Press `q` to exit the status view.

## Configure firewall

Ubuntu comes with a firewall called **ufw**, which stands for uncomplicated
firewall.
Many packages on Ubuntu, ships with firewall profiles.

You can see the profiles you have available with:

```bash
sudo ufw app list
```

There are 3 different profiles for Apache.

| Profile | Ports opened |
|---|---|
| `Apache` | 80 (HTTP) only |
| `Apache Secure` | 443 (HTTPS) only |
| `Apache Full` | 80 and 443 |

We want to allow `Apache Full` profile which allows both HTTP and HTTPS traffic
on the default ports.
Also, if you access the server over SSH, you must allow the "OpenSSH" profile,
otherwise you will lose access.

```bash
sudo ufw allow 'Apache Full'
sudo ufw allow 'OpenSSH'
```

Next, you need to make sure the firewall is enabled and got the profiles
loaded.
You can check if the firewall is enabled with:

```bash
sudo ufw status
```

On a fresh Ubuntu install it will say `Status: inactive`, meaning that the
firewall haven't been enabled yet.
To make sure the firewall is enabled, and the right rules are set, do:

```bash
sudo ufw enable
sudo ufw reload
```

Then check the status again with the command from above.
It should look like this:

```
To                         Action      From
--                         ------      ----
Apache Full                ALLOW       Anywhere
OpenSSH                    ALLOW       Anywhere
Apache Full (v6)           ALLOW       Anywhere (v6)
OpenSSH (v6)               ALLOW       Anywhere (v6)
```

You will see a duplicate of the rules with "v6" in the description.
It simply means that the rule/profile applies to IPv6 also.

Maybe you are wondering what happens to all traffic we haven't specified a rule
for?
The default is to allow outgoing connections and deny all incoming.
You can view the default rule by running:

```bash
sudo ufw status verbose
```

## Verify web server and firewall rules

It might be a good time to check that everything works so far.
Apache comes with a default web-page, let's try if we can access it.

You need to find the servers IP address.
This can be done in a couple of different ways.

One with that works across just about every Linux distributions is with:

```bash
ip address
```

It shows both IPv4, IPv6 and MAC addresses for all network interfaces of the system.
There is a special loopback device (`lo`) with the IPv4 `127.0.0.1` or IPv6
`::1`, which redirects all traffic to the system itself.

The command `curl` is commonly used to make web request from the terminal.
We can use it to check that Apache works from the server itself.

```bash
curl -I http://127.0.0.1
```

If you see `HTTP/1.1 200 OK` it means that it is working.
The `-I` argument instructs `curl` to only print the headers of the response.
If you run the command again without `-I` then you will see the response body.

Systems typically have the host name `localhost` pointing to `127.0.0.1`, so
that should work as well.

```bash
curl -I http://localhost
```

Next, try to access the page from a web browser on your own computer.
Using the IP `127.0.0.1` won't work since on your computer it will loop back to
your own machine.

Let's try a different command to find the servers IP.
This command might no work on all Linux variants, but on Ubuntu, it likely
gives you just the IP you are interested in.

```bash
hostname -I
```

Open `http://YOUR_SERVER_IP` in a web browser, replacing `YOUR_SERVER_IP` with
the IP of your server.
You should see the default **Apache2 Ubuntu Default Page**.

![Screenshot of Apache2 Ubuntu Default Page](./apache-default-page.png)

By default, Apache serves files from `/var/www/html`.
If you `cat` the `index.html` file:

```bash
cat /var/www/html/index.html
```

You will see the same as if you made a request with `curl`:

```bash
curl http://localhost
```

## Create your own static site

### Create the directory

```bash
sudo mkdir -p /var/www/example.com/public_html
```

### Set ownership and permissions

Give your user ownership so you can edit files without `sudo`, and make sure Apache (which runs as `www-data`) can read them:

```bash
sudo chown -R $USER:$USER /var/www/example.com
sudo chmod -R 755 /var/www/example.com
```

`755` means the owner can read/write/execute, while everyone else (including Apache) can read and traverse directories.

### Create an `index.html`

```bash
nano /var/www/example.com/public_html/index.html
```

Paste the following:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Welcome to example.com</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <main>
    <h1>It works!</h1>
    <p>This static site is served by Apache on Ubuntu.</p>
  </main>
</body>
</html>
```

Save and exit (`Ctrl+O`, `Enter`, `Ctrl+X`).

### Add a stylesheet (optional)

```bash
nano /var/www/example.com/public_html/style.css
```

```css
body {
  font-family: system-ui, sans-serif;
  background: #f5f7fa;
  color: #222;
  display: grid;
  place-items: center;
  min-height: 100vh;
  margin: 0;
}

main {
  text-align: center;
}
```

## Set up a Virtual Host for your site

### Create a Virtual Host configuration

A **virtual host** tells Apache which directory to serve for which domain name.
This lets one server host many sites.

Create a new configuration file:

```bash
sudo nano /etc/apache2/sites-available/example.com.conf
```

Add the following:

```apache
<VirtualHost *:80>
    ServerName example.com
    ServerAlias www.example.com
    ServerAdmin webmaster@example.com

    DocumentRoot /var/www/example.com/public_html

    <Directory /var/www/example.com/public_html>
        Options -Indexes +FollowSymLinks
        AllowOverride None
        Require all granted
    </Directory>

    ErrorDocument 404 /404.html

    ErrorLog ${APACHE_LOG_DIR}/example.com-error.log
    CustomLog ${APACHE_LOG_DIR}/example.com-access.log combined
</VirtualHost>
```

### Enable your site and disable the default one

```bash
sudo a2ensite example.com.conf
sudo a2dissite 000-default.conf
sudo systemctl reload apache2
```

> Disabling the default site is recommended so requests that don't match any
> `ServerName` don't fall through to the placeholder page. If you'd like to
> keep the default site as a catch-all, skip the second command (the
> first-loaded configuration file acts as the default).

### Test the configuration

#### Check for syntax errors

```bash
sudo apache2ctl configtest
```

You should see:

```
Syntax OK
```

If you see a warning like `Could not reliably determine the server's fully qualified domain name`, it's harmless, but you can fix it:

```bash
echo "ServerName localhost" | sudo tee /etc/apache2/conf-available/servername.conf
sudo a2enconf servername
```

#### Test in web browser

Open `http://YOUR_SERVER_IP` in a web browser, replacing `YOUR_SERVER_IP` with
the IP of your server.

### Note on domain names (optional)

Normally each Virtual Host will have its own domain name.
But it will be a bit excessive to buy a bunch of domains, just to follow along
this guide.
Therefore, we skipped the part on setting a domain name for the Virtual Host.
If interested, you can add the following at line 2 of `/etc/apache2/sites-available/example.com.conf` file:

```apache
    ServerName example.com
    ServerAlias www.example.com
    ServerAdmin webmaster@example.com
```

Reload Apache:

```bash
sudo systemctl reload apache2
```

Test with:

```bash
curl -H "Host: example.com" http://localhost
```

Now remove the `SeverName`, `ServerAlias` and `ServerAdmin` lines again from
the `/etc/apache2/sites-available/example.com.conf` file.
And do another reload:

```bash
sudo systemctl reload apache2
```

## Optional Hardening and Performance Tweaks

### Hide Apache version information

If you run:

```bash
curl -I http://localhost
```

You will see somewhere, it says `Server: Apache/2.4.66 (Ubuntu)`.
Attackers can use this information to check if there are any known
vulnerabilities for that particular version of Apache that can be exploited.
We can disable the version information as follows.

```bash
sudo nano /etc/apache2/conf-available/security.conf
```

Set these values:

```apache
ServerTokens Prod
ServerSignature Off
```

**Note:** any line beginning with `#` are not interpreted as part of the
configuration, they are only there for documentation.

Apply the change:

```bash
sudo systemctl reload apache2
```

Test with:

```bash
curl -I http://localhost
```

### Add security headers

Enable the headers module:

```bash
sudo a2enmod headers
```

Open the configuration:

```bash
sudo nano /etc/apache2/sites-available/example.com.conf
```

Add the following inside your `<VirtualHost>` block.

```apache
Header always set X-Content-Type-Options "nosniff"
Header always set X-Frame-Options "SAMEORIGIN"
Header always set Referrer-Policy "strict-origin-when-cross-origin"
```

Apply the change:

```bash
sudo systemctl reload apache2
```

Test with:

```bash
curl -I http://localhost
```

### Enable compression

Compression reduces bandwidth and speeds up page loads.
The `deflate` module is usually enabled by default; confirm with:

```bash
sudo a2enmod deflate
```

Open the configuration:

```bash
sudo nano /etc/apache2/sites-available/example.com.conf
```

Add the following inside your `<VirtualHost>` block.

```apache
<IfModule mod_deflate.c>
    AddOutputFilterByType DEFLATE text/html text/plain text/css
    AddOutputFilterByType DEFLATE application/javascript application/json image/svg+xml
</IfModule>
```

Check the configuration and apply the changes:

```bash
sudo apache2ctl configtest && sudo systemctl reload apache2
```

Test:

```bash
curl -I --compressed http://localhost
```

You should see `Content-Encoding: gzip`.
Also, take a look at the `Content-Length` header.
It is the number of bytes in the response and should be smaller than if ran the
command without `--compressed`.
Web browsers will automatically ask for compressed response if available.

### Enable browser caching

The best way to speed up the loading of your website, and reduce the load on
your server, would be if your browser could just show the page, without having
to make a request to the server.
This can easily be done by instructing the browser to cache the website.

```bash
sudo a2enmod expires
```

Open the configuration:

```bash
sudo nano /etc/apache2/sites-available/example.com.conf
```

Add the following inside your `<VirtualHost>` block.

```apache
<IfModule mod_expires.c>
    ExpiresActive On
    ExpiresByType text/html     "access plus 1 minute"
    ExpiresByType text/css      "access plus 1 week"
    ExpiresByType application/javascript "access plus 1 week"
    ExpiresByType image/jpeg    "access plus 1 month"
    ExpiresByType image/png     "access plus 1 month"
    ExpiresByType image/webp    "access plus 1 month"
    ExpiresByType image/svg+xml "access plus 1 month"
</IfModule>
```

Check the configuration and apply the changes:

```bash
sudo apache2ctl configtest && sudo systemctl reload apache2
```

To test, open `http://YOUR_SERVER_IP` in a web browser.
Hit <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>I</kbd> then click "Network" tab.
Now, reload the page.

Before:

![Page load without cache](./no-cache.png)

After:

![Page load with cache](./with-cache.png)
