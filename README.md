# balena-nginx-raspberry

This project was created in order to use a Raspberry pi 4 as a production server for a low-traffic personal website and blog. 

# Components:
- **Nginx webserver:** Dockerfile based on [rpi-nginx](https://github.com/Tob1asDocker/rpi-nginx) repository
- **Certbot:** Letsencrypt client used to generate SSL certificates. [Certbot](https://certbot.eff.org/)
- **Netdata monitoring:** Dockerfile + Docker-compose, based on [Netdata](https://github.com/netdata/netdata)
- **ddclient** Dynamic DNS set: Dockerfile based on [ddclient](https://github.com/ddclient/ddclient)
- **balena** for code deployment on Raspberry pi: [Balena.io](https://balena.io)

# Usage:


>Using balena.io, after performing the necessery configuration changes, just push the project to a Raspberry pi and it will setup automatically. **It's that easy.**

# Configuration:

To use **build secrets** and copy confidential files into the device, we use a special folder named `.balena`, according to the Build-time secrets functioanlity as illusrated in the [docs](https://www.balena.io/docs/learn/deploy/deployment/#build-time-secrets-and-variables).

To use **environment variables** we use the functionality of balena.io platform, so that we can change dynamically the environment variables. In our case, we want to enable CERTBOT once (=1) to get the certificate and then disable it (=0). The certificates will be *saved* into a **named volume** which persists across container updates & new releases.

Reference:
 - balena.io environment variables: [Docks](https://www.balena.io/docs/learn/manage/serv-vars/)
 - balena.io named volumes: [Docks](https://www.balena.io/docs/learn/develop/multicontainer/#named-volumes)

## Build Variables/Secrets
  - `REPO_ZIP_URL`, link to the zip archive of the website's repository (e.g  https://github.com/OdysLam/odyslam.github.io/)
  - `REPO_NAME`, name of the website's repository (e.g odyslam.github.io)
  - `CERTBOT_MAIL`, mail for ssl certificate
  - `CERTBOT_DOMAIN_1`, website domain (e.g www.odyslam.me)
  - `CERTBOT_DOMAIN_2`, website domain (e.g odyslam.me)
  - `ddclient.conf`, configuration for ddclient
  - `dhparam.pem`, DH parameters generated with OpenSSL for SSL. [Example](https://scaron.info/blog/improve-your-nginx-ssl-configuration.html)

## Environment Variables

- `CERTBOT = [1 OR 0]`, it signals to the service wether to commence the certbot ssl certification functionality

## Balena

Balena is a novel platform that empowers IoT developers by facilitating the development and deployment of IoT applications to Edge devices. It brings Cloud Natives paradigms, such as container-based virtualization and CI/CD pipelines to the world of IoT.

We simply define the application as a docker-compose microservices application and balena.io builds it on the cloud and delivers ready-to-run containers to our balenaOS device. It uses a modified version of docker that is highly optimized for Edge devices. In fact, balenaOS supports a multitude of devices, unifying the experience for each and every to the same workflow.

## Static Website:

The project assumes that the website source files (html, js, css, assets) are hosted on a public repository of Github and are downloaded with each build, at build-time. 

**NginX** is used as the webserver of choice because it is highly performant, something essential for the size-factor of a small computer, such as the [Raspberry Pi 4](https://www.raspberrypi.org/products/raspberry-pi-4-model-b/specifications/).

Note that in order for nginx to function properly, it needs the certificates/private keys that are generated with `certbot` and a `dhparam.pem` that can be generated on the developer's machine. Thus, if running the webserver for the first time, enable the appropriate environment variable so that  `certbot` can generated the required keys.

## Netdata

Netdata is used to monitor the device's resources and the connection status of Nginx. It used the `/stub_status` page of nginx in order to aggregate information and present it in the dashboard.

## ddclient


As the webserver is hosted on a private internet connection, the ISP provides Dynamic IP and thus we have to enable Dynamic DNS to our Domain provider so that the domain always points to the correct IP. 

ddclient is a small `perl` program that has pre-configured functionality for known domain providers and is set to run every 1 minute in order to always keep the IP up-to-date.

In our use-case, the domain provider is Namecheap, thus:

- Namecheap dynamic DNS: [Namecheap Docs](https://www.namecheap.com/support/knowledgebase/subcategory/11/dynamic-dns/)
- ddclient configuration: [Namecheap Docs](https://www.namecheap.com/support/knowledgebase/article.aspx/583/11/how-do-i-configure-ddclient)




