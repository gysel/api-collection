# Release notes

## 2.10

This release introduces Viber as third channel on the Conversations. 

Also, interactive templates with buttons as well as stickers are now available on WhatsApp.

### Features

#### Viber support

Beginning with this release the Conversations starts supporting Viber as channel.

The Viber channel supports to send the following through messages:

text
image
text & button
text & image & button


Using the channel requires you to request a viber id from tyntec.

### WhatsApp - Interactive templates 

With this release the API supports requesting and sending interactive templates.

These templates can provide up to 3 quick reply buttons or 2 call to action buttons (URL and phone number).

The quick reply buttons can be charged with a custom payload that is send back in the new event `MoMessage::Postback`
when a user clicks on the button. 

### WhatsApp - New content types on rich templates

This release enables the following content types in the header component of rich templates:

 - text
 - video
 - location

## Changes

### Length restriction on body component of rich templates

It's now possible to define up to 1024 characters on the body component of rich templates.

Previously it was 160.

**Note** : The length restriction still applies to the hydrate template (template text including parameter replacements).

## Deprecations

### Groups API

WhatsApp decided to deprecate the Groups API 6th of July for new and 6th of October for all WhatsApp business accounts.
 
This affects on our side

 - group management API 
 - sending messages to groups
 
WhatsApp announced a replacement but without any timeline yet.

## 2.9

This release introduces the WhatsApp template management API and also APIs to retrieve information about your WhatsApp account and assigned phone numbers.

### Features

#### WhatsApp Account information

The API allows you now to receive information about your WhatsApp accounts. At the moment these are only basic information like
the message on behalf status, the Facebook Business Manager ID.

This will be enhanced in the future to provide you with more detailed information about the current status of your account requests.

#### WhatsApp phone number information

The API provides now information about phone numbers assigned to your WhatsApp business account.

Most important are the `status` and `quality rating`. 

The `status` provides you with information if your number is 

 - pending - it hasn't been yet activated
 - dis/connected - is it available for sending
 - flagged - due to user reports about low quality messaging
 - restricted - it has reached the daily unique users limit
 
The `quality rating` shows you how your messages are perceived by your users. When a number is `flagged` and has a `red` rating, it is on risk for being permanently banned from WhatsApp.

#### WhatsApp template management

The API allows you now to request new templates (standard and rich media templates). As well add new localizations to an existing template.

It's possible also to delete a template and get information about it's status.

**Note** It's not possible to edit a rejected template at the moment due to limitations by WhatsApp API.

## 2.8

This release introduces the support of WhatsApp contact messages, the WhatsApp group admin API.
And also the first release of the applications API. 

### Features

#### Applications API - First Release

This release starts with an introduction of applications.

Applications will allow you to control which events should be delivered to your webhook.

The level of control is down to the single event. This enables you to route different events to
different webhooks.

This release only provides the default application, which acts as the global fallback
in case no other application was configured and attached to the messaging request.

In the future, custom applications will be introduced. These will allow specific webhooks to be used for each set of channels.

#### Support for Sending and Receiving Contacts

You can now send a contact to a WhatsApp user as well receive an Inbound Message with content type ``contacts``.  

#### WhatsApp Group Admin Management

You can now promote and demote participants of a group to group admins.

This change also adds the events 

- ``WhatsAppGroupEvent::userPromoted`` and
- ``WhatsAppGroupEvent::userDemoted``

indicating the promotion and demotion of a user.

#### WhatsApp Group Icon Events

When a user modifies the group icon, you can now receive the following events: 

- ``WhatsAppGroupEvent::iconChanged`` and 
 - ``WhatsAppGroupEvent::iconDeleted``

to stay informed of any changes.

#### Deletion of WhatsApp Profile Logo

The logo of the profile can now be removed

#### Public WhatsApp Profile Logo Link 

The logo resource supports now retrieval of the profile logo via the public WhatsApp link.

### Fixes

#### Marking a message as read will not work without the following body

The documentation of marking a message as read, which is a capability of WhatsApp, missed the required body:

    { "status" : "read" }

## 2.7

This release introduces rich media notifications to the WhatsApp API as well the support of the read indicator.

Rich media notifications allow you to send apart from the classic text templates as well image and pdf files as templated messages to your customers.

**Note** Please be aware that the rich templates require to request a specific template **per** file type you want to send.

### Features

#### Read indicator on user messages

You can now set the read indicator (double blue check mark on WhatsApp) on messages received by your user.

#### Support of rich templates via WhatsApp

Rich templates allow you to send apart from the classic text templates as well image and pdf files as templated messages to your customers. The parameters are specified in the newly added `whatsapp.template.components` property of type array.

Each component is identified by a type (currently `header` and `body` are supported). In case of a classic text template only the `body` component is specified. When a rich template should be used, both `header` and `body` have to be specified.

### Deprecations

#### `whatsapp.template.parameters` definition

In order to make the usage of the API simpler and support the extensibility we introduce the more flexible components structure. 
This introduction deprecates the existing `whatsapp.template.parameters` which will be dropped in the next major release.

## 2.6

This release introduces the WhatsApp profile API.

### Features

#### WhatsApp profile API

This self-service API enables you to change the following information of your WhatsApp account:

 - logo
 - about
 - description
 - e-mail
 - address
 - vertical
 - websites

The API supports patching of single / specific information if needed.

## 2.5

This release introduces the first intermediate event type. These event types are meant for informational purposes during the message delivery processes. They will always be followed by a final event (_MessageStatus::failed_, _MessageStatus::delivered_, _MessageStatus::seen_).

In order not to break existing clients, you need to opt-in for the new event(s) before we start pushing them to you.

### Features

#### WhatsApp preview url support

With this release you can toggle if an url in a text messages has a preview rendered. The property to control this behavior is called ``urlPreviewDisplayed`` and is of type ``boolean``. It defaults to ``false``.

#### Failure details on MessageStatus events

The MessageStatus is extended with the ``details`` property. This property provides information about ``code`` and ``message`` in case of an errorneous situation which lead to a failure.

#### Additional MessageStatus events

Additional MessageStatus events are defined

- MessageStatus::accepted - send out after we have accepted your message
- MessageStatus::channelFailed - send out after a failure occured on the delivery channel. Contains the specific information about the error in the property ``details``. This event is an **intermediate** event and will be followed by one of the final events (_MessageStatus::failed_, _MessageStatus::delivered_, _MessageStatus::seen_).
  
#### ``to`` on MessageStatus::deleted

When a user deletes a message he has sent previously, the ChatAPI now also transports the receiving account id in the property ``to``.

### Deprecations

#### content type ``url``

As the content type is not used and in order to make the consumption of the API simpler we will drop the support of content type ``url`` on WhatsApp and SMS in a future release. Starting with this release the url will be translated on WhatsApp messages to content type ``text`` with ``urlPreviewDisplayed`` enabled.

Please use the contentType ``text`` on both WhatsApp and SMS.

#### language policy ``fallback``

We got notified by WhatsApp that they will drop the ``fallback`` policy on template messages. As we need to stay up-to-date with the WhatsApp API, we will also drop this policy in the next month.

Please switch to language policy ``deterministic``.

### Fixes

#### Documentation

##### Fix mistake in WhatsAppGroupInviteLink

Align the described property ``inviteLink`` with the actual property ``link`` produced by the system.

##### Fix mistake in MessageStatus

Remove mandatory flag of property ``from`` as one event (``MessageStatus::failed``) never supported it.

## 2.4

### General

#### New resources

In order to enable the group management capabilities and be extensible in the future the Conversations  is extended with a new path pattern

    channels/<channel name>

All channel-specific capabilities will reside under this base path.

#### URL move

The Conversations has been moved to https://api.tyntec.com/chat-api/v2. The old base URL is still accessible, but will not get any further major upgrades.

#### Inbound messages are events

With this release we start to treat the objects send to your API, eg. MoMessage or MessageStatus, as events. In order to support this shift in the model all objects 
pushed actively to your system will contain - from this release on - a property named _event_. This property indicates not only the object type, but as well event details.
For example, if a message was delivered, the event will be: _MessageStatus::delivered_.

This enables your systems to easily filter out unwanted events without the needs to have a deep look into the data transferred.

### Features

#### WhatsApp Group Chat support

Starting with this release the Conversations makes the WhatsApp group chat functionality available.

The current release supports:

 - listing all groups
 - creating a new group
 - getting list of participants and remove them from the group
 - getting information about the group
 - updating the group profile and icon
 - maintaining the invite link
 - group chat itself via the normal messaging API
 - group specific events, such as _user has joined the group_ or _user has left the group_

#### Location support

On inbound and outbound messages locations can be shared.

They are of contentType _location_ and are represented as shown in this example

    "location" : {
      "longitude": 7.4954884,
      "latitude": 51.5005765,
      "name": "tyntec GmbH",
      "address": "tyntec GmbH, Semerteichstraße, Dortmund"
    }

### Deprecations

#### ``deliveryChannel`` property

In order to make the consumption of the API simpler and as a preparation for a future release, we decided to deprecate the property ``deliveryChannel`` on the MessageStatus and HistoryItem object and replace it with the property ``channel``. 

Until the property ``deliveryChannel`` is removed both properties are provided with the same data.

#### ``status`` property on MessageStatus

As a preparation for a future release, we decided to deprecate the property ``status`` on the MessageStatus object and replace it with the property ``event``. 

Until the property ``status`` is removed both properties are provided.

#### ``receivedAt`` property on MoMessage

As a preparation for a future release, we decided to deprecate the property ``receivedAt`` on the MoMessage object and replace it with the property ``timestamp``. 

Until the property ``receivedAt`` is removed both properties are provided.

#### ``DefaultContent`` object

In order to make the consumption of the API simpler (and as it was not being used) we decided to drop the support of the default content in the next major release.

Please migrate to the channel specific objects _WhatsappMessageRequest_, _SMSRequest_ and/or _TyntecEchoRequest_.

### Fixes

Minor fixes that clarify documentation.

### Improvements

#### ``from`` property on History and MessageStatus

In alignment with the Group Chat feature the message History and MessageStatus are enhanced with the property ``from``. This property contains the id of the issuer of the event.

On Group Chats this is usefull to understand if all members of a group have received and/or read the message send. And which member has joined, left, ... the group

#### ``event`` property on MoMessage and MessageStatus 

As a preparation for a future release we introduced the property _event on the MoMessage and MessageStatus object. This property holds the type of event consisting of classification and, if applicable, detail.

For the MoMessage the event is always _MoMessage_.

For the MessageStatus it can be one of:

  - MessageStatus::accepted
  - MessageStatus::delivered
  - MessageStatus::seen
  - MessageStatus::failed
  - MessageStatus::unknown
  - MessageStatus::deleted

#### Documentation

In order to ease the maintenance and improve readability the open API specification file has been slightly rearranged and contains other improvements including the use of more common properties, etc..

## 2.3

### Fixes

#### Missing caption support on MoMedia

The MoMedia object now transfers the caption set by the user.

## 2.2

### Features

#### User Context support

You can now set a user context, which is transferred back to notifications.

#### Notification Callback URL override

You can now override the default notification callback URL per message request.

Our system will then send notifications to this endpoint instead of the default one.

The same rules as for the default notification endpoint applies

#### Video media type support

The video media type can now be send to users.

### Fixes

#### message ID on MO messages

The previous versions stated that the message ID on MO messages is a UUID. As this has changed with Release 2.0,
the documentation has now been updated with the correct information: the message id is just a string.

#### base URL pointing to the correct major version

Here's the previous version that's pointed to a deprecated base url: (https://api.tyntec.com/messaging/v1/chat). The correct version is: (https://api.tyntec.com/messaging/v2/chat)


## 2.1

### Features

#### History object provides details about the status

The history object now provides details why a certain state was reached. For example the error code and reason.

### Fixes

#### Documentation improved

A few improvements to  explanatory texts were made.

## 2.0

### Breaking changes

#### Mo Message ID data type changed from UUID to plain string.

In order to support the delete status without introducing a new model element, we decided to relax the data type definition of the Mo Message ID from UUID to plain string.

### Features

#### Support of video media type

#### Mo Messages provide profile information of the sender

#### Mo Messages provide reply message ID

The Mo Messages transfer the message ID the message replies to. Currently it works only for WhatsApp.

#### Delete status added to notifications

When the client deletes a message that was previously sent, a delete notification is sent.