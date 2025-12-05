Strapi allows the development of plugins that work exactly like the built-in plugins or 3rd-party plugins available from the Marketplace.  
To start developing a Strapi plugin:  
1. Create a plugin using the CLI-based generator.  
2. Learn more about the structure of a plugin.  
3. Get an overview of the plugin APIs to add features to your plugin.  
4. Read some guides based on your use case.

# Plugin APIs
Strapi provides following APIs:  
Admin Panel API: Use the Admin Panel API to have your plugin interact with the admin panel of Strapi.  
Server API: Use the Server API to have your plugin interact with the backend server of Strapi.  

# Plugin creation and setup
1. create the plugin,  
2. enable the plugin,  
3. install dependencies, build the admin panel, and start the server.

```
yarn strapi generate plugin
```
```
module.exports = {
    'reviewmail': {
        enabled: true,
        resolve: './src/plugins/reviewmail'
    }
}
```
```
cd src/plugins/reviewmail
yarn
```
## servers
Strapi itself is headless. The admin panel is completely separate from the server.
```
yarn develop --watch-admin
```
will run the server on localhost:1337 and enable hot reloading on both the back-end and front-end servers.  

# Plugin structure
When creating a plugin with the CLI generator, Strapi generates the following boilerplate structure for you in the ./src/plugins/reviewmail folder.  
/admin; /server; package.json; strapi-admin.js; strapi-server.js  
/admin for Admin Panel API, /server for Server API  

# Admin Panel API for plugins
The admin panel is a React application that can embed other React applications. 
## Entry file
\[plugin-name\]/admin/src/index.js  
This file exports the required interface, with the following functions available:  
1. Lifecycle funtions: register, bootstrap  
2. Async function: registerTrads  

register():  
1. register itself so it's available to the admin panel  
2. add a new link to the main navigation(Menu API)  
3. create a new settings section  
4. define injectiion zones  
5. add reducers  

registerPlugin():  
Registers the plugin to make it available in the admin panel.  

bootstrap():  
Exposes the bootstrap function, executed after all the plugins are registered.  

registerTrads():
is used to register a plugin's translations files and to create separate chunks for the application translations. It does not need to be modified. 
## Available actions
