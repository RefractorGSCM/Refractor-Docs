# Requirements

Refractor is a self-hosted web application. Because of this, you are required to have some prerequisites sorted out before deploying your own instance of Refractor.

### Server Requirements

You will require a Linux server which has both Docker and Docker Compose installed. The server should have at least 512MB of memory. A cheap Virtual Private Server (VPS) would suffice.

See the links below to install Docker and Docker Compose:

[Install Docker](https://docs.docker.com/engine/install/)

[Install Docker Compose](https://docs.docker.com/compose/install/)

### Domain Name

Refractor is intended to be used with a reverse proxy which links the various services and provides encryption on all traffic to keep you, your users and your server's data and credentials secure.

Because of this, you are required to have a domain name purchased and pointing to the server which you are deploying Refractor on. You can purchase a domain from any registrar (e.g Namecheap, Google Domains, etc).

Once you have access to a domain, you need to point it to your server. There are different ways to go about this. Two methods will be briefly explained below.

1. If you are deploying Refractor on a Virtual Private Server (VPS), then you may wish to give control of your domain's DNS to your VPS provider. You do this by setting your domain's nameservers to your VPS provider's nameservers. From there, you can use your VPS provider's DNS tooling to create an **A** record pointing to the IP address of your VPS.

2. If you are not using a VPS or you simply do not wish to use the method above, you can use your domain registrar's DNS settings to create an **A** record that points to your server's IP. The steps to do this will differ from registrar to registrar.

**It is highly recommended that you deploy Refractor on a subdomain**. This is so that if you already have or may in the future have another website running on your domain (e.g `yourdomain.com`) you can avoid re-deploying Refractor. Something like `refractor.yourdomain.com` would work fine.

> **Note:** DNS record propagation is fairly quick nowadays, but it is not instant. It is recommended that you wait for at least an hour or two after creating an A record to your server before you attempt deploying Refractor. In some cases, it can take up to 48 hours for DNS records to fully propagate.

### SMTP

Refractor is requires access to an SMTP server capable of sending emails. If you have web hosting, chances are you already have access to an SMTP server. If you don't have access to an SMTP server, you can use a service like [MailGun](https://www.mailgun.com) or any other suitable alternatives.

### Low Memory Notice

For Linux systems with low amounts of memory (under 2GB), it is highly recommended that you set up a swapfile to speed up the installation of Refractor. The steps to do this will differ depending on what operating system you're using.

### OS Compatibility

Refractor was built for Linux machines. Currently, it has only been tested on Ubuntu 20.04. It is unknown whether or not it functions properly on Windows machines.
