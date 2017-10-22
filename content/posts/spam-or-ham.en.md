+++
title = "Spam or Ham"
lastmod = 2017-10-22T12:04:13-05:00
tags = ["email", "security"]
categories = ["site-related"]
draft = false
date = 2017-10-14
slug = "spam-or-ham"
+++

As planned, I am documenting my mail server setups. Setting up the mail server is probably documented everywhere, but I had to put in some effort make my setup secure enough to prevent it from been mistaked as spam.


## Setting up the mail server {#setting-up-the-mail-server}

I really don't see how I can write anything better than [this tutorial](http://www.netarky.com/programming/arch_linux/Arch_Linux_mail_server_setup_1.html), so I will just document some of the steps that seemed missing from the tutorial.


### Setting DNS Record {#setting-dns-record}

Before anything, I needed to setup my DNS record. I created a `CNAME` for my mail server address, and added a `MX` record indicating the mail will be handled by the mail server.


### Creating `Maildir` {#creating-maildir}

After setting up `postfix` for the first time, I needed to setup the `Maildir` manually and giving it appropirate permissions:

```sh
$ mkdir -p /home/<username>/Maildir/{cur,new,tmp}
$ chown <username> /home/<username>/Maildir/{,cur,new,tmp}
$ chmod 0755 /home/<username>/Maildir/{,cur,new,tmp}
```


### SSL Certificate {#ssl-certificate}

In stead of using the built-in certificate generators in `dovecot`, I choose to use the same SSL certificate for my website. I added my mail server address to the `server_name` field in `/etc/nginx/nginx.conf` and generated my certificate with `certbot`. After that, I simply changed `/etc/dovecot/conf.d/10-ssl.conf` for `dovecot` :

```sh
use_ssl = yes
ssl_cert = </path/to/fullchain.pem
ssl_key = </path/to/privkey.pem
```

Similarly for `postfix` I also used this certificate. Do note that `dovecot` and `postfix` should be run as `root` to have read permissions to read these certificates.


### Mail Client {#mail-client}

I am using Thunderbird as my mail client and for receiving mail. I used SSL/TLS while for sending mail, I needed to set STARTTLS.


## Security Measures {#security-measures}

After completing the email setup, I immediately tested the server by sending test emails, only to find them been tossed straight into spam by gmail. It seems that gmail has a new feature that shows the security check status on the email (accessible by 'View Original'). These measures include SPF, DKIM and DMARC. My avatar showed up as an octagon with a question mark, indicating the mail server failing the basic SPF check. In order to avoid this, I took a bunch of security measures to tick all the boxes from email security test sites like [intodns](https://intodns.com) and [mxtoolbox](https://mxtoolbox.com).


### Sender Policy Framework (SPF) {#sender-policy-framework--spf}

An SPF TXT record documents the allowed servers to send emails on behalf of this address. In my case where only mail servers documented in the MX TXT record are used, I simply put in:

```sh
v=spf1 mx -all
```


### DomainKeys Identified Mail (DKIM) {#domainkeys-identified-mail--dkim}

I am using `opendkim` to sign and verify that emails are indeed from my server. After installing the `opendkim` package, I followed the instruction in [Arch Wiki](https://wiki.archlinux.org/index.php/OpenDKIM). First copy example configuration file from `/etc/opendkim/opendkim.conf.sample` to `/etc/opendkim/opendkim.conf` and edit (socket selection can be arbitrary):

```sh
Domain                  <domainname>
KeyFile                 /path/to/keys.private
Selector                <myselector>
Socket                  inet:<dkimsocket>@localhost
UserID                  opendkim
Conicalization          relaxed/simple
```

Next, in the specified keyfile directory (the default is `/var/db/dkim/`), generate keys with:

```sh
$ opendkim-genkey -r -s <myselector> -d <domainname> --bits=2048
```

Along with the generated `.private` file is a `.txt` file with the necessary TXT record for DKIM. It basically posts the public key for your mail server. Note that the TXT record may need to be broke down into several strings to comply with the 255 character limit. To check if the TXT record has been properly setup, I used (requires package `dnsutils` ):

```sh
$ host -t TXT <myselector>._domainkey.<domainname>
```

The final step would be to start the `opendkim` service and  make sure `postfix` performs the encryption upon sending email. Edit `/etc/postfix/main.cf` to be:

```sh
non_smtpd_milters=inet:127.0.0.1:<dkimsocket>
smtpd_milters=inet:127.0.0.1:<dkimsocket>
```

After reloading `postfix`, DKIM should be in effect.


### Domain-based Message Authentication, Reporting and Conformance (DMARC) {#domain-based-message-authentication-reporting-and-conformance--dmarc}

Without surprise, there is a package `opendmarc` that implements DMARC and there is also an [Arch Wiki](https://wiki.archlinux.org/index.php/OpenDMARC) page for it. Do note that this would require SPF and DKIM to be setup first. After installation, I edited `/etc/opendmarc/opendmarc.conf`:

```sh
Socket inet:<dmarcsocket>@localhost
```

After starting the service, enable DMARC filter in `postfix` (separate with comma):

```sh
non_smtpd_milters=inet:127.0.0.1:<dkimsocket>, inet:127.0.0.1:<dmarcsocket>
smtpd_milters=inet:127.0.0.1:<dkimsocket>, inet:127.0.0.1:<dmarcsocket>
```

The final step is to add a DMARC TXT record in DNS settings as detailed on Arch Wiki page and reload `postfix`.


## Ticking the Boxes {#ticking-the-boxes}

I tested my server by sending test email to `check-auth@verifier.port25.com` and everything seems to be working. Not to mention that my email no longer gets classified as spam by gmail and I can see my emails passing SPF, DKIM and DMARC checks in 'View Original'. I also get an detailed daily report from gmail due to DMARC. At this point, I am pretty comfortable about ditching all my previous gmail addresses and sticking to my own email. I am also looking into options of self-hosting calenders. Hopefully in the near future I can completely ditch Google for my essential communication needs.