---
title: "Get Started"
date: 2018-03-08T16:28:42+01:00
description: "Describes how to get started quickly. Shows alternative ways to launch and use the console."
icon: "/img/rocket.png"
toc: true
weight: 10
---
The HAL management console is part of every WildFly and JBoss EAP installation. To get started simply fire up your browser and open http://localhost:9990. 

# Standalone Mode

{{< imgflow src="/img/documentation/connect.png" float="right" >}}
Besides that you can run the console on its own aka *standalone mode*. HAL is a RIA with no server side dependencies. When run on its own, HAL will show a dialog when it's started. You have to provide the URL of the management endpoint you want to connect to. This is typically the one which uses port 9990. You can add as many endpoints as you want. They're stored in the browser local storage and survive a browser restart. 

If you want to skip the connection dialog and connect to a previously defined endpoint, use the `connect` request parameter. If the console runs on port 9090, you'd use an URL like http://localhost:9090/?connect=Development.

The standalone mode allows you to manage different WildFly and / or JBoss EAP instances using the same console. Furthermore you can always use the latest HAL version.

There are different ways to use the standalone mode. All of them require to configure the allowed origins of the http(s) management endpoint:
{{</ imgflow >}}

**Standalone Mode**

```bash
/core-service=management/management-interface=http-interface:list-add(name=allowed-origins,value=<url>)
reload
```
**Domain Mode**

```bash
/host=master/core-service=management/management-interface=http-interface:list-add(name=allowed-origins,value=<url>)
reload --host=master
``` 

The URL depends on how you launch the console. You can choose between one of the following options:

## Undertow

The package `hal-standalone.jar` starts an Undertow server at http://localhost:9090. You can specify a different port as command line parameter:

1. Add http://localhost:9090 as allowed origin
1. `java -jar hal-standalone-<version>.jar [port]` 
1. Open http://localhost:9090
  
## NPM 

The npm package [`hal-console`](https://www.npmjs.com/package/hal-console) launches a local web server at http://localhost:3000. You can specify a different port using the PORT environment variable:

1. Add http://localhost:3000 as allowed origin
1. `npm install hal-console`
1. `[PORT=dddd] node hal-console`

## GitHub Pages

The latest console version is also available at GitHub:

1. Add https://hal.github.io/console/ as allowed origin
1. Open https://hal.github.io/console/

GitHub pages are served from https so you need to secure the management interface as well. Please note that if you're using a self signed key store you might need to open the local management endpoint in the browser and accept the unsafe certificate once, before you can use it with HAL.

## Docker

Finally you can use the docker image [`halconsole/hal`](https://hub.docker.com/r/halconsole/hal/) which wraps an Undertow server inside a docker container. Follow the steps at https://hub.docker.com/r/halconsole/hal/ to get started. 
