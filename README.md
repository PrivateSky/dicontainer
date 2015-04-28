# container node.js module
DI container inspired by angular.js with one major twist: a service can become "out of service"  and working fine again and all other services depending on it will get notified.

# Why?
 We found that existing modules for DI doesn't handle the errors. Externale services can go down or the network can go down and error recovery code should automaticaly go up as fast as possible. Without a solid foundation on how objects get wired it is quite dififcult to prevent loosing important data when such things happen.

 Also, we extened a bit idea of DI to go beyound object instantiation on  knowning precisely when a specific feature is ready to be used in other zones of the application  depending on it.
 

# Install
npm install container 

//now in your code you can get an instance of the container
var container = require("container").container;

#API

## service
    /* name: name of teh service
      arr: array with  dependencies (names) for this service
      callback: function called when all dependencies are ready
    */
    container.service(name, arr, callback)

## declareDependency
    //identical with  service but to signal that a callback can instantiate local/closure things that are not services
    container.declareDependency(name, arr, callback)

## resolve
    //directly assign a value to the service. Tt can't be null. It will try to initialise other services depedning on name
    container.resolve(name,value)

## outOfService
    //  Declare that a service or feature is not working properly. All services depending on this will get notified
    container.outOfService(name,value)

The callback given to service or declareDependecy functions will behave like in angular except that the first parameter will be always a boolean (outOfService flag) that will signal that the callback is called for invalidating the current service or for proper initialisation

# Example

    
    container.service('node', ['node_base'], function(outOfService, leaf){
        if(!outOfService){
            return {type:"node", leaf:leaf}
        } else {
            assert.true(leaf == null);
        }
    })
    
    var fakeRoot = {fakeRoot:true};
    var root = fakeRoot;
    container.service('root', ['node'], function(outOfService, node) {
        if (!outOfService) {
            assert.true(node1 != null);
            root = {type: "root", node: node};
            return root; //value used as value
        } else {
            root = fakeRoot;
        }
    });

    //initialisation will be triggered by calling container.service('node_base', [''],..) or    container.resolve('node_base')


#more examples

