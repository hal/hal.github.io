---
title: "Architecture"
date: 2018-03-08T16:30:01+01:00
description: "Describes the basic architecture of the console. Lists the used frameworks and libraries and gives background information about the choices made."
icon: "/img/building.png"
toc: true
weight: 20
---
HAL is a client side RIA without server side dependencies. It is a GWT application - which means it's written almost completely in Java. GWT is used to transpile the Java code into a bunch of JavaScript, HTML and CSS files. HAL uses some external JavaScript libraries as well. These dependencies are managed using [NPM](https://npmjs.org/) which is in turn integrated into the Maven build using the [`maven-frontend-plugin`](https://github.com/eirslett/frontend-maven-plugin). 

We use the [model view presenter](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93presenter) pattern and [GWTP](https://dev.arcbees.com/gwtp/) for its implementation. The main business logic resides in presenters like the [`DataSourcePresenter`](https://github.com/hal/console/blob/develop/app/src/main/java/org/jboss/hal/client/configuration/subsystem/datasource/DataSourcePresenter.java). Presenters are responsible for holding state and talk to the management endpoint. The views are used to hold all UI related code. They leverage the [PatternFly](https://www.patternfly.org/) components and are implemented using [Elemento](https://github.com/hal/elemento). Views register event handlers for user interaction and interact with the presenters. The model in this scenario are usually instances of [`ModelNode`](https://github.com/hal/console/blob/develop/dmr/src/main/java/org/jboss/hal/dmr/ModelNode.java) which are the result of DMR operations. They're passed around between the presenters and views.

For more details about the different parts of the console and how they work together see [building blocks]({{< relref "/development/building-blocks.md" >}}).

# Technical Stack

In a nutshell the console uses the following technical stack:

- Java 11
- [GWT](http://www.gwtproject.org/) 
- [GWTP](https://dev.arcbees.com/gwtp/)
- [Elemento](https://github.com/hal/elemento)
- [RxGWT](https://github.com/intendia-oss/rxgwt)
- [PouchDB](https://pouchdb.com/)
- [PatternFly](https://www.patternfly.org/)

# Maven Modules

HAL consists of many maven modules, each of which serves a specific purpose. Here's the list of the most important modules (a-z) and a quick description:

| Module                  | Description                                                                                                                      |
|-------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| hal-ballroom            | Core UI classes like `Form`, `Dialog` or `Table`                                                                                 |
| hal-bom                 | Bill of materials (declaration of all HAL modules)                                                                               |
| hal-build-config        | Build configuration like checkstyle rules, code formatting and licenses.                                                         |
| hal-code-parent         | Parent POM for all modules with code                                                                                             | 
| hal-config              | Configuration classes for the environment, operation mode, current user and its roles                                            |
| hal-console             | Main application containing the GWT [entry point](http://www.gwtproject.org/doc/latest/DevGuideCodingBasicsClient.html#creating) |
| hal-core                | Core HAL API                                                                                                                     |
| hal-db                  | Thin wrapper around [PouchDB](https://pouchdb.com/)                                                                              |
| hal-dmr                 | DMR related code to execute operation, read results and work with model nodes                                                    |
| hal-flow                | Execute asynchronous tasks in order                                                                                              |
| hal-gwt-parent          | Parent POM for all GWT related modules                                                                                           |
| hal-js                  | JavaScript related helper classes                                                                                                |
| hal-meta                | Metadata related classes to encapsulate the different parts of the resource descriptions                                         |
| hal-parent              | Parent POM for all other modules                                                                                                 |
| hal-processors          | Annotation processors for code generation                                                                                        |
| hal-resources           | I18n resources, images and HTML snippets                                                                                         |
| hal-spi                 | SPI related classes and annotations                                                                                              |
| hal-standalone          | Quarkus based app to start the console in [standalone mode]({{< relref "/documentation/get-started.md#standalone-mode" >}})      |
| hal-testsuite-resources | Maven setup to assemble classes from different modules and make them available as one dependency for the test suite              |
| hal-theme-parent        | Different HAL themes                                                                                                             |
| hal-theme-eap           | Theme used for [JBoss EAP](https://developers.redhat.com/products/eap/overview/)                                                 |
| hal-theme-hal           | Theme used for the [standalone mode]({{< relref "/documentation/get-started.md#standalone-mode" >}})                             |
| hal-theme-wildfly       | Theme used for [WildFly](https://wildfly.org/)                                                                                   |

    
## Dependencies

Here's the dependency graph of the main maven modules and the main external dependencies as generated by the [`degraph-maven-plugin`](https://github.com/ferstl/depgraph-maven-plugin): 

{{< screenshot src="/img/development/dependency-graph.png" >}} 
