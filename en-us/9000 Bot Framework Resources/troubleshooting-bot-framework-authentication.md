---
layout: page
title: Troubleshooting Bot Framework Authentication
permalink: /en-us/support/troubleshooting-bot-framework-authentication/
weight: 9280
parent1: Bot Framework Resources
---

## Overview

This guide will help you troubleshoot authentication errors with your bot. This guide is easiest to follow if you can download and use the [Bot Framework Channel Emulator](/en-us/tools/bot-framework-emulator/) and can update your bot's ID and password. If you cannot use the emulator or make changes, only certain steps will apply.

## Before we get started

Your bot's security is configured by a Microsoft App ID and a Microsoft App Password. These values are specified within the bot -- typically in a configuration file -- and are used to retrieve access tokens from the Microsoft Account service.

This guide will walk you through steps to identify and correct common errors in your security configuration.

## Step 1: Connect without a password on localhost

*Note: This step requires the [Bot Framework Channel Emulator](/en-us/tools/bot-framework-emulator/).*

The first step is to disable security on your bot and make sure you can connect to it.

**Note**: Following this step will disable security on your bot, allowing unknown attackers to impersonate users. Only follow this step if you are operating in a protected debugging environment.

To connect without a password, blank out the Microsoft App ID and Microsoft App Password inside your configuration file.

***C# Bot Builder SDK web.config file:***


    <appSettings>
        <add key="BotId" value="MyFirstBot" />
        <add key="MicrosoftAppId" value="" />
        <add key="MicrosoftAppPassword" value="" />
    </appSettings>


***Node.js Bot Builder:***

    var connector = new builder.ChatConnector({
        appId: null,
        appPassword: null
    });

Now, follow these steps:

1. Start your bot
2. Open the channel emulator and make sure the "Emulator Url" has its default value of "http://localhost:9000/"
3. Enter your bot's endpoint into the Channel Emulator field labeled "Bot Url." The default endpoint for the Bot builder SDK is http://localhost:3978/api/messages
4. Make sure the "Microsoft App Id" and "Microsoft App Password" fields are empty
5. Type some text and press enter.

![Bot Framework Channel Emulator](/en-us/images/troubleshooting/troubleshooting-bot-framework-authentication_1.png)

Your request is successful if:

1. The bot responds
2. There are no red-circle icons in the chat window

If there is a red circle icon next to your text, click it and see what the error is.

Common errors:

1. Your bot's endpoint is incorrect. Make sure you have included the path at the end of the URL (e.g. /api/messages).
2. The bot is configured with an App ID or an App Password, when both fields should be blank.
3. The Emulator is configured with an App ID or an App Passoword, when both fields sholud be blank.

If your message was successful, you have verified that your bot responds on localhost when security is disabled.

## Step 2: Enable security and run on localhost

*Note: This step requires the [Bot Framework Channel Emulator](/en-us/tools/bot-framework-emulator/).*

The second step is to enable security and use the emulator to test that security works on your local machine. This step assumes the bot's endpoint is responding. If you're unsure if your bot's endpoint is working, perform step 1, above.

To enable security on your bot, you must retrieve a registration from the Bot Framework Developer Portal. Your bot's security relies on Microsoft services -- even when your bot is running only on localhost -- and the Developer Portal is where you can create and retrieve your registration.

If you haven't created a registration, follow these steps. You may jump ahead if you already have a Microsoft App ID and Microsoft App Password.

1. Navigate to https://dev.botframework.com
2. Click the "Sign in" link in the top right corner
3. Sign in with your Microsoft Account. If you do not have a Microsoft Account, please create one and sign in.
4. After signing in, click the "Register a bot" link.
5. Enter a name and description for your bot. Choose a Bot Handle, which is a shortened name used in URLs.
6. Halfway through the form, you will see a blue button labeled "**Generate Microsoft App Id and Password**". Click this button and it will take you to a page where you can:
	1. Generate an App Id. Copy this value and paste it in your bot's configuration file (see below).
	2. Generate a Password. Copy this value and paste it in your bot's configuration file. **Note:** this password cannot be accessed after it is created. It can only be seen once.
7. Click the "Complete and go back" button to navigate back to the bot registration page.
8. Complete the bot registration form and click "Create Bot" to save and continue.

Now that your bot is created in the Bot Framework Developer Portal, you can enable its security. Make sure the Microsoft App Id and Microsoft App Password you generated earlier are present in your configuration file.

***C# Bot Builder SDK web.config file:***


    <appSettings>
        <add key="BotId" value="MyFirstBot" />
        <add key="MicrosoftAppId" value="791a4e72-d841-4145-96d9-23b4989d70d6" />
        <add key="MicrosoftAppPassword" value="PASSWORD" />
    </appSettings>


***Node.js Bot Builder:***

    var connector = new builder.ChatConnector({
        appId: '791a4e72-d841-4145-96d9-23b4989d70d6',
        appPassword: 'PASSWORD'
    });

Now, follow these steps:

1. Start your bot
2. Open the channel emulator and make sure the "Emulator Url" has its default value of "http://localhost:9000/"
3. Enter your bot's endpoint into the Channel Emulator field labeled "Bot Url." The default endpoint for the Bot builder SDK is http://localhost:3978/api/messages
4. Enter your bot's Microsoft App Id into the field labeled Microsoft App Id.
5. Enter your bot's Microsoft App Password into the field labeled Microsoft App Password.
5. Type some text and press enter.

![Bot Framework Channel Emulator](/en-us/images/troubleshooting/troubleshooting-bot-framework-authentication_2.png)

Your request is successful if:

1. The bot responds
2. There are no red-circle icons in the chat window

If there is a red circle icon next to your text, click it and see what the error is.

Common errors:

1. Your bot's endpoint is incorrect. See Step 1, above.
2. The bot is configured without a Microsoft App Id or a Microsoft App Password.
3. Your Microsoft App Id or Microsoft App Password are incorrect.

If your message was successful, you have verified that your bot responds on localhost when security is enabled.

## Step 3: Enable security and run in the cloud

*Note: This step requires the [Bot Framework Channel Emulator](/en-us/tools/bot-framework-emulator/).*

*Note: This step also requires a tool to allow your bot to send an HTTP request back to the Bot Framework Emulator, running on your development machine. [ngrok.io](https://ngrok.io) offers an easy-to-use tool for routing messages from your bot back to the Emulator so they can be displayed.*

Once you have established that your bot's endpoint can be reached (step 1) and your bot responds when security is enabled (step 2), you can deploy your bot to the cloud and try security there.

Bot Framework requires that bots be accessible from the internet. If you already have a cloud hosting provider, you can deploy your bot there. If not, [Microsoft Azure offers a Free Trial](https://azure.microsoft.com/en-us/free/) that has everything you need to host your bot. Azure also offers free SSL/TLS hosting on Azure Websites, which is a requirement for Bot Framework bots.

Once you have a hosting provider, deploy your bot with its security configuration enabled as described in step 2.

Next, start a tool to open an HTTP tunnel from your bot back to the Emulator. [ngrok.io](https://ngrok.io) is an easy-to-use tool that routes traffic to your machine for you.

**Note:** you MUST allow the bot to POST HTTP requests back to the Emulator to complete this test.

Configure ngrok with the host-header=rewrite option and the Channel Emulator default port of 9000.

    ngrok http -host-header=rewrite 9000

ngrok will display an HTTPS endpoint. You will use this URL later in the channel emulator.

Now, follow these steps to connect to your bot in the cloud with security enabled:

1. Make sure your bot is deployed and running
2. Make sure ngrok.io is running
3. Open the channel emulator
4. Copy the ngrok.io URL (or similarly internet-accessible Emulator URL) and paste it into the Emulator field labeled "Emulator Url."
5. Enter your bot's cloud endpoint into the Channel Emulator field labeled "Bot Url."
6. Enter your bot's Microsoft App Id into the field labeled Microsoft App Id.
7. Enter your bot's Microsoft App Password into the field labeled Microsoft App Password.
8. Type some text and press enter.

Your request is successful if:

1. The bot responds
2. There are no red-circle icons in the chat window

If there is a red circle icon next to your text, click it and see what the error is.

Common errors:

1. Your bot's cloud endpoint URL is not correct. Make sure you have included the path at the end of the URL (e.g. /api/messages).
2. Your bot's endpoint is not HTTPS or is not trusted by the Bot Framework Channel Emulator. Your bot must have a valid, chain-trusted certificate.
3. The bot is configured without a Microsoft App Id or a Microsoft App Password. (See Step 2 to troubleshoot this.)
4. Your Microsoft App Id or Microsoft App Password are incorrect. (See Step 2 to troubleshoot this.)
5. Your bot was unable to open an HTTP connection back to the Emulator to send a response. Make sure the "Emulator Url" field contains an internet-accessible URL that points back to the Emulator's port.

If your message was successful, you have verified that your bot responds to the emulator while in the cloud and with security enabled. **Note**, however, that the emulator uses a special security model and a final test with the Bot Framework Developer Portal is necessary to establish that the bot is operating properly.

## Step 4: Connect to your bot using the Bot Framework Developer Portal

The [Bot Framework Developer Portal](https://dev.botframework.com) contains a test panel where you can test the connection from the Bot Connector service to your bot. The security model used by the Bot Connector differs slightly from the security model used by the Emulator, and this is the most realistic step in testing your bot's authentication.

Follow these steps to test your bot in the cloud with security enabled:

1. Make sure your bot is deployed and running
2. Log in to the [Bot Framework Developer Portal](https://dev.botframework.com), click on "My bots," and if necessary, select the bot you want to debug from the list.
3. Locate the test panel in the bottom-left corner of the screen and click the blue "Test" button. The results of the test appear just below the button.

![Bot Framework Developer Portal test panel](/en-us/images/troubleshooting/troubleshooting-bot-framework-authentication_3.png)

Your request is successful if:

1. The text reads "Accepted" or "Endpoint authorization succeeded."

If an error occurred, it is displayed in the panel just below the blue Test button.

Common errors:

1. Your bot's cloud endpoint URL is not correct. Make sure you have included the path at the end of the URL (e.g. /api/messages).
2. Your bot's endpoint is not HTTPS or is not trusted by the Bot Framework Channel Emulator. Your bot must have a valid, chain-trusted certificate.
3. The bot is configured without a Microsoft App Id or a Microsoft App Password. (See Step 2 to troubleshoot this.)
4. Your Microsoft App Id or Microsoft App Password are incorrect. (See Step 2 to troubleshoot this.)

If your message was successful, you have verified that your bot responds while in the cloud and with security enabled.

**At this point, your bot is ready to be used in a Bot Framework channel such as Skype, Facebook Messenger, Direct Line, and others.**

## Step 5: Advanced troubleshooting

If you continue to encounter difficulties, here are some additional resources:

* You can use a proxying tool like [Fiddler](https://www.telerik.com/fiddler) to inspect HTTP/S traffic to and from your bot.
* You can read the [Bot Connector authentication guide](/en-us/restapi/authentication/) for more detail on the authentication technology used by the Bot Framework.
* You can find additional guides and community support via the [Bot Framework Support](/en-us/support/) page.
