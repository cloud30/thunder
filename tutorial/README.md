## Getting Started

_Thunder_ is an iOS [app](http://itunes.apple.com) that allows to create pixel-perfect mockups of iOS applications using simple HTML-like markup. All you need is a text editor and a web server.


## Hello World

Native user interfaces are built by assembling together native views and controllers. In _Thunder_ you do this using tags and attributes just like in HTML, except that element names correspond to native classes and attributes to native properties. Most iOS [UIKit](https://developer.apple.com/library/ios/documentation/uikit/reference/UIKit_Framework/_index.html) classes and properties are available and the official documentation can be used as a reference. In addition, we provide our own extensions where appplicable.

Without further ado, let's start with a quick hello world. Just create a file named _hello.html_ with the contents below, place it under your favorite web server, and enter its URL in _Thunder_.

```xhtml
<?thunder xhtml ?>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Hello World!</title>
    </head>
	<body>
		<composite name="hello">
			<UILabel text="Hello World" backgroundColor="#D6F5D6"/>
		</composite>
	</body>
</html>
```
[try it!](thunder:https://raw.git.com/cloud30/thunder/master/tutorial/hello.html)

A green screen with a hello world label should be displayed.

Looking at the source file, the first `<?thunder xhtml ?>` directive tells Thunder that this html file contains our special markup (in xhtml format) and should be rendered as a native screen rather than an HTML page. `<composite>` is our root node. The first child will be the root view and it will be automatically sized to fill the screen. In this first example we use a [UILabel](https://developer.apple.com/library/ios/documentation/uikit/reference/UILabel_Class/Reference/UILabel.html) view and set its `text` and `backgroundColor` properties.

Congratulations you got your first mockup running. It's all downhill from here! :)


## Properties
As you can see from Apple's documentation, every property is of a specific type, however in markup everything is a string so it needs to be converted accordingly. For basic types like strings, integers and floats, this happens as you'd expect. For other types, where appplicable, we use Apple's string representation and convert it automatically. Please refer to our [Property Types Reference]() for the correct syntax of each type.
Let's refine the label above with additional properties. Create a file named _helloFancy.html_ as follows:

```xhtml
<?thunder xhtml ?>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Hello Fancy!</title>
    </head>
	<body>
		<composite name="helloFancy">
			<UIView backgroundColor="orange">
				<UILabel frame="{{25,100},{200,50}}" text="Hello World" font="{PartyLetPlain, 24}" textColor="LimeGreen" backgroundColor="#D6F5D6" shadowColor="rgba(0,30,0,0.5)" shadowOffset="{0,1}" textAlignment="center" adjustsFontSizeToFitWidth="YES"/>
			</UIView>
		</composite>
	</body>
</html>
```
[try it!](thunder:https://raw.git.com/cloud30/thunder/master/tutorial/helloFancy.html)

In order to provide the most accurate presentation of your application mockup, _Thunder_ displays it in full screen without adding any additional control or toolbar. In order to return to the initial screen so that you can reload the file or load a new one, you can either shake the device or send it temporarily in the background (e.g. double-click the home button and select _Thunder_ again).

## Styles
Just like with HTML, when styling an application a lot of properties need to have the same values. In these cases it helps to externalize these properties in a separate stylesheet. Similarly to CSS classes, _Thunder_ lets you specify style classes in each element and externalize their values in a separate JSON file.
For this example, create a JSON file called _fancyLabels.json_:

```json
{
  "stdLabel": {
    "font": "{PartyLetPlain, 16}",
    "textColor": "LimeGreen",
    "backgroundColor": "#D6F5D6",
    "shadowColor": "rgba(0,30,0,0.5)",
    "shadowOffset": "{0,1}",
    "adjustsFontSizeToFitWidth": "YES"
  },
  "bigFont": {
    "font": "{PartyLetPlain, 24}"
  }
}
```

and a corresponding _helloStyles.html_:

```xhtml
<?thunder xhtml ?>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Hello Styles!</title>
    </head>
	<body>
		<composite name="helloStyles" style="fancyLabels.json">
			<UIView backgroundColor="yellow">
				<UILabel styleClass="stdLabel bigFont" frame="{{25,100},{200,50}}" text="Hello World"/>
				<UILabel styleClass="stdLabel" frame="{{25,150},{200,50}}" text="...it's a styled world!" textAlignment="right"/>
			</UIView>
		</composite>
	</body>
</html>
```
[try it!](thunder:https://raw.git.com/cloud30/thunder/master/tutorial/helloStyles.html)

As you can see, the `style` attribute of the `composite` tag specifies the json file to be used for styling. Any element can use the `styleClass` attribute to specify one or more space-separated class names to be applied in order.


## Layout
Frames are easy to understand and are very fast. However their absolute positioning makes them cumbersome to work with when there are changes to the UI or when we are designing for flexible screen sizes (such as 3.5 vs 4 inch iPhone and iPad). iOS 6 introduced layout constraints, which provide a very powerful and flexible solution (even thought it's not without its own idiosyncrasies). Constraints allow to define position and size of a component in relationship to others.
In the following example we create a container view (which, being the root view, fills up the entire screen) and then size and position its children relative to it. This will cause components to adapt based on device type and orientation.

```xhtml
<?thunder xhtml ?>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Hello Layouts!</title>
    </head>
	<body>
		<composite name="helloLayouts">
			<UIView backgroundColor="lightblue">
				<UIView id="brow" centerX="_parent" width="75%" top="100" height="10" backgroundColor="red"/> 
				<UILabel id="helloLabel" left="brow + 10" top="brow.bottom + 10" text="Hello World" backgroundColor="lightblue"/>
				<UIView left="helloLabel.right + 30" right="brow" top="helloLabel" bottom="helloLabel" backgroundColor="green"/>
				<UIView centerX="brow" width="50" top="helloLabel.bottom" height="brow.width * 0.5" backgroundColor="yellow"/> 
			</UIView>
		</composite>
	</body>
</html>
```
[try it!](thunder:https://raw.git.com/cloud30/thunder/master/tutorial/helloLayouts.html)

When using layouts, 2 constraints per axis are normally needed so that size and position are not ambiguous. Some components (like labels and images) provide an intrinsic size. In that case, a single position constraint per axis is sufficient. This is especially useful for labels, which may vary in size when localized. For a list of constraint attributes and full syntax check out the [Layout Reference]().


## Tables
Tables are a fundamental design element as they provide scrollable lists used to display data. In this first example we will create a table using static data.


```xhtml
<?thunder xhtml ?>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Hello Table!</title>
    </head>
	<body>
		<composite name="helloTable">
	            <UIView backgroundColor="white" automaticallyAdjustsScrollViewInsets="NO">
	            	<UITableView top="_topGuide.bottom" bottom="_bottomGuide.top" left="0" width="_parent" style="Plain" showSection="NO">
	                <UITableViewCell style="Default">
	                	<UILabel.textLabel text="Hello Default Cell"/>
	                </UITableViewCell>
	                <UITableViewCell style="Value1">
	                	<UILabel.textLabel text="Hello Value1 Cell"/>
	                	<UILabel.detailTextLabel text="detail"/>
	                </UITableViewCell>
	                <UITableViewCell style="Value2">
	                	<UILabel.textLabel text="Hello Value2"/>
	                	<UILabel.detailTextLabel text="detail"/>
	                </UITableViewCell>
	                <UITableViewCell style="Subtitle">
	                	<UILabel.textLabel text="Hello Subtitle Cell"/>
	                	<UILabel.detailTextLabel text="detail"/>
	                </UITableViewCell>
	                <UITableViewCell accessoryType="DisclosureIndicator">
	                	<UILabel.textLabel text="Hello DisclosureIndicator"/>
	                </UITableViewCell>
	                <UITableViewCell accessoryType="DetailDisclosureButton">
	                	<UILabel.textLabel text="Hello DetailDisclosureButton"/>
	                </UITableViewCell>
	                <UITableViewCell accessoryType="Checkmark">
	                	<UILabel.textLabel text="Hello Checkmark"/>
	                </UITableViewCell>
	                <UITableViewCell accessoryType="DetailButton">
	                	<UILabel.textLabel text="Hello DetailButton"/>
	                </UITableViewCell>
	            </UITableView>
			</UIView>
		</composite>
	</body>
</html>
```
[try it!](thunder:https://raw.git.com/cloud30/thunder/master/tutorial/helloTable.html)

This example is pretty trivial, but one thing to notice is the `<UILabel.textLabel>` tag. As we have seen earlier, normally properties can be set using HTML attributes using simple strings. However, when properties are complex, it would be impractical trying to reduce them to a single string. So in those cases we use a child element whose tag name includes the property type and property name separated by a dot. For example, since `UITableViewCell` has a `textLabel` property of type `UILabel` we can use the tag `<UILabel.textLabel>` to define its properties like any other `UILabel`.


## Data-driven Tables
Static tables are useful but sometimes we want to use them to render a long list of data we have. For example, let's say we have a file called `countries.json` with the following content:

```json
{
  "countries": [
    {
      "name": "Afghanistan",
      "code": "AF"
    },
    {
      "name": "Åland Islands",
      "code": "AX"
    },
    {
      "name": "Albania",
      "code": "AL"
    }
  ]
}
```

In this case we may want to create a table with a template for each cell and then bind the array to the table and each field to a label in each table cell. Thunder allows you to do exactly that:

```xhtml
<?thunder xhtml ?>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Country Table</title>
    </head>
	<body>
		<composite name="countryTable" cfg="countries.json">
			<UIView backgroundColor="white" automaticallyAdjustsScrollViewInsets="NO">
            	<UITableView top="_topGuide.bottom" bottom="_bottomGuide.top" left="0" width="_parent" style="Plain" showSection="NO" model-bind="cfg:countries">
		            <UITableViewCell.defaultCellTmpl style="Value2">
	                	<UILabel.textLabel text-bind="code" textAlignment="center"/>
	                	<UILabel.detailTextLabel text-bind="name" adjustsFontSizeToFitWidth="YES"/>
		            </UITableViewCell.defaultCellTmpl>
	            </UITableView>
			</UIView>
		</composite>
	</body>
</html>
```
[try it!](thunder:https://raw.git.com/cloud30/thunder/master/tutorial/countryTable.html)

Similarly to the `style` property we saw before, the `cfg` property in the composite tag refers to a json file in the same location as the html file. Instead of providing each table cell individually as children, we use the `model` property and bind it to the countries array. Each element of the array is then bound to the template speficied by the `defaultCellTmpl` property. In this case, the data of country is bound to the cell's labels.
This is just scratching the surface of the power of table views. For more advanced features check out our [TableView Reference]().


## Events and Actions
Every component has the ability to fire _events_ to notify us of meaningful activities that have happened. They may also expose _actions_ that perform some predefined behavior. In _Thunder_ we can easily listen to events and trigger actions in response to them. In the following example we'll create 2 buttons that will change the text of a label and the background color of the entire screen upon pressing them.

```xhtml
<?thunder xhtml ?>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Hello Events</title>
    </head>
	<body>
		<composite name="helloEvents">
            <UIView id="rootView" backgroundColor="ivory">
                <UIButton centerY="100" height="50" centerX="_parent" width="200" title="Choose Red" titleColor="orange" tintColor="red" msg="Painful Truth">
                    <_onTouchUpInside do="rootView.setProperty(backgroundColor,red)"/>
                    <_onTouchUpInside do="message.setProperty(text,$sender.msg)"/>
                </UIButton>

                <UIButton centerY="200" height="50" centerX="_parent" width="200" title="Choose Blue" titleColor="lightblue" tintColor="blue" msg="Blissful Ignorance">
                    <_onTouchUpInside do="rootView.setProperty(backgroundColor,blue)"/>
                    <_onTouchUpInside do="message.setProperty(text,$sender.msg)"/>
                </UIButton>

                <UILabel id="message" bottom="_bottomGuide.top - 100" height="50" centerX="_parent" width="75%" font="{PartyLetPlain, 35}" textColor="white" backgroundColor="clear" textAlignment="center"/>
            </UIView>
		</composite>
	</body>
</html>
```
[try it!](thunder:https://raw.git.com/cloud30/thunder/master/tutorial/helloEvents.html)

If only one action needs to be triggered, the event handler can be specified as an attribute as in `_onTouchUpInside="rootView.setProperty(backgroundColor,blue)"`. If multiple actions are needed, the tag format above has to be used. Actions will be performed in the order specified.
Every event normally carries a payload that can be referenced with the `$` prefix. The `$sender` event payload is usually provided and it represents the element where the event has happened (in this case the `UIButton` itself). Just like in HTML we can leverage custom properties to carry useful additional payload and in this case with the `msg` custom property.


## Hello John
Sometimes event handlers need to do more than just invoking actions. Since _Thunder_ files are plain HTML files, JavaScript is available and can be used to implement more complex actions. In the example below we get the input from a text field, make it uppercase, and set the text of a label from JS.

```xhtml
<?thunder xhtml ?>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Hello John</title>
        <script type="text/javascript">//<![CDATA[
            function _onLoad(url) {
                $().init({
                    helloJohn: {
                        updateName: function (name) {
                            $('nameTextField').doAction('resignFirstResponder');
                            $('message').setProperty('text', 'Hello ' + name.toUpperCase() + '!');
                        }
                    }
                });
            };
        //]]></script>
    </head>
	<body>
		<composite name="helloJohn">
            <UIView backgroundColor="peru">
                <UITextField id="nameTextField" centerY="100" height="50" centerX="_parent" width="200" backgroundColor="rgba(255,255,255,0.5)" borderStyle="RoundedRect" textAlignment="center" contentVerticalAlignment="center" placeholder="What's your name?" _onShouldReturn="-updateName($sender.text)"/>

                <UILabel id="message" top="nameTextField + 100" height="50" centerX="_parent" width="90%" font="{PartyLetPlain, 35}" textColor="white" backgroundColor="clear" textAlignment="center"/>
            </UIView>
		</composite>
	</body>
</html>
```
[try it!](thunder:https://raw.git.com/cloud30/thunder/master/tutorial/helloJohn.html)

As you can see, if the action names starts with `-` then a JS function is invoked instead of and action.


## Navigation
Until now our entire application had only one page but in reality we probably want to have multiple and be able to navigate across them. The Navigation controller allows to manage multiple screens by stacking them and providing a familiar title bar and back button automatically. Each transition from one screen to another is managed by a component called _segue_ (e.g. `BUXNavigationSegue`) that specifies title, styles and other properties.

```xhtml
<?thunder xhtml ?>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Hello Navigation</title>
    </head>
	<body>
		<composite name="helloTutorial">
			<UINavigationController toolbarHidden="YES" navigationBarHidden="NO">
                <BUXNavigationSegue>
                    <UINavigationItem.navigationItem title="Hello Navigation">
	                    <UIBarButtonItem.backBarButtonItem title="Home"/>
	                </UINavigationItem.navigationItem>
                    <UIView>
		                <UIButton centerY="200" height="50" centerX="_parent" width="200" title="Choose Red" titleColor="orange" tintColor="red" _onTouchUpInside="red.present()"/>
		                <UIButton centerY="300" height="50" centerX="_parent" width="200" title="Choose Blue" titleColor="lightblue" tintColor="blue" _onTouchUpInside="blue.present()"/>
		            </UIView>
                </BUXNavigationSegue>

                <BUXNavigationSegue id="red">
                    <UINavigationItem.navigationItem title="Painful Truth"/>
                    <UIView backgroundColor="red"/>
                </BUXNavigationSegue>

                <BUXNavigationSegue id="blue">
                    <UINavigationItem.navigationItem title="Blissful Ignorance"/>
                    <UIView backgroundColor="blue"/>
                </BUXNavigationSegue>

			</UINavigationController>
		</composite>
	</body>
</html>
```
[try it!](thunder:https://raw.git.com/cloud30/thunder/master/tutorial/helloTutorial.html)

A `UINavigationController` should have one or more `BUXNavigationSegue` children, the first of which will be the root of the stack and be displayed automatically. Any other segue can be presented invoking the present() or presentWithModel() actions. See [Event Reference]() and [Action Reference]() for more information about events and actions.

## Putting it all together
Using the concepts covered so far, we can combine all these sample files together in a single app.

```xhtml
<?thunder xhtml ?>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Thunder Tutorial</title>
        <script type="text/javascript">//<![CDATA[
            function _onLoad(url) {
                $().init({
                    helloJohn: {
                        updateName: function (name) {
                            $('nameTextField').doAction('resignFirstResponder');
                            $('message').setProperty('text', 'Hello ' + name.toUpperCase() + '!');
                        }
                    }
                });
                $().imports(['hello', 'helloFancy', 'helloStyles', 'helloLayouts', 'helloTable', 'countryTable', 'helloEvents', 'helloJohn']);
            };
        //]]></script>
    </head>
	<body>
		<composite name="helloTutorial">
			<UINavigationController toolbarHidden="YES" navigationBarHidden="NO">
                <BUXNavigationSegue>
                    <UINavigationItem.navigationItem title="Hello Tutorial">
	                    <UIBarButtonItem.backBarButtonItem title="Home"/>
	                </UINavigationItem.navigationItem>
                    <UITableView style="Plain" showSection="NO">
		                <UITableViewCell.defaultCellTmpl accessoryType="DisclosureIndicator" detailTitle-bind="detailTitle" compName-bind="compName" _onDidSelect="tutorial.presentWithModel($sender)">
		                	<UILabel.textLabel text-bind="detailTitle"/>
		                </UITableViewCell.defaultCellTmpl>
		                <json.model>
		                	[
		                		{"detailTitle":"Hello World!", "compName":"hello"},
		                		{"detailTitle":"Hello Fancy!", "compName":"helloFancy"},
		                		{"detailTitle":"Hello Styles!", "compName":"helloStyles"},
		                		{"detailTitle":"Hello Layouts!", "compName":"helloLayouts"},
		                		{"detailTitle":"Hello Table!", "compName":"helloTable"},
		                		{"detailTitle":"Country Table", "compName":"countryTable"},
		                		{"detailTitle":"Hello Events", "compName":"helloEvents"},
		                		{"detailTitle":"Hello John", "compName":"helloJohn"}
		                	]
		                </json.model>
		            </UITableView>
                </BUXNavigationSegue>

                <BUXNavigationSegue id="tutorial" reuse="NO">
                    <UINavigationItem.navigationItem title-bind="detailTitle"/>
                    <UIView composite-bind="compName" backgroundColor="white"/>
                </BUXNavigationSegue>
			</UINavigationController>
		</composite>
	</body>
</html>
```
[try it!](thunder:https://raw.git.com/cloud30/thunder/master/tutorial/helloTutorial.html)

Every component has a special property called `composite` that allows to load its contents from another file or URL. This allows to decompose and test experiences in smaller chunks that are easier to work with and reuse.

This tutorial covered a lot of ground that normally takes a long time to master. Don't worry if you haven't fully grasped everything. Just start from the sample files, check the documentation, and hack away! :)
