# AAM.Js 

## Overview

The plugin is a new way to integrate the AAM HTML5 Responsive Designer tool. Following modern web best practises, the AAM HTML5 Responsive Designer can now be “plugged into” a customer’s website. The designer is embedded within a customer’s web page, allowing for more seamless and secure integration

The plugin does not require an iFrame to host the AAM HTML5 Responsive Designer which allows for better branding control around the designer tool by the customer and also mitigates possible security concerns in the future.

It is now truly stateless and does not require a session to be able to hold information about the specific HTML5 skin to be loaded which also helps if setups require a proxy between the AAM HTML5 Responsive Designer and AAM Servers.

The Framework being used around the AAM HTML5 Plugin allows for a fast single page designer application using a single concatenated and minified JavaScript file that can be easily embedded within any of a customer’s web pages.

#### Features
-	Single page application (no page loads)
-	Fully Stateless
-	iFrame independent
-	Seamless integration
-	Better performance overall
-	Flexible and highly customizable
 
## Demo

A demo of the AAM HTML5 Plugin designer can be found using the following link which simulates a live integration:

Responsive Designer in Demo Responsive Page: https://us.personalcard.net/Hosting/HTML5PlugginDemo.aspx

You can refer to this link if you wish to see a working version of a detailed implementation by viewing the source of the page.

## Plugin Implementation Options

The following information details the different options that can be used to implement the AAM HTML5 plugin.

### Required 
###### AAM HTML5 Responsive Designer placeholder

AAM HTML5 Responsive Designer placeholder needs to be placed within the webpage between HTML Body tags. This will define where the designer will load on the page:

```
<div id="designer1" class="aam-designer-init"></div>
```

###### Product Handover Key

A product must be setup with access to the HTML5 features enabled which will create a unique GUID that needs to be added as the value for “data-handoverkey” within the following JavaScript options.

###### JavaScript Source

As part of the project implementation, a different “src” URL will be supplied for UAT and Production to the specific HTML5 Skin that has been agreed upon. This URL will point to a specific JavaScript file which will hold information about the current functionality and look and feel of the designer. 


 

## Option 1: Simplest Implementation

**Warning: Still currently in development and Option 2 should be used as the preferred option until further notice.**

The following line of JavaScript can be placed anywhere within a webpage but it is normally best practice to place this at the end of the document:

```
<script type="text/javascript" data-name="aam-designer" data-bootstrap="true" data-handoverkey="<HANDOVER_KEY>" src="<URL>/embed.min.js"></script>
```

#### “data-bootstap”

If this setting is set to “true”, the AAM HTML5 Responsive Designer will launch on pageLoad automatically.

If this setting is set to “false”, the designer will not load automatically and a controlled launch of the AAM HTML5 Responsive Designer would be needed using the “designer.start()” function to explicitly load the designer. This is detailed below in the next implementation option.

#### “data-handoverkey”

This Guid relates to a pre-configured Product which has been setup within the AAM Administration website. This is used to identify specific Product setup including Design Template, Gallery Selection, DataCapture information, etc.

#### “src”

Please email to support.AWS@gemalto.com for *src* URL. 

## Option 2: Simple Implementation

The following line of JavaScript can be placed anywhere within a webpage but it is normally best practice to place this at the end of the document:

```
<script type="text/javascript" data-name="aam-designer" data-bootstrap="false" data-handoverkey="<HANDOVER_KEY>" src="<URL>/embed.min.js"></script>

<script type="text/javascript">
	(function() {
			var designer = new AAM({
			el: document.getElementById('designer1')
		});

		designer.start()
	})();
</script>
```

This implementation option allows for a controlled launch of the AAM HTML5 Responsive Designer using the “designer.start()” function.

The AAM Object that is created, by default holds all the relevant information about the designer to be loaded and is updated throughout the end users designer session. It will hold key information about the customers experience like the unique CardImageId, manipulation and placement of the image, etc.

## Option 3: Detailed Implementation

The following line of JavaScript can be placed anywhere within a webpage but it is normally best practice to place this at the end of the document:

```
<script type="text/javascript" data-name="aam-designer" data-bootstrap="false" data-handoverkey="<HANDOVER_KEY>" src="<URL>/embed.min.js"></script>
<script type="text/javascript">
	(function() {
		var designerElement = document.getElementById('designer1');

		var designer = new AAM({
			el: designerElement,

			callbacks: {
				onLoad: function(){
					designerElement.style.display = 'block';
				},
				onSubmit: function(response){
					designerElement.style.display = 'none';
				},
				onError: function(response){
					console.log(response);
				}
			},
			options: {
				startHidden: false,
				emailAddress: 'test@test.com',
				languageId: 'en-GB',
				passthrough: {},
				handback: {
					enabled: true,
					url: 'http://www.google.com'
				},
colours: {
                			colour1:"#339acc",
                			colour2:"#c1e3f3",
                			colour3:"#339acc" 				}
			}
		});

	designer.start()
	// designer.hide();
	// designer.show();
	// designer.close();

	})();
</script>
```

##### Callbacks
•	*onload*: executes when the plugin has loaded (when API is available)

•	*onSubmit*: executes when the user has submitted the design (handback data is returned here).

Information about the design session can be access after the onSubmit call back is fired using the following:

-	*“response.CardImageId”* will return a unique AAM CardImageId associated with the submitted design.
-	*“response.Handover_Key”* will return the product HandoverKey.
-	*“response.ImageType”* will return the type of image that an end user submitted during the design process. These values can be “Gallery” or “Custom”.
-	*“response.LanguageId”* will return the language code that was used when displaying the designer which can be the default value setup in the product or a language code passed into the designer. Example codes “en-US”, “fr-FR”, etc.
-	*“response.emailaddress”* will return the email address of the end user that was captured

Custom DataCapture fields that can be setup within the AAM Product Configuration page can also be accessed using the Name of the DataCapture field setup. 

For example, if you created a DataCapture field called “customerName”, “address” and “PhoneNo”, you will be able to use the following Keys to retrieve the submitted values:

-	*“response.customerName”*
-	*“response.address”*
- *“response.mobileNo”*

•	*onError*: executes when an error has occurred (error info is returned here).

#### Options

Please Note: Any options that are used during the launch of the AAM HTML5 Responsive Designer will override the default options already setup at Product level.

•	*startHidden*: Allows for the AAM HTML5 Responsive Designer to be loaded by hidden from the end user

•	*emailAddress*: This allow for the email address to be preset before loading the designer and are saved with the design submission and Handback to the defined Handback page.

•	*lauguageId*: Allows for the AAM HTML5 Responsive Designer to be loaded using a different language other than the value setup by default in the AAM Product configuration page.

•	*Passthrough*: optional parameters can be passed into the designer session to be passed back to the Handback page after design submission.  These key:value pairs would also be stored as DataCapture values if this have been setup within Program Management. Example:

```
passthrough: {customerName:'John', mobileNo:'0123456789'},
```

•	*Handback*: Allows the use of Dynamic Handback URLs where by different end users can be redirected to different handback pages. Commonly used to handback to multilingual pages. This is used with conjunction with the AAM product URL whitelist that ensures only validate handback URL defined are allowed to be used.
o	enabled: True or False – setting defaults to False
o	url: Full URL set to redirect the page after the a design submission

#### Colours*

The AAM HTML5 Responsive Designer has been created to not only be responsive but customisable in terms of colours to help change the look and feel of the designer to match the site in which the plugin will be embedded. Changing the values of the colours will affect the following aspect of the Responsive Skin using Hex colour codes:

colour1: breadcrumb active stage, svg backgrounds, text, borders
colour2: svg inactive, breadcrumb stage
colour3: call to action button, backgrounds(svg and/or html)

Omitting the colour options results in the designer using the default colours: colour 1 & 3 = #339acc (mid blue), colour 2 = #c1e3f3; (pale blue)
* Please note that colours apply to only support skins – please inquire for more details*

## Controlling the Plugin-designer dimensions
The following meta-tags should be added to the <head> tag to optimize the responsive behaviour on mobile devices:- 

```
<meta name="viewport" content="width=device-width, initial-scale=1">
<meta name="apple-mobile-web-app-capable" content="yes">
```
The plugin-designer is set up to fit its container, that is, the element containing the plugin as shown in red below.

```
<div id=”yourContainer”>
<script type="text/javascript" data-name="aam-designer" data-bootstrap="false" data-handoverkey="<HANDOVER_KEY>" 
src="<URL>/embed.min.js">
</script>
</div>
```

When the container size is changed, the plugin-designer will try to fit the resulting dimensions (within limits). 

The designer behaves differently depending on the mode (embedded or full-screen) in which it is displayed. On a desktop browser, the default is embedded mode but may be switched to full-screen mode by selecting the full-screen icon top right. 

-	Embedded - The designer is sized and positioned to fill its immediate container, but has minimum dimensions - currently 320 pixels width and a fixed height of 450 pixels.  

This enables the host page to have full control of its content without disturbance from the designer. The designer’s container should be controlled as for any other content panel on the page.

-	Full screen – The designer is sized to maximum and positioned relative to the browser window.

This masks any other host page content. The designer resizes in a fluid manner to fill the browser window but has minimum dimensions - currently 320 x 450 pixels.

On mobile devices, the layout of the designer elements may change depending on the device’s orientation and aspect ratio.


For the best results, 

-	The designer’s container should have minimum dimensions, ideally 320 x 450 pixels. This may be assigned directly, or assigned indirectly by assigning absolute minimum dimensions to one of its ancestors and setting the container’s intermediate ancestor dimensions to 100% x 100%.

-	Ideally your host page should differentiate between mobile and desktop views. This enables the page to show a reduced amount or reformatted content on mobile devices. This can be achieved on a basic level by adding the following code to your page, which assigns the appropriate class to the <body> depending on the result…

```
function _isTouchDevice()
{
try{  document.createEvent("TouchEvent"); return true; }
       catch(e){ return false; }
}

var isTouch =  _isTouchDevice();
var bodyTag = document.getElementsByTagName("BODY")[0];

if(isTouch){
bodyTag.setAttribute("class","deviceMode");
}
else
{
bodyTag.setAttribute("class","desktopMode");
}
```
…and then hiding/showing relevant content as required using the .deviceMode/.desktopMode classes.

-	In mobile view, aim to maximise the designer’s content area by avoiding padding/margins on the designer’s container or its ancestors.
