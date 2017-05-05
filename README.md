# ansible-cdn-letsencrypt

An Ansible playbook to set up a bunch of reverse proxies with a shared letsencrypt certificate

## Prerequisites

* ansible on your local host
* one blank controller server
* some blank proxy servers
* a domain pointing to your controller server eg. `ctrl.example.com`
* a domain pointing to your distribution eg. `www.example.com`

* an `inventory` file with content like this:
    ```
    [controller]
    x.x.x.x

    [proxies]
    y.y.y.y proxy_number=1
    z.z.z.z proxy_number=2
    ```


## Setting up the controller and deploying the 'stage-2' playbook

* run `ansible-playbook -i inventory playbook.yml`


## Initializing the proxy-servers and aquiring a certificate from letsencrypt

* ssh into the controller
* run `./init_proxies.sh`
* you should be fine to go now


## Renewing the certificate

* ssh into the controller
* run `./update_proxies.sh`


## Caveats

* *ssh hostkeys*: for now we dont automatically trust ssh host keys **workaround**:
    * ssh into all proxy servers from your mashine before startingt
    * ssh into all proxy server from the controller before running `./init_proxies.sh`
    **or**
    * run `./trust_hosts.sh x.x.x.x y.y.y.y z.z.z.z` from your machine before starting
    * run `./trust_hosts.sh y.y.y.y z.z.z.z` from the controller before running `./init_proxies.sh`

* *renewal cronjob*: for now we dont automatically renew the certificate **todo**:
    * automate certificate renewal with a cronjob

* *adding new proxies*: this is currently **untested**:
    * theoretically updating the `inventory` file, re-deploying the 'stage-2' playbook and executing `./update_proxies` on the controller should work


## Todo

* handle multiple domains (nginx config and letsencrypt)
* automate ssh trust on first use (notebook/controller -> proxies)
* automated tests?
* test adding another proxy after the initial setup
* i think letsencrypt allows serving the challenge over https with a self signed certificate.
    if this is true, we can remove `playbook_lb_initial.yml` and just deploy the production proxy configuration, just with a self signed ssl certificate.

## Disclaimer

```
I am not responsible for damages to your soft- or hardware or bugs eating your cat that occur through usage of this code or part of it.
```
