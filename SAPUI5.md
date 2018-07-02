## Walkthrough

### Step 1: Start

You should create a new folder `webapp`(or any other name) which will contain all sources of the app we will create.

Create a new root HTML file called `index.html` in your app folder.



### Step 2: Bootstrap

If SAPUI5 is deployed somewhere else on the server, you need to adjust the corresponding paths in the bootstrap (here: `src="/resources/sap-ui-core.js"`) in this tutorial according to your own requirements. 

SAPUI5 can also be retrieved from the Content Delivery Network (CDN) at <https://sapui5.hana.ondemand.com/resources/sap-ui-core.js> . 

In an actual app, you always have to specify an SAPUI5 version explicitly.

**webapp/index.html**

```HTML
<!DOCTYPE html>
<html>
   <head>
	<meta http-equiv="X-UA-Compatible" content="IE=edge">
	<meta charset="utf-8">
 	<title>Walkthrough</title>
      <script
         id="sap-ui-bootstrap"
         src="/resources/sap-ui-core.js" 
         data-sap-ui-theme="sap_belize"
         data-sap-ui-libs="sap.m"
         data-sap-ui-compatVersion="edge"
         data-sap-ui-preload="async" >
      </script>
      <script>
         sap.ui.getCore().attachInit(function () {
             alert("UI5 is ready");
         });
      </script>
   </head>
   <body>
      <p>Hello World</p>
   </body>
</html>
```

The `src` attribute of the first `<script>` tag tells the browser where to find the SAPUI5 core library – it initializes the SAPUI5 runtime and loads additional resources, such as the libraries specified in the `data-sap-ui-libs` attribute. 

The `sap-ui-core.js` file contains a copy of jQuery, this means that you can use all jQuery features. 



### Step 3: Controls

Controls are used to define appearance and behavior of parts of the screen. Now it is time to use SAPUI5 control `sap.m.Text` to replace the "Hello World" text in the HTML body.

**webapp/index.html**

```HTML
<!DOCTYPE html>
<html>
   <head>
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta charset="utf-8">
      <title>Walkthrough</title>
      <script
         id="sap-ui-bootstrap"
         src="/resources/sap-ui-core.js"
         data-sap-ui-theme="sap_belize"
         data-sap-ui-libs="sap.m"
         data-sap-ui-compatVersion="edge"
         data-sap-ui-preload="async" >
      </script>
      <script>
         sap.ui.getCore().attachInit(function () {
            new sap.m.Text({
               text : "Hello World"
            }).placeAt("content");
         });
      </script>
   </head>
   <body class="sapUiBody" id="content">
   </body>
</html>
```

**Note**

Only instances of `sap.ui.core.Control` or their subclasses can be rendered stand-alone and have a `placeAt` function. Each control extends `sap.ui.core.Element` that can only be rendered inside controls. 



### Step 4: XML Views

To avoid a messy setup due to putting all UIs in the `index.html`, we will put the `sap.m.Text` control into a dedicated view. 

**webapp/view/App.view.xml (New)**

```XML
<mvc:View
   xmlns="sap.m"
   xmlns:mvc="sap.ui.core.mvc">
</mvc:View>
```

We create a new `view` folder in our app and a new file for our XML view inside the app folder. The root node of the XML structure is the `view`. Here, we reference the default namespace `sap.m` where the majority of our UI assets are located. We define an additional `sap.ui.core.mvc` namespace with alias `mvc`, where the SAPUI5 views and all other Model-View-Controller (MVC) assets are located. 

**webapp/view/App.view.xml**

```XML
<mvc:View
   xmlns="sap.m"
   xmlns:mvc="sap.ui.core.mvc">
   <Text text="Hello World"/>
</mvc:View>
```

Inside the `view` tag, we add the declarative definition of our `text` control with the same properties as in the previous step. The XML tags are mapped to controls and the attributes are mapped to the properties of the control. 

In SAPUI5, each control has its own ID. In the XML view above we did not specify an ID attribute, and therefore the SAPUI5 runtime generates an own ID and adds it to the control. However, it is a good practice to set the IDs of controls explicitly, so that controls can be identified easily. 

 **webapp/index.html**

```HTML
<!DOCTYPE html>
<html>
   <head>
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta charset="utf-8">
      <title>Walkthrough</title>
      <script
         id="sap-ui-bootstrap"
         src="/resources/sap-ui-core.js"
         data-sap-ui-theme="sap_belize"
         data-sap-ui-libs="sap.m"
         data-sap-ui-compatVersion="edge"
         data-sap-ui-preload="async"
         data-sap-ui-resourceroots='{
            "walkthrough": "./"
         }' >
      </script>
      <script>
         sap.ui.getCore().attachInit(function () {
            sap.ui.xmlview({
               viewName : "walkthrough.view.App"
            }).placeAt("content");
         });
      </script>
   </head>
   <body class="sapUiBody" id="content">
   </body>
</html>
```

We tell SAPUI5 core that resources in the `walkthrough` namespace are located in the same folder as `index.html`. This is, for example, necessary for apps that run in the SAP Fiori launchpad. 

We replace the instantiation of the `sap.m.Text` control by our new App XML view. The view is created by a factory function of SAPUI5 which makes sure that the view is correctly configured and can be extended by customers. The name is prefixed with the namespace `walkthrough.view` in order to uniquely identify this resource. 

**Conventions**

- View names are capitalized
- All views are stored in the `view` folder
- Names of XML views always end with `*.view.xml`
- The default XML namespace is `sap.m`
- Other XML namespaces use the last part of the SAP namespace as alias (for example, `mvc` for `sap.ui.core.mvc`)



### Step 5: Controllers

In this step, we replace the text with a button and show the “Hello World” message when the button is pressed. The handling of the button's press event is implemented in the controller of the view. 

**webapp/view/App.view.xml**

```XML
<mvc:View
   controllerName="walkthrough.controller.App"
   xmlns="sap.m"
   xmlns:mvc="sap.ui.core.mvc">
   <Button
      text="Say Hello"
      press="onShowHello"/>
</mvc:View>
```

We add a reference to the controller, and replace the text control with a button with text “Say Hello”. The button triggers the `onShowHello` event handler function when being pressed. We have to specify the name of the controller that is connected to the view and holds the `onShowHello` function by setting the `controllerName` attribute of the view.

A view does not necessarily need an explicitly assigned controller. You do not have to create a controller if the view is just displaying information and no additional functionality is required. If a controller is specified, it is instantiated after the view is loaded.

**webapp/comtroller/App.controller.js (New)**

```javascript
sap.ui.define([
   "sap/ui/core/mvc/Controller"
], function (Controller) {
   "use strict";
   return Controller.extend("", {
   });
});
```

We create the folder `webapp/controller` and a new file `App.controller.js` inside.

The "use strict" which was introduced by ECMAScript 5 tells the browser to execute the code in a so called "strict mode". The strict mode helps to detect potential coding issues at an early state at development time.

**webapp/controller/App.controller.js**

```javascript
sap.ui.define([
   "sap/ui/core/mvc/Controller"
], function (Controller) {
   "use strict";
   return Controller.extend("walkthrough.controller.App", {
      onShowHello : function () {
         // show a native JavaScript alert
         alert("Hello World");
      }
   });
});
```

We define the app controller in its own file by extending the `Controller` object of the SAPUI5 core. In the beginning it holds only a single function called `onShowHello` that handles the button's `press` event by showing an alert.

**Conventions**

- Controller names are capitalized
- Controllers carry the same name as the related view (if there is a 1:1 relationship)
- Event handlers are prefixed with on
- Controller names always end with `*.controller.js`



### Step 6: Modules

In SAPUI5 , resources are often referred to as modules. In this step, we replace the alert from the last exercise with a proper Message Toast from the sap.m library. The required modules are enabled to be loaded asynchronously. 

**webapp/controller/App.controller.js**

```javascript
sap.ui.define([
   "sap/ui/core/mvc/Controller",
   "sap/m/MessageToast"
], function (Controller, MessageToast) {
   "use strict";
   return Controller.extend("walkthrough.controller.App", {
      onShowHello : function () {
         MessageToast.show("Hello World");
      }
   });
});
```

We extend the array of required modules with the fully qualified path to `sap.m.MessageToast`. Once both modules, `Controller` and `MessageToast`, are loaded, the callback function is called and we can make use of both objects by accessing the parameters passed on to the function.

This Asynchronous Module Definition (AMD) syntax allows to clearly separate the module loading from the code execution and greatly improves the performance of the application. The browser can decide when and how the resources are loaded prior to code execution. 

**Conventions**

- Use `sap.ui.define` for controllers and all other JavaScript modules to define a global namespace. With the namespace, the object can be addressed throughout the application.
- Use `sap.ui.require` for asynchronously loading dependencies but without declaring a namespace, for example code that just needs to be executed, but does not need to be called from other code.
- Use the name of the artifact to load for naming the function parameters (without namespace).



### Step 7: JSON Model



## Model View Controller (MVC)

The Model View Controller (MVC) concept is used in SAPUI5 to separate the representation of information from the user interaction. This separation facilitates development and the changing of parts independently.

 Model, view, and controller are assigned the following roles: 

- The **view** is responsible for defining and rendering the UI.
- The **model** manages the application data.

- The **controller** reacts to view events and user interaction by modifying the view and model.

The purpose of data binding in the UI is to separate the definition of the user interface (view), the data visualized by the application (model), and the code for the business logic for processing the data (controller). The separation has the following advantages: It provides better readability, maintainability, and extensibility and it allows you to change the view without touching the underlying business logic and to define several views of the same data. 

Views and controllers often form a 1:1 relationship, but it is also possible to have controllers without a UI, these controllers are called application controllers. It is also possible to create views without controllers. From a technical position, a view is a SAPUI5 control and can have or inherit a SAPUI5 model.

View and controller represent reusable units, and distributed development is highly supported.

### Models

A model in the Model View Controller concept holds the data and provides methods to retrieve the data from the database and to set and update data.

SAPUI5 provides the following predefined models:

- **OData model**: Enables binding of controls to data from OData services. The OData model supports two-way (default), one-way and one-time binding modes. However, two-way binding is currently only supported for properties, and not for aggregations. 
- **JSON model**: Can be used to bind controls to JavaScript object data, which is usually serialized in the JSON format. The JSON model is a client-side model and, therefore, intended for small data sets, which are completely available on the client. The JSON model supports two-way (default), one-way and one-time binding modes. 
- **XML model**: A client-side model intended for small data sets, which are completely available on the client. The XML model does not contain mechanisms for server-based paging or loading of deltas. The XML model supports two-way (default), one-way and one-time binding modes. 
- **Resource model**: Designed to handle data in resource bundles, mainly to provide texts in different languages. The resource model only supports one-time binding mode because it deals with static texts only. 

The JSON model, XML model, and the resource model are **client-side models**, meaning that the model data is loaded completely and is available on the client. Operations such as sorting and filtering are executed on the client without further server requests.

The OData (V2 or V4) model is a **server-side** model and only loads the data requested by the user interface from the server.

You can not only define one model for your applications, but define different areas in your application with different models and assign single controls to a model. You can also define nested models, for example, a JSON model defined for the application and an OData model for a table control contained in the application.

A Web application should support several data sources, such as JSON, XML, Atom, or OData. However, the way in which data binding is defined and implemented within the UI controls should be independent of the respective data source. It is also possible to create a custom model implementation for data sources that are not yet covered by the framework or are domain-specific.

### Views

The view in the Model View Controller concept is responsible for defining and rendering the UI. SAPUI5 supports predefined view types.

XML views are recommended here because XML views force a clear separation of the UI definition from the application logic (which has to be implemented in the controller). This makes the code is better readable and easier to support.

#### XML View

The XML view type is defined in an XML file. The file name either ends with `.view.xml` or as an XML string. The file name and the folder structure together specify the name of the view that equals the SAPUI5 module name.

**Example**

For `resources/sap/hcm/Address.view.xml`, the view name is `sap.hcm.Address`. The application uses this view name for displaying an instance of this view. If you define the XML view by means of an XML string, no file or define/require is needed.

The file looks as follows:

```xml
<mvc:View controllerName="sap.hcm.Address" xmlns="sap.m" xmlns:mvc="sap.ui.core.mvc">
   <Panel>
      <Image src="http://www.sap.com/global/ui/images/global/sap-logo.png"/>
      <Button text="Press Me!"/>
   </Panel>
</mvc:View>
```

 #### Namespaces in XML Views

The names of the SAPUI5 control libraries and the related subpackages are mapped to XML namespaces.

One of the required namespaces can be defined as the default namespace. The control tags for this namespace do not need a prefix.

The `<View>` tag is required and in the example below, the mvc namespace is defined in the first line. You can define any alias for the namespace.

A control can be located in a subpackage of a control library, for example `sap.ui.layout.form.Form` is located in the `sap.ui.layout` library, but the full package name is `sap.ui.layout.form`. You have to specify this subpackage as a separate XML namespace, even if `sap.ui.layout` is already defined as namespace.

```XML
<mvc:View
   controllerName="..."
   xmlns="sap.ui.layout"
   xmlns="sap.ui.layout.form"
   xmlns:mvc="sap.ui.core.mvc">
</mvc:View>
```

#### Aggregation Handling in XML Views

In XML views, aggregated child controls can be added as child tags. 

**Aggregations of XML Views**

On root level, you can only define content for the default aggregation, e.g. without adding the `content` tag. If you want to specify content for another aggregation of a view like `dependents`, place it in a child control´s `dependents` aggregation or add it by using the `addDependent` method. 

**Aggregations of Controls Inside the View**

Some controls have more than one content area, for example the shell control that has the main content area, a menu bar, a headerItems aggregation, a worksetItemsaggregation, and so on. An aggregation tag usually serves as a direct child of a container and contains children. You can only add children directly if the container control has marked one of the child aggregations as default. 

You fill aggregations as shown in the following example. The namespace of the parent control tag and the aggregation tag must be the same. 

```XML
<mvc:View controllerName="sap.hcm.Address" xmlns="sap.m" xmlns:mvc="sap.ui.core.mvc">
   <Panel>
      <content>  <!-- this is the general way of adding children: use the aggregation name -->
         <Image src="http://www.sap.com/global/ui/images/global/sap-logo.png"/>
         <Button text="Press Me"/>
      </content>
   </Panel>
</mvc:View>
```

