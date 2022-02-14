---
title: "Extensions"
date: 2018-03-08T16:30:20+01:00
description: "Shows how you can write custom runtime extensions using the JavaScript API. Includes how to setup your environment and how to add the extensions to the console."
draft: true
icon: "/img/plug.png"
toc: true
weight: 40
---
Extensions are a way to add features to the management console. They are written in JavaScript and should use the [JavaScript API]({{< relref "/development/javascript-api" >}}) to build the UI and interact with the management interface.  
 
# Architecture

JavaScript extensions consist of one script and one or more optional stylesheets. The script contains both the code to register the extensions and the actual code of the extension. Here's the code of a sample extension:

```js
let core = hal.core.Core.getInstance();
let whoami = hal.core.Extension.header("whoami", "Who am I?", () => {
    let operation = core.operation(hal.dmr.ResourceAddress.root(), "whoami")
        .param("verbose", true)
        .build();
    core.dispatcher.execute(operation, result => {
        let username = hal.dmr.ModelNodeHelper.failSafeGet(result, "identity/username").asString();
        let realm = hal.dmr.ModelNodeHelper.failSafeGet(result, "identity/realm").asString();
        let roles = hal.dmr.ModelNodeHelper.failSafeList(result, "mapped-roles")
            .map(role => role.asString())
            .join(", ");
        alert(`You are user ${username} at ${realm}. Mapped roles: [${roles}].`);
    });
});
core.extensionRegistry.register(whoami);
```

The extension's script and stylesheets must be served by endpoints which are known to HAL. When HAL starts, it injects the scripts and stylesheets of all known extensions which in turn will make them available in HAL.

# Extension Points

The console provides four different extension points which can be used by extensions to add their features:
 
1. Header: Adds a menu item to the "Extensions" dropdown in the header
1. Finder Item: Adds a new item to a specific finder column
1. Footer: Adds a menu item to the "Extensions" dropdown in the footer
1. Custom: It's up to the extension how to add itself to the console

# Development 

Extensions must adhere to certain rules:

- the code must be in one file
- the stylesheets can be spread across multiple files
- the extension must register itself using HAL's JavaScript API (there's no lookup / detection mechanism in the console).

Please make sure that your extension doesn't use the global scope. Use idioms like [IIFE](https://en.wikipedia.org/wiki/Immediately-invoked_function_expression) to define a custom scope for your extension.

To get started quickly you can use the NPM package hal-edk (extension development kit). Follow these steps to setup a new extension project:

1. Create a new node project: `npm init`
1. Install a developer dependency for hal-edk: `npm add hal-edk --dev`  
   This will install the management console in `node_modules/hal-edk` and create three files in the project's root folder:
   
    1. index.html: Starts the console and loads extension.js
    1. extension.js: Registers a noop header extension
    1. extension.json: Contains metadata for the extension
    
1. Start a local web server and add the URL as allowed origin to the management model.
1. Open index.html

# Deployment

Extensions can be deployed using two different ways:

## Bundled Extensions

Bundled extensions are part of the WildFly installation and installed as WildFly modules. They have to be installed outside of the console. WildFly and the console have to be restarted / reloaded after adding or removing bundled extensions.

## Standalone Extensions

Standalone extensions are hosted by a public available endpoint. This endpoint must serve the extensions metadata. You can add and remove standalone extensions using the management console. They're stored in the browser's local storage. As such they're scoped to the browser and URL which runs the management console.

See https://github.com/hal/js-extensions for sample standalone extensions.
