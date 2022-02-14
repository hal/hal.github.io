---
title: "JavaScript API"
date: 2018-03-16T05:39:16+01:00
description: "Gives background information about the JavaScript API. Shows sample code and describes what you can do with the API."
draft: true
icon: "/img/code.png"
toc: true
weight: 50
---
The goal of the JavaScript API is to open HAL for JavaScript developers. The API can be used to write extensions which add features to the console. Another use case is to write SPAs which interact with the management interface. A monitoring tool which shows the statistics of all data-source pools across the domain could be an example of such an application.

The JavaScript API is divided into several modules which define a common namespace for classes that belong together. 

- `hal.config` - contains classes which hold information about the console and its environment.
- `hal.core` - provides access to the core classes of HAL.
- `hal.dmr` - deals with executing operations and the DMR API.
- `hal.meta` - provides access to the metadata of resources.
- `hal.mvp` - provides access to the model view presenter framework which is used in HAL (*NYI*).
- `hal.ui` - contains classes to build the user interface.

See https://cdn.rawgit.com/hal/console/esdoc/index.html for all details. 

# Examples

## CRUD Operations

This example shows the CRUD operations in action. First a IO worker is created using an add-resource dialog, then the newly created resource is read. Finally the IO worker is modified and removed again. 

```js
let core = hal.core.Core.getInstance();
let address = "{selected.profile}/subsystem=io/worker=*";
let template = hal.meta.AddressTemplate.of(address);
let attributes = ["io-threads", "stack-size", "task-keepalive", "task-max-threads"];

core.crud.addDialog("Worker", address, attributes, (name, address) => {
   
    console.log("Worker '" + name + "' successfully added as '" + address.toString() + "'"); 
    let workerAddress = template.resolve(core.statementContext, name);
    core.crud.read(workerAddress, worker => {
        
        console.log(worker.toString());
        core.crud.save("Worker", name, template, {"stack-size": 42}, () => {
            
            console.log("Worker '" + name + "' successfully modified"); 
            core.crud.remove("Worker", name, workerAddress, () => {
                
                console.log("Worker '" + name + "' successfully removed"); 
            });
        });
    });
});
```

## Read Child Resources

This example uses the crud operations to read the children of a resource. In this case the subsystems of the selected profile are read. 

```js
let core = hal.core.Core.getInstance();

core.crud.readChildren("{selected.profile}", "subsystem", children => {
    children.forEach(child => console.log(child.name))
});
```

## Execute DMR Operations

This example shows how to execute arbitrary operations. 

```js
let core = hal.core.Core.getInstance();
let op = core.operation("/", "read-resource")
    .param("attributes-only", true)
    .param("include-runtime", true)
    .build();

core.dispatcher.execute(op, result => console.log(result.toString()));
```

## UI Elements

### Table

Creates a table to manage paths defined at `/path=*`.

```js
let name = "Path";
let core = hal.core.Core.getInstance();
let template = hal.meta.AddressTemplate.of("/path=*");

core.metadataProcessor.lookup(template, metadata => {
    let table = core.table(metadata)
        .add(name, template, ["name", "path", "relative-to"], refresh)
        .remove(name, template, t => t.selectedRow.get("name").asString(), refresh)
        .columns(["name", "path"])
        .build();

    $("#hal-root-container")
        .empty()
        .append("<h1>" + name + "</h1>")
        .append("<p>" + metadata.description.description + "</p>")
        .append(table.element);
    table.attach();
    refresh();

    function refresh() {
        core.crud.readChildren("/", "path", properties => {
            table.update(properties.map(property => property.value));
        });
    }
});
```

### Form

Creates a form to view and edit the transaction manager defined at `/subsystem=transactions`.

```js
let name = "Transaction Manager";
let core = hal.core.Core.getInstance();
let template = hal.meta.AddressTemplate.of("{selected.profile}/subsystem=transactions");

core.metadataProcessor.lookup(template, metadata => {
    let form = core.form(metadata)
        .onSave((f, changedValues) => {
            core.crud.saveSingleton(name, template, changedValues, refresh);
        })
        .build();
    
    $("#hal-root-container")
        .empty()
        .append("<h1>" + name + "</h1>")
        .append("<p>" + metadata.description.description + "</p>")
        .append(form.element);
    form.attach();
    refresh();
    
    function refresh() {
        core.crud.read(template, config => form.view(config));
    }
});
```

### Master Detail

Creates a master-detail view to manage log categories defined at `/subsystem=logging/logger=*`.

```js
let type = "Category";
let core = hal.core.Core.getInstance();
let template = hal.meta.AddressTemplate.of("{selected.profile}/subsystem=logging/logger=*");

core.metadataProcessor.lookup(template, metadata => {
    let table = core.namedTable(metadata)
        .add(type, template, ["level", "handlers", "use-parent-handlers"], refresh)
        .remove(type, template, t => t.selectedRow.name, refresh)
        .columns(["category", "level"])
        .build();
    
    let form = core.namedForm(metadata)
        .onSave((f, changedValues) => {
            core.crud.save(type, f.model.name, template, changedValues, refresh);
        })
        .build();
    
    $("#hal-root-container")
        .empty()
        .append("<h1>Categories</h1>")
        .append("<p>" + metadata.description.description + "</p>")
        .append(table.element)
        .append(form.element);
    
    table.attach();
    form.attach();
    table.bindForm(form);
    refresh();
    
    function refresh() {
        core.crud.readChildren(template.parent, "logger", properties => {
            form.clear();
            table.update(hal.dmr.ModelNodeHelper.asNamedNodes(properties));
        });
    }
});
```
