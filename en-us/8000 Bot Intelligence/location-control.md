---
layout: page
title: Location Control
permalink: /en-us/bot-intelligence/location-control/
weight: 8280
parent1: Bot Intelligence
---


* TOC
{:toc}

## Overview
The Bing location control for Microsoft Bot Framework makes the process of collecting and validating the user's desired location in a conversation easy and reliable. The control is available for C# and Node.js and works consistently across all channels supported by Bot Framework. 

![Basic Scenario](/en-us/images/locationcontrol/skype_multiaddress_1.png)

## Use Case and Features
Bots often need the user to input a location to complete a task. For example, a Taxi bot requires the user's pickup and destination address before requesting a ride. Similarly, a Pizza bot must know the user's delivery address to submit the order, and so on. Normally, bot developers need to use a combination of location or place APIs, and have their bots engage in a multi-turn dialog with users to get their desired location and subsequently validate it. The development steps are usually complicated and error-prone.  

The Bing location control makes this process easy by abstracting away the tedious coding steps to let the user pick a location and reliably validate it. The control offers the following capabilities: 

- Address look up and validation using Bing's Maps REST services. 
- Address disambiguation when more than one address is found.
- Support for declaring required location fields.
- Support for FB Messenger's location picker GUI dialog.
- Open-source code (C# and Node.js) with customizable dialog strings. 

You can find screenshots of the scenarios supported by the location control in the [Examples](#examples) section. 

## Getting Started
As a prerequisite to use the location control, you need to obtain a Bing Maps API subscription key. You can sign up to get a free key with up to 10,000 transactions per month in [Azure Portal](https://azure.microsoft.com/en-us/marketplace/partners/bingmaps/mapapis/){:target="_blank"}.

The following sections describe the coding steps to add the location control to your bot. You can find the full developer guides and sample bots for [C#](https://github.com/Microsoft/BotBuilder-Location/tree/master/CSharp){:target="_blank"} and [Node.js](https://github.com/Microsoft/BotBuilder-Location/tree/master/Node){:target="_blank"} in the [BotBuilder-Location](https://github.com/Microsoft/BotBuilder-Location/tree/master/){:target="_blank"} github repository. 

## Code Highlights

### Start using the location control
{:.no_toc}

In C#, import the Microsoft.Bot.Builder.Location package from NuGet and add the following namespace in your code. 

{% highlight csharp %}
using Microsoft.Bot.Builder.Location;
{% endhighlight %}

In Node.js, install the BotBuilder-Location module using npm and load it.

    npm install --save botbuilder-location    

{% highlight JavaScript %}
var locationDialog = require('botbuilder-location');
{% endhighlight %}

<br />

### Calling the location control with default parameters
{:.no_toc}

The example initiates the location control with default parameters, which returns a custom prompt message asking the user to provide an address. 

<div id="thetabs1">
  <ul>
    <li><a href="#tab11">C#</a></li>
    <li><a href="#tab12">Node.js</a></li>
  </ul>

<div id="tab11">

{% highlight csharp %}
var apiKey = WebConfigurationManager.AppSettings["BingMapsApiKey"];
var prompt = "Where should I ship your order? Type or say an address.";
var locationDialog = new LocationDialog(apiKey, message.ChannelId, prompt);
context.Call(locationDialog, (dialogContext, result) => {...});
{% endhighlight %}

</div>
<div id="tab12">

{% highlight JavaScript %}
locationDialog.getLocation(session,
 { prompt: "Where should I ship your order? Type or say an address." });
{% endhighlight %}

</div>  
</div>
<br />

### Using FB Messenger's location picker GUI dialog 
{:.no_toc}

FB Messenger supports a location picker GUI dialog to let the user select an address. If you prefer to use FB Messenger's native dialog,  pass the `LocationOptions.UseNativeControl` option in the location control's constructor.  

<div id="thetabs2">
  <ul>
    <li><a href="#tab21">C#</a></li>
    <li><a href="#tab22">Node.js</a></li>
  </ul>

<div id="tab21">

{% highlight csharp %}
var apiKey = WebConfigurationManager.AppSettings["BingMapsApiKey"];
var prompt = "Where should I ship your order? Type or say an address.";
var locationDialog = new LocationDialog(apiKey, message.ChannelId, prompt, LocationOptions.UseNativeControl);
context.Call(locationDialog, (dialogContext, result) => {...});
{% endhighlight %}

</div>
<div id="tab22">

{% highlight JavaScript %}
var options = {
    prompt: "Where should I ship your order? Type or say an address.",
    useNativeControl: true
};
locationDialog.getLocation(session, options);
{% endhighlight %}

</div>  
</div>
<br />

FB Messenger by default returns only the lat/long coordinates for any address selected via the location picker GUI dialog. You can additionally use the `LocationOptions.ReverseGeocode` option to have Bing reverse geocode the returned coordinates and automatically fill in the remaining address fields. 

<div id="thetabs3">
  <ul>
    <li><a href="#tab31">C#</a></li>
    <li><a href="#tab32">Node.js</a></li>
  </ul>

<div id="tab31">

{% highlight csharp %}
var apiKey = WebConfigurationManager.AppSettings["BingMapsApiKey"];
var prompt = "Where should I ship your order? Type or say an address.";
var locationDialog = new LocationDialog(apiKey, message.ChannelId, prompt, LocationOptions.UseNativeControl | LocationOptions.ReverseGeocode);
context.Call(locationDialog, (dialogContext, result) => {...});
{% endhighlight %}

</div>
<div id="tab32">

{% highlight JavaScript %}
var options = {
    prompt: "Where should I ship your order? Type or say an address.",
    useNativeControl: true,
    reverseGeocode: true
};
locationDialog.getLocation(session, options);
{% endhighlight %}

</div>  
</div>
<br />

**Note**: Reverse geocoding is an inherently imprecise operation. For that reason, when the reverse geocode option is selected, the location control will collect only the `PostalAddress.Locality`, `PostalAddress.Region`, `PostalAddress.Country` and `PostalAddress.PostalCode` fields and ask the user to provide the desired street address manually. 

<br />

### Specifying required fields 
{:.no_toc}

You can specify required location fields that need to be collected by the control. If the user does not provide values for one or more required fields, the control will prompt him to fill them in. You can specify required fields by passing them in the location control's constructor using the `LocationRequiredFields` enumeration. The example specifies the street address and postal (zip) code as required. 

<div id="thetabs4">
  <ul>
    <li><a href="#tab41">C#</a></li>
    <li><a href="#tab42">Node.js</a></li>
  </ul>

<div id="tab41">

{% highlight csharp %}
var apiKey = WebConfigurationManager.AppSettings["BingMapsApiKey"];
var prompt = "Where should I ship your order? Type or say an address.";
var locationDialog = new LocationDialog(apiKey, message.ChannelId, prompt, LocationOptions.None, LocationRequiredFields.StreetAddress | LocationRequiredFields.PostalCode);
context.Call(locationDialog, (dialogContext, result) => {...});
{% endhighlight %}

</div>
<div id="tab42">

{% highlight JavaScript %}
var options = {
    prompt: "Where should I ship your order? Type or say an address.",
    requiredFields:
        locationDialog.LocationRequiredFields.streetAddress |
        locationDialog.LocationRequiredFields.postalCode
}
locationDialog.getLocation(session, options);
{% endhighlight %}

</div>  
</div>
<br />

### Handling returned location
{:.no_toc}

The following example shows how you can leverage the location object returned by the location control in your bot code. 

<div id="thetabs5">
  <ul>
    <li><a href="#tab51">C#</a></li>
    <li><a href="#tab52">Node.js</a></li>
  </ul>

<div id="tab51">

{% highlight csharp %}
var apiKey = WebConfigurationManager.AppSettings["BingMapsApiKey"];
var prompt = "Where should I ship your order? Type or say an address.";
var locationDialog = new LocationDialog(apiKey, message.ChannelId, prompt, LocationOptions.None, LocationRequiredFields.StreetAddress | LocationRequiredFields.PostalCode);
context.Call(locationDialog, (context, result) => {
    Place place = await result;
    if (place != null)
    {
        var address = place.GetPostalAddress();
        string name = address != null ?
            $"{address.StreetAddress}, {address.Locality}, {address.Region}, {address.Country} ({address.PostalCode})" :
            "the pinned location";
        await context.PostAsync($"OK, I will ship it to {name}");
    }
    else
    {
        await context.PostAsync("OK, cancelled");
    }
}
{% endhighlight %}

</div>
<div id="tab52">

{% highlight JavaScript %}
locationDialog.create(bot);

bot.dialog("/", [
    function (session) {
        locationDialog.getLocation(session, {
            prompt: "Where should I ship your order? Type or say an address.",
            requiredFields: 
                locationDialog.LocationRequiredFields.streetAddress |
                locationDialog.LocationRequiredFields.locality |
                locationDialog.LocationRequiredFields.region |
                locationDialog.LocationRequiredFields.postalCode |
                locationDialog.LocationRequiredFields.country
        });
    },
    function (session, results) {
        if (results.response) {
            var place = results.response;
            session.send(place.streetAddress + ", " + place.locality + ", " + place.region + ", " + place.country + " (" + place.postalCode + ")");
        }
        else {
            session.send("OK, I won't be shipping it");
        }
    }
]);
{% endhighlight %}

</div>  
</div>
<br />

## Examples
The examples show different location selection scenarios supported by the Bing location control. 

**Address selection with single result returned**
{:.no_toc}

![Single Address](/en-us/images/locationcontrol/skype_singleaddress_2.png)

**Address selection with multiple results returned**
{:.no_toc}

![Multiple Addresses](/en-us/images/locationcontrol/skype_multiaddress_1.png)

**Address selection with required fields filling**
{:.no_toc}

![Required Fields](/en-us/images/locationcontrol/skype_requiredaddress_1.png)

**Address selection using FB Messenger's location picker GUI dialog**
{:.no_toc}

![Messenger Location Dialog](/en-us/images/locationcontrol/messenger_locationdialog_1.png)



