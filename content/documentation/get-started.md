---
title: "Get Started"
date: 2018-03-08T16:28:42+01:00
description: "Describes how to get started quickly. Shows alternative ways to launch and use the console."
icon: "/img/rocket.png"
toc: true
weight: 10
---
The HAL management console is part of every WildFly and JBoss EAP installation. To get started simply fire up your browser and open [http://localhost:9990](http://localhost:9990). 

# Standalone Mode

{{< imgflow src="/img/documentation/connect.png" float="right" >}}
You can run the console on its own aka *standalone mode*. This mode starts a local web server and serves HAL on its own without being part of a WildFly installation. HAL is "just" a single page application ([SPA](https://en.wikipedia.org/wiki/Single-page_application)) without any server side dependencies. The only requirement is a management interface of a running WIldFly instance.

When run on its own, HAL will show a dialog when it's started. You have to provide the URL of the management interface you want to connect to. This is typically the one which uses port 9990. You can add as many interfaces as you want. They're stored in the browser local storage and survive a browser restart. 

If you want to skip the connection dialog and connect to a previously defined interface, use the `connect` request parameter. If the console runs on port 9090, you'd use a URL like http://localhost:9090/?connect=Development.

If you want to connect to an interface not stored by name, you can also provide the URL of the management interface directly to the `connect` parameter like in http://localhost:9090/?connect=http://localhost:9990.

The standalone mode has some advantages over the traditional mode:

- Use the latest version  
  You can use the latest HAL version with the latest features and bugfixes even before the version is bundled into the next WildFly major release. Check the GitHub [repository](https://github.com/hal/console) for the latest [release](https://github.com/hal/console/releases).

- One console to rule them all  
  You can use one console to connect to arbitrary WildFly instances. The console can manage and store the connections in an internal registry.

## Prerequisites

The console uses the [fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) to talk to the management interface of a running WildFly instance. When the console is started on its own, the origins of HAL and WildFly are different, and we need to tell WildFly that HAL is allowed to make requests to the management interface (see [CORS policies](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) for more details).

To do this we can add URLs as allowed origins to the http(s) management interface. Open the WildFly CLI and execute the following command
{{</ imgflow >}}

**Standalone Mode**

```shell
/core-service=management/management-interface=http-interface:list-add(name=allowed-origins,value=<url>)
reload
```
**Domain Mode**

```shell
/host=master/core-service=management/management-interface=http-interface:list-add(name=allowed-origins,value=<url>)
reload --host=master
``` 

The URL depends on how you launch the console.

If you don't want to bother with configuring URLs, use one of the images from [quay.io/halconsole/wildfly](https://quay.io/repository/halconsole/wildfly) or the scripts provided by [github.com/hal/wildfly](https://github.com/hal/wildfly). You get WildFly containers for each major version with preconfigured allowed origins ready to be used with the options below.

## Native Binary

HAL is available as a native binary for Linux, macOS and Windows. You can download the native binary for your platform from the latest [HAL release](https://github.com/hal/console/releases). If you're on macOS, run

```shell
halconsole-<version>-macos
```

The native binary listens to port 9090 by default. If you want to listen to a different port use `-Dquarkus.http.port=<port>` to change the port. Then add https://localhost:\<port\> as allowed origin.

## Container

Another option is to use the container image from [quay.io/halconsole/hal](https://quay.io/repository/halconsole/hal). Start the container using

```shell
docker run quay.io/halconsole/hal
```

The container publishes port 9090 by default. If you want to listen to a different port, use `--publish <port>:9090`. Then add https://localhost:\<port\> as allowed origin.

## Online

Finally, the latest stable version of HAL is also available at GitHub:

1. Add https://hal.github.io as allowed origin
1. Open https://hal.github.io/console/

GitHub Pages are served from https, so you need to [secure the management interface](https://docs.wildfly.org/26/WildFly_Elytron_Security.html#enable-one-way-ssltls-for-the-management-interfaces) as well. Please note that if you're using a self-signed key store you might need to open the local management interface in the browser and accept the unsafe certificate once, before you can use it with HAL.

{{< callout info >}}
The link above points to the documentation for WildFly 26 (time of writing). To find the most recent version

1. open https://docs.wildfly.org/
1. choose the latest version
1. open the "Security Guide"
1. go to section "Configure SSL/TLS" and
1. find the subsection "Enable One-way SSL/TLS for the Management Interfaces"
{{< /callout >}}
