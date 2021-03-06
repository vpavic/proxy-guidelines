## Guidelines for working behind a network proxy

This repository contains general guidelines and configuration samples for setting up development environment when working behind a corporate network proxy. The guidelines are targeted at Linux users however some parts are likely to be reusable for other operating systems.

### Tools

#### CNTLM

CNTLM is an NTLM/NTLMv2 authenticating HTTP proxy which sits between your applications and the corporate network proxy, adding NTLM authentication on-the-fly. You can specify several "parent" proxies and CNTLM will try one after another until one works. Authenticated connections are cached and reused to achieve high efficiency.

For further information on CNTLM refer to [its homepage](http://cntlm.sourceforge.net/).

CNTLM is your friend in corporate network environments, it allows you to manage your parent proxy(ies) together with authentication information (encrypted) and ignored hosts information in a centralized manner. This allows your applications to simply use ```localhost:3128``` (or whatever port you configured in your CNTLM configuration) as network proxy without having to configure authentication and ignored hosts.

If you switch often between corporate network which requires the use of proxy, and public networks which don't, consider using [NetworkManager](http://linux.die.net/man/8/networkmanager) dispatcher script available [here](https://github.com/vpavic/bash-scripts/blob/master/cntlm_noproxy_config.sh) to automatically reconfigure the CNTLM's ```NoProxy``` setting according to your current environment. Usage instructions are available in the comments within the script itself.

#### Corkscrew

Corkscrew is a tool for tunneling SSH through HTTP proxies.

For further information on Corkscrew refer to [its homepage](http://agroman.net/corkscrew/).

Corkscrew complements CNTLM nicely in environments that don't allow the use of SSH and force the use of an HTTP proxy, since it makes possible to use that HTTP proxy as a transport for SSH.

### Configuration

#### System

You should configure your environment to be aware of your network proxy. This is important since most well-behaved native applications will check for well known environment variable to discover information about network proxy. See [this article](https://wiki.archlinux.org/index.php/proxy_settings) for more details.

The best place to define system-wide environment variables is ```/etc/environment``` file which is specifically meant for that purpose. Please note that variable expansion does not work within ```/etc/environment``` since it is not a script file, but rather consists of assignment expressions.

Here's an example of ```/etc/environment``` network proxy configuration:

```shell
http_proxy="http://localhost:3128/"
https_proxy="http://localhost:3128/"
```

See the ```/etc/environment``` [sample configuration file](https://github.com/vpavic/proxy-guidelines/blob/master/samples/environment).

#### Java

Many Java applications will check the JVM system properties to discover information about network proxy. See [this article](https://docs.oracle.com/javase/8/docs/technotes/guides/net/proxies.html) for more details. Such JVM system properties should be configured using special environment variable named ```JAVA_TOOL_OPTIONS``` which will be picked up by the JVM.

If you have configured system proxy environment variables described in the previous section, you can use the following ```JAVA_TOOL_OPTIONS``` environment variable declaration:

```shell
JAVA_TOOL_OPTIONS="-Djava.net.useSystemProxies=true"
```

Alternatively, you can use the following ```JAVA_TOOL_OPTIONS``` environment variable declaration to explicitly configure the proxy details:

```shell
JAVA_TOOL_OPTIONS="-Dhttp.proxyHost=localhost -Dhttp.proxyPort=3128 -Dhttps.proxyHost=localhost -Dhttps.proxyPort=3128"
```

See the ```/etc/environment``` [sample configuration file](https://github.com/vpavic/proxy-guidelines/blob/master/samples/environment).

#### NPM

NPM (node package manager), according to its [configuration documentation](https://docs.npmjs.com/misc/config), should honor the System network proxy configuration. However, in practice this doesn't always work as advertised (possibly depending on NPM version), so if you run into problems try with explicit configuration of network proxy:

```shell
npm config set proxy http://localhost:3128/
npm config set https-proxy http://localhost:3128/
```

After executing ```npm config``` commands the configuration will be saved to ```~/.npmrc``` file. As an alternative to using these commands you can edit this file manually.

See the ```~/.npmrc``` [sample configuration file](https://github.com/vpavic/proxy-guidelines/blob/master/samples/.npmrc).
