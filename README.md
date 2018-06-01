# Oracle JET Training Materials

#### Day 1: Basics

   * Getting Started
   * Working with Oracle JET Components
   * Working with Oracle JET Composite Components
   
#### Day 2: Data
   
   * Visualizing Data from a File
   * Visualizing Data from a REST Endpoint
   * Selecting a Row in the Table
   * Adding CRUD Functionality
   * Adding Responsive Design
   
#### Day 3: Miscellaneous

   * Internationalization
   
## Day 1   

### Getting Started

1. Follow the instruction on the Getting Started page to install the ojet-cli:

http://www.oracle.com/webfolder/technetwork/jet/globalGetStarted.html

2. Run the following in the terminal:

```js #button { border: none; }
ojet create BankAnalyzer --template=navdrawer
```
3. Run the following in the terminal and look in the browser:

```js #button { border: none; }
ojet serve
```

4. Open your editor and eplore the source structure and learn about what everything does.

5. Make a change in the 'Dashboard.html' file and notice what happens in the browser, without needing to refresh.

### Working with Oracle JET Components

1. Explore the Oracle JET Cookbook.

2. Especially explore the data visualization use cases:

http://www.oracle.com/webfolder/technetwork/jet/jetCookbook.html?component=dataVisualizations&demo=gallery

3. Take a look at the Bar Chart:

http://www.oracle.com/webfolder/technetwork/jet/jetCookbook.html?component=barChart&demo=default

4. Look at the JS documentation, the description, variations, and tweak the code.

5. Copy the HTML into the Dashboard View, the JavaScript into the Dashboard ViewModel, and reference the 'ojs/ojchart' in the define block of the Dashboard ViewModel. Look in the browser and see the Bar Chart is displayed.

6. Change the 'bar' type to 'pie' by hand and then let the user do it by combining with a Combobox One:

http://www.oracle.com/webfolder/technetwork/jet/jetCookbook.html?component=comboboxOne&demo=single

In this scenario, also discuss the square braces vs. curly braces.

### Working with Oracle JET Composite Components

Let's now take the chart and the combobox and turn them into a reusable CCA component.

#### Getting Started

1. In the Terminal, in the root of your project, run the following:

```js #button { border: none; }
ojet create component my-chart
```

2. Take a look at your source structure and find the new 'my-chart' CCA component.

3. Load the loader into the ViewModel, declare the new custom element in the View, then look in the browser and notice the message from the CCA component.

4. Move the chart and the combobox into the 'my-chart' CCA component.

5. Declare the 'my-chart' custom element a few more times and notice that you now see multiple charts.

```html #button { border: none; }
<my-chart></my-chart>
<my-chart></my-chart>
```

#### Setting Properties

1. In 'component.json', add a 'chartType' property:

```js #button { border: none; }
"properties": {
    "chartType": {
        "type": "string"
    }
},
```

2. In 'dashboard.html', use the property in your 'my-chart' custom elements:

```html #button { border: none; }
<my-chart chart-type="bar"></my-chart>
<my-chart chart-type="pie"></my-chart>
```

3. In 'my-chart-viewmodel.js', create a new Knockout property that is initialized by the custom element's context, i.e., by 'bar' and by 'pie' in the two examples above:

```js #button { border: none; }
self.chartType = ko.observable(context.properties.chartType);
```

4. In 'my-chart-view.html', change the value of the combobox to '{{chartType}}' and the type of the chart to '[[chartType]]'.

5. After the above works correctly, replace some of the other properties in the CCA component so that they can be set from the Dashboard module.

#### Using Arrays in Properties

1. In 'component.json', define a new property, this time an 'array', to work with the 'series' property of the chart:

```js #button { border: none; }
"properties": {
    "chartType": {
        "type": "string"
    },
    "chartData": {
        "type": "array"
    }
},
```

2. In 'dashboard.html', use the property, by referencing a display-only variable to be defined in the 'dashboard.js':

```html #button { border: none; }
<my-chart
    chart-data="[[carsales]]"
    chart-type="bar"></my-chart>
```

3. In 'dashboard.js', define the variable referenced above:

```js #button { border: none; }
self.carsales = [
    {name: "Honda", items: [12, 24]},
    {name: "Volvo", items: [95, 39]},
    {name: "Mazda", items: [16, 52]},
    {name: "Mercedes", items: [32, 26]},
    {name: "Jaguar", items: [82, 36]}];
```

4. In 'my-chart-viewModel.js', set a property that is defined by the 'my-chart' custom element:

```js #button { border: none; }
self.chartData = context.properties.chartData;
```

5. Reference the property above in the 'my-chart-view.html' file, in the 'series' property of the chart:

```html #button { border: none; }
series="[[chartData]]"
```

In the browser, you should see that your data can now be defined per 'my-chart' custom element, rather than being hardcoded in the CCA component.

## Day 2

Today, we will focus on displaying and manipulating data.

### Flex Layouts

1. Read the below:

https://docs.oracle.com/en/middleware/jet/5/develop/oracle-jet-flex-layouts.html#GUID-0F39F088-0814-42D2-9DC4-9EDF7656A120

2. Use the classes oj-flex, oj-flex-item, oj-panel, oj-panel-alt1, and oj-panel-margin in your Views.

### Visualizing Data from a File

In the first scenario, we move our hardcoded data from the ViewModels into a JSON file. From there, we load the data into our ViewModel, parse it, and then push it into a variable referenced in our view.

1. Move the data into a file named 'carsales.json', in a new folder named 'data', 'src/js'. Here's what its content should look like:

```js #button { border: none; }
[
    {"name":"Honda", "items": [42, 34]},
    {"name":"Volvo", "items": [55, 30]},
    {"name":"Fiat", "items": [36, 50]},
    {"name":"Mercedes", "items": [22, 46]},
    {"name":"Jaguar", "items": [10, 46]}
]
```

2. Load the file in the 'define' block:

```js #button { border: none; }
'text!../data/carsales.json'
```

3. Reference the file in the callback function in the same position as the file in the 'define' block:

```js #button { border: none; }
carSalesFile
```

4. Now parse the file and push into the 'carsales' variable:

```js #button { border: none; }
self.carsales = [];
var carSalesContent = JSON.parse(carSalesFile);
for (var i = 0; i < carSalesContent.length; i++) {
    var carSale = carSalesContent[i];
    self.carsales.push({
        name: carSale["name"],
        items: carSale["items"]
    });
}
```

5. Reference the 'carsales' variable as display-only in the 'chart-data' attribute of the 'my-chart' CCA component:

```html #button { border: none; }
<my-chart
    chart-data="[[carsales]]"
    chart-type="bar"></my-chart>
```

In the browser, you should now see that the data is being loaded from a file. 

Do the above for all the data in all your ViewModels, i.e., separate the data from your logic.

### Visualizing Data from a REST Endpoint

We'll use these REST endpoints, one for large resolution and the other for small devices, e.g., mobile phones, which have less real estate so we should make a smaller subset of data available:

https://apex.oracle.com/pls/apex/oraclejet/emp/

https://apex.oracle.com/pls/apex/oraclejet/m/emp/

Instead of a chart, we will now use a table.

1. Explore this section in the Cookbook:

http://www.oracle.com/webfolder/technetwork/jet/jetCookbook.html?component=crud&demo=table

2. Read the JS documentation for ojTable:

http://www.oracle.com/webfolder/technetwork/jet/jsdocs/oj.ojTable.html

2. In a ViewModel, e.g., 'employees.js', model your data using BackBone syntax:

```js #button { border: none; }
self.serviceURL = 'https://apex.oracle.com/pls/apex/oraclejet/emp/';
self.EmpCol = ko.observable();
self.datasource = ko.observable();

self.Employee = oj.Model.extend({
    urlRoot: self.serviceURL,
    idAttribute: 'empno'
});

self.myProfile = new self.Employee();
self.EmpCollection = oj.Collection.extend({
    url: self.serviceURL,
    model: self.myProfile,
    comparator: 'ename'
});

self.EmpCol(new self.EmpCollection());

self.EmpCol().fetch();

self.datasource(new oj.CollectionTableDataSource(self.EmpCol()));
```

3. Reference the components you need in the 'define' block of the ViewModel:

```js #button { border: none; }
'ojs/ojtable', 'ojs/ojcollectiontabledatasource'
```

4. Copy the table into a View:

```html #button { border: none; }
 <oj-table id="table" data="[[datasource]]"
          columns='[
          {"headerText": "Name",
          "field": "ename", "sortable": "enabled"},
          {"headerText": "Job",
          "field": "job", "sortable": "enabled"}]'
          selectionMode='{"row": "none", "column": "none"}'>
</oj-table>
```
Now you should see the data from the REST endpoint in your table.

### Selecting a Row in the Table

1. Read about ojTable events:

http://www.oracle.com/webfolder/technetwork/jet/jetCookbook.html?component=table&demo=eventTable

2. Read the JS documentation for ojTable:

http://www.oracle.com/webfolder/technetwork/jet/jsdocs/oj.ojTable.html

Especially look at:

http://www.oracle.com/webfolder/technetwork/jet/jsdocs/oj.ojTable.html#event:beforeCurrentRow

3. In the ojTable in the View:

```html #button { border: none; }
on-oj-before-current-row='[[currentRowListener]]'
```

4. In the ViewModel:

```js #button { border: none; }
self.selectedName = ko.observable("[nothing selected]");

self.currentRowListener = function (event, data) {
    var newCurrentRow = event.detail.currentRow;
    console.log(newCurrentRow);
    self.datasource().at(newCurrentRow['rowIndex']).
            then(function (rowObj) {
                var obj = rowObj['data'];
                self.selectedName(obj.ename);
            });
};
```

5. In the View, to display the selected name:

```js #button { border: none; }
Selected: <oj-bind-text value="[[selectedName]]"></oj-bind-text>
```
### Adding CRUD Functionality

Explore this section in the Cookbook:

http://www.oracle.com/webfolder/technetwork/jet/jetCookbook.html?component=crud&demo=table

### Adding Responsive Design

We'll create another table, different to the one above, this time to display different data depending on the resolution:

1. In the folder where 'main.js' is found, create 'responsiveUtils.js', to contain helpful media queries provided by Oracle JET:

```js #button { border: none; }
define(['ojs/ojcore'],
    function (oj) {
        function ResponsiveController() {
            var self = this;
            // Media queries for repsonsive layouts
            var smQuery = oj.ResponsiveUtils.getFrameworkQuery(oj.ResponsiveUtils.FRAMEWORK_QUERY_KEY.SM_ONLY);
            self.smScreen = oj.ResponsiveKnockoutUtils.createMediaQueryObservable(smQuery);
            var mdQuery = oj.ResponsiveUtils.getFrameworkQuery(oj.ResponsiveUtils.FRAMEWORK_QUERY_KEY.MD_UP);
            self.mdScreen = oj.ResponsiveKnockoutUtils.createMediaQueryObservable(mdQuery);
        }
        return new ResponsiveController();
    });
```   

2. In a View, create a table:

```html #button { border: none; }
<oj-table 
    id='table' 
    aria-label='Employees Table'
    data='[[dataprovider]]' 
    columns='[[dynamicCols]]'
    style='width: 100%; height:100%;'>
</oj-table>
```

Notice that 'data' and 'columns' are going to be defined in the ViewModel.

3. In the ViewModel, return values for the above based on the resolution:

```js #button { border: none; }
self.dataprovider = ko.observable();
self.dynamicCols = ko.observableArray([]);

function getUrl() {
    if (ru.mdScreen()) {
        url = "https://apex.oracle.com/pls/apex/oraclejet/emp/";
    } else {
        url = "https://apex.oracle.com/pls/apex/oraclejet/m/emp/";
    }
    return url;
}

self.getCols = function () {
    var url = getUrl();
    $.getJSON(url).then(function (data) {
        var tempCols = [];
        for (var property in data.items[0]) {
            if (data.items[0].hasOwnProperty(property)) {
                tempCols.push({headerText: property, field: property});
            }
        }
        self.dynamicCols(tempCols);
        self.dataprovider(new oj.ArrayDataProvider(data.items, {idAttribute: 'empno'}));
    });
};

self.getCols();
```

4. Load the applicable components via the ViewModel 'define' block, and put 'ru' at the end of the callback function:

```js #button { border: none; }
'responsiveUtils',
'ojs/ojtable',
'ojs/ojcollectiontabledatasource',
'ojs/ojarraydataprovider'
```

Resize the browser and notice that different columns are displayed depending on the resolution.

## Day 3 Miscellaneous

In this part, we're going to add functionality to let the user switch from one language to another, in this case, from English to Arabic and back.

### Adding a Language Switcher with RTL Support

1. In 'index.html', find the responsive toolbar, oj-toolbar, and add new items to it for switching languages. Above the 'Preferences' oj-option, add the following:

```html #button { border: none; }
<oj-option id="languages">
    <span>Languages</span>
    <oj-menu id="languages_menu">
        <oj-option on-oj-action="[[setLangAction]]" id="english" value="english">
            <span class="oj-fwk-icon oj-fwk-icon-arrow-n" slot="startIcon"></span>English
        </oj-option>
        <oj-option on-oj-action="[[setLangAction]]" id="arabic" value="ar-EG">
            <span class="demo-icon-font demo-bookmark-icon-16" slot="startIcon"></span>Arabic
        </oj-option>
    </oj-menu>
</oj-option>
```

2. In 'appController.js', right below 'var self-this', add the 'setLangAction' method that is referred to above:

```js #button { border: none; }
self.setLangAction = function (event) {
    var newLang = event.target.value;
    oj.Config.setLocale(newLang,
        function () {
            $('html').attr('lang', newLang);
            if (newLang === 'ar-EG') {
                $('html').attr('dir', 'rtl');
            } else {
                $('html').attr('dir', 'ltr');
            }
            document.dispatchEvent(new CustomEvent('localeListener'));
        });
};
```
Notice that we're dispatching an event, which can be finetuned further, so the modules can listen to it and react appropriately.

4. In the application, find the new 'Arabic' menu item, select it, and notice that the user interface switches from right to left.

### Adding Translation Bundles

1. In 'src/js', add a new folder named 'resources'. 

2. In the 'resources' folder, create a folder named 'nls', containing 'l10.js', which is a file with this content:

```js #button { border: none; }
define({
  "root": {
    "dashboardLabel": "Dashboard",
},
  "ar": true,
});
```

3. In the 'nls' folder, create a folder named 'ar', containing 'l10.js', which is a file with this content:

```js #button { border: none; }
define({
  "dashboardLabel":"عدادات",
});
```

4. In 'main.js', under the 'shim' section, add the following so that the translation bundles are found:

```js #button { border: none; }
config: {
    ojL10n: {
      merge: {
        'ojtranslations/nls/ojtranslations': 'resources/nls/l10'
      }
    }
  }
```

### Internationalizing the Module Tabs

Let's now internationalize the 'Dashboard', 'Incidents', 'Customers', and 'About' items in the tabs of the application, which are defined in the 'appController.js' file.

1. In 'appController.js', add the following, under 'var self=this', to initialize the 'self.dashboardLabel':

```js #button { border: none; }
self.dashboardLabel = ko.observable(oj.Translations.getTranslatedString('dashboardLabel'));
```

2. In 'appController.js', in the 'setLangAction' method, below the if/else statement, refresh the 'self.dashboardLabel':

```js #button { border: none; }
self.dashboardLabel(oj.Translations.getTranslatedString('dashboardLabel'));
```

3. In 'appController.js', in the Navigation setup, in 'var navData', change 'name: 'Dashboard'' to 'name: self.dashboardLabel', so that the first item in the 'navData' array now looks like this:

```js #button { border: none; }
{name: self.dashboardLabel, id: 'dashboard',
     iconClass: 'oj-navigationlist-item-icon demo-icon-font-24 demo-chart-icon-24'},
```

4. When you switch to 'Arabic' in the application, notice that the Dashboard label is now Arabic and it is English when you switch to 'English'.

### Internationalizing the Module Texts

1. Add a new string to your translation bundles, named 'dashboardHeader'.

2. In 'dashboard.js', define a new variable for the dashboard header label, to initialize the variable with the current state of the translation bundle:

```js #button { border: none; }
self.dashboardHeaderLabel = ko.observable(oj.Translations.getTranslatedString('dashboardHeader'));
```

3. In 'dashboard.js', listen for the 'localelistener' event and update the dashboard header label with the current state of the translation bundle:

```js #button { border: none; }
document.addEventListener("localeListener", function () {
     self.dashboardHeaderLabel(oj.Translations.getTranslatedString('dashboardHeader'));
});
```

4. In 'dashboard.html', replace the H1 with the following:

```html #button { border: none; }
<h1><oj-bind-text value="[[dashboardHeaderLabel]]"></oj-bind-text></h1>
```

5. In the application, switch to Arabic and notice the H1 is now Arabic.

6. Do the same for other texts, in the other modules in the application.

### Experiment with the Event Based Programming

1. Replace the EventListener in 'appController.js', to include a 'params' variable with a 'detail' object for the second argument, as shown here:

```js #button { border: none; }
var params = {
    'bubbles': true,
    'detail': {'message': self.dashboardLabel()}
};
document.dispatchEvent(new CustomEvent('localeListener', params));
```

2. Next, rewrite how how the listener is used, with the callback having an event payload that contains the 'detail' object that you can pull your values from.

```js #button { border: none; }
document.addEventListener("localeListener", function (event) {
    console.log('EventValue: ' + event.detail.message);
}); 
```

This is a very powerful way of using event based programming.

More details relating to this: https://javascript.info/bubbling-and-capturing

### Internationalizing the Oracle JET Components

1. In 'dashboard.html', add the following:

```html #button { border: none; }
<oj-input-date id="date"></oj-input-date>
```

2. In 'dashboard.js', include the following in the 'define' block:

```js #button { border: none; }
'ojs/ojdatetimepicker'
```

3. In the application, switch from English to Arabic, and notice the calendar automatically displays Arabic months.

4. Experiment with other components in the Oracle JET Cookbook and see what they look like in Arabic:

http://www.oracle.com/webfolder/technetwork/jet/jetCookbook.html

