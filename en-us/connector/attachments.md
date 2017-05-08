---
layout: 'redirect'
permalink: /en-us/core-concepts/attachments/
redir_to: 'https://docs.microsoft.com/en-us/bot-framework/rest-api/bot-framework-rest-connector-send-and-receive-messages'
sitemap: false
---


Some channels support attachments that you can use to send richer responses to the user. For example, you can send images, videos, audio, and rich cards. To determine the type and number of attachments that a channel supports, and how the channel renders the attachment, see the channel's documentation.

To add an attachment, create an [Attachment](../reference/#attachment) object and set the **contentType** property to the appropriate media type.  

If you're attaching an image, you'd set the **contentType** property to the `image` media type and its subtype to PNG, GIF, or JPEG (for example, `image/png`). Depending on the channel and the type of images that they support, you'd set the **contentUrl** property or the **thumbnailUrl** property to the URL of the image (for example, http://aka.ms/Fo983c). For channels that support inline binaries of the image, you can set the property to a base64 binary of the image (for example, data:image/png;base64,iVBORw0KGgo…). The channel will display the image or the image's URL next to the message's text string.

After completing the attachment, add it to the message's **attachments** array.

The following example shows a message with an single image attachment.

```cmd
POST https://api.botframework.com/v3/conversations/abcd1234/activities/5d5cdc723 HTTP/1.1
Authorization: Bearer eyJhbGciOiJIUzI1Ni...
Content-Type: application/json

{
    "type": "message",
    "from": {
        "id": "12345678",
        "name": "sender's name"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "conversation's name"
   },
   "recipient": {
        "id": "1234abcd",
        "name": "recipient's name"
    },
    "text": "Here's a pic of the duck I was telling you about.",
    "attachments": [
        {
            "contentType": "image/png",
            "contentUrl": http://aka.ms/Fo983c
        }
    ],
    "replyToId": "5d5cdc723"
}
```

Attaching a video or audio is the same as attaching images. Depending on the channel, the video and audio may be played inline or it may be displayed as a link.

Channels can also add attachments to the messages that they send your bot. For example, the message may contain an attachment if the user can upload a photo to be analyzed or a document to be stored.

###Attaching rich cards to the message

Some channels support the following rich cards.

|**Card**|**Description**
|[AnimationCard](../reference/#animationcard)|A card that's capable of playing animated GIFs or short videos.
|[AudioCard](../reference/#audiocard)|A card that's capable of playing an audio file.
|[HeroCard](../reference/#herocard)|A card with a large image, title, text, and action buttons.
|[ThumbnailCard](../reference/#thumbnailcard)|Same as a Hero card except the image is a thumbnail.
|[ReceiptCard](../reference/#receiptcard)|A card that contains a receipt for a purchase.
|[SignInCard](../reference/#signincard)|A card that lets a user sign in to a service.
|[VideoCard](#videocard)|A card that's capable of playing videos.

To determine which cards a channel supports, see the channel's documentation. The channel's documentation will also specify any limits placed on the contents of the cards. For example, the maximum number of action buttons or the maximum length of the title and text strings. If you specify a rich card attachment, you typically don't include the message's **text** property.

To add a rich card attachment, create an [Attachment](../reference/#attachment) object and set the **contentType** property to the card's media type. For example, for a hero card, you'd set **contentType** to application/vnd.microsoft.card.hero. After creating the card that you want to attach, set the **content** property to it.

Add the attachment to the message's **attachments** array. Depending on the channel, you can add one or more rich cards to the **attachments** array. You might create multiple cards if you're providing multiple options to a user. For example, if your bot lets users book hotel rooms, the list could contain a picture and amenities of all the rooms that the user can select from. If you add multiple cards, you should set the **attachmentLayout** property to specify whether to display the cards in a list (default) or in a carousel.  

The following example shows a message with an single Hero card attachment.

```cmd
POST https://api.botframework.com/v3/conversations/abcd1234/activities/5d5cdc723 HTTP/1.1
Authorization: Bearer eyJhbGciOiJIUzI1Ni...
Content-Type: application/json

{
    "type": "message",
    "from": {
        "id": "12345678",
        "name": "sender's name"
    },
    "conversation": {
        "id": "abcd1234",
        "name": "conversation's name"
   },
   "recipient": {
        "id": "1234abcd",
        "name": "recipient's name"
    },
    "attachments": [
        {
            "contentType": "application/vnd.microsoft.card.hero",
            "content": {
                "title": "title goes here",
                "subtitle": "subtitle goes here",
                "text": "descriptive text goes here",
                "images": [
                    {
                        "url": "http://aka.ms/Fo983c",
                        "alt": "picture of a duck",
                        "tap": {
                            "type": "playAudio",
                            "value": "url to an audio track of a duck call goes here"
                        }
                    }
                ],
                "buttons": [
                    {
                        "type": "playAudio",
                        "title": "Duck Call",
                        "value": " url goes here of an audio track of a duck call"
                    },
                    {
                        "type": "openUrl",
                        "title": "Watch Video",
                        "image": "http://aka.ms/Fo983c",
                        "value": "url goes here of the duck in flight"
                    }
                ]
            }
        }
    ],
    "replyToId": "5d5cdc723"
}
```

For information about adding channel-specific data to a message, see [Adding Channel Data](../channeldata).

For information about sending messages, [Sending and Receiving Messages](../messages).