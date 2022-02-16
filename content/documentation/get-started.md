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
Besides that you can run the console on its own aka *standalone mode*. HAL is a RIA with no server side dependencies. When run on its own, HAL will show a dialog when it's started. You have to provide the URL of the management endpoint you want to connect to. This is typically the one which uses port 9990. You can add as many endpoints as you want. They're stored in the browser local storage and survive a browser restart. 

If you want to skip the connection dialog and connect to a previously defined endpoint, use the `connect` request parameter. If the console runs on port 9090, you'd use a URL like http://localhost:9090/?connect=Development.

If you want to connect to a URL not stored by name, you can also provide the URL of the management endpoint to the `connect` parameter like in http://localhost:9090/?connect=http://localhost:9990.

The standalone mode allows you to manage different WildFly and / or JBoss EAP instances using the same console. Furthermore, you can always use the latest HAL version.

There are different ways to use the standalone mode. All of them require to configure the allowed origins of the http(s) management endpoint:
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

The URL depends on how you launch the console. You can choose between one of the following options:

## Native Binary

The standalone version of HAL is available as native binary for Linux, macOS and Windows. You can download the native binary for your platform from the latest [HAL release](https://github.com/hal/console/releases). It's based on Quarkus and listens to port 9090 by default. If you want to listen to a different port use the `quarkus.http.port` property to specify a different port. If you're on macOS, run 

```shell
halconsole-<version>-macos -Dquarkus.http.port=9123
```

Then add https://localhost:9123 as allowed origin.

## Container

In addition, the standalone mode is also available as a container image at [`quay.io/halconsole/hal`](https://quay.io/repository/halconsole/hal). It's based on the native Linux binary and listens to port 9090 by default. Start the container using 

```shell
docker run --publish 9123:9090 quay.io/halconsole/hal
```

Then add https://localhost:9123 as allowed origin. 

## GitHub Pages

The latest stable version of HAL is also available at GitHub:

1. Add https://hal.github.io as allowed origin
1. Open https://hal.github.io/console/

GitHub Pages are served from https, so you need to secure the management interface as well. Please note that if you're using a self-signed key store you might need to open the local management endpoint in the browser and accept the unsafe certificate once, before you can use it with HAL.
