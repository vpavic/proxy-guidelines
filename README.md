## Guidelines for working behind a network proxy

This repository contains general guidelines and configuration samples for setting up development environment when working behind a corporate network proxy. The guidelines are targeted at Linux users however some parts are likely to be reusable for other operating systems.

### CNTLM

CNTLM is an NTLM/NTLMv2 authenticating HTTP proxy which sits between your applications and the corporate network proxy, adding NTLM authentication on-the-fly. You can specify several "parent" proxies and CNTLM will try one after another until one works. Authenticated connections are cached and reused to achieve high efficiency.

For further information on CNTLM refer to [its homepage](http://cntlm.sourceforge.net/).

CNTLM is your friend in corporate network environments, it allows you to manage your parent proxy(ies) together with authentication information (encrypted) and ignored hosts information in a centralized manner. This allows your applications to simply use ```localhost:3128``` (or whatever port you configured in your CNTLM configuration) as network proxy without having to configure authentication and ignored hosts.

If you switch often between corporate network which requires the use of proxy, and public networks which don't, consider using [NetworkManager](http://linux.die.net/man/8/networkmanager) dispatcher script available [here](https://github.com/vpavic/bash-scripts/blob/master/cntlm_noproxy_config.sh) to automatically reconfigure the CNTLM's ```NoProxy``` setting according to your current environment. Usage instructions are available in the comments within the script itself.

### System

See the ```/etc/environment``` [sample configuration file](samples/environment).

### Java

See the ```/etc/environment``` [sample configuration file](samples/environment).

### NPM

See the ```~/.npmrc``` [sample configuration file](samples/.npmrc).

### Bower

See the ```~/.bowerrc``` [sample configuration file](samples/.bowerrc).
