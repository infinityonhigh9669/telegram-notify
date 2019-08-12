# telegram-notify
Send your Server Notifications thru Telegram

As a system administrator, you must be receiving your important server notifications by emails. Email has been used since decades, but it is slowly replaced in everydays life by social networks. With social network clients, messages are usually displayed as thread and get instant notification on your smartphone.

So, why not receiving your main server notifications on one of your favorite social network client ?

Telegram is becoming a very popular social network as it is free and multi-platforms. It has also a unique feature : it's allowing the use of bots. Bots are very interesting as they can be seen as an application account associated to a real person's account. They are able to send or receive messages to/from either an individual account or a group.

So, instead of sending a mail, it could be very interesting for your Debian/Ubuntu server to be able to send you Telegram notification. You can for example create an administrator group and send all your server notifications to this group. Any administrator belonging to this Telegram group will get instant notification from the server.

This article explains how to setup a server environment to very easily send Telegram notifications from any of your server script or service. It also provides a script that allows you to send Telegram messages including text, photos, documents, logs, …

It has been tested on a Debian 8.5 server but it should work on any modern Linux distribution.

1. Pre-Requisite
1.1. Create your Telegram Bot
Telegram allows you to easily create your own Bot with the help of @BotFather.

This page explain very clearly what you can expect from your Telegram Bot and how to create it.

To make it short, a Bot is a type of Telegram account that doesn't need to be associated to a phone number. It's an application account associated to your personnal account.

Once you have created your Bot, you get your API key. This key looks like 123456789:AABBCCDD_abcdefghijklmnopqrstuvwxyz.

As Telegram APIs are using plain httpsURL with POST data, this API key is needed to identify the Bot when you send messages, photos and documents straight from any web client.

All parameters are explained in Telegram API page.

Your Bot will be able to send messages either to :

your personnal account
any channel it belongs to
But to send any message, your Bot will need to use either your user ID or a Channel ID. It can not address an account simply using its name.

Here is the way to get these IDs.

1.2. Get your User ID
To get your user ID, you just need to :

send a message to your Bot from your Telegram client
call the following URL from any web browser
 URL	 https://api.telegram.org/bot123456789:AABBCCDD_abcdefghijklmnopqrstuvwxyz/getUpdates
 Answer	 {"ok":true,"result":[{"update_id":563273027,"message":{"message_id":505, "from":"id":11223344, "first_name":"YourFirstName", "last_name":"YourLastName", "username":"YourUserName"},
"chat":{"id":11223344, "first_name":"YourFirstName", "last_name":"YourLastName", "username":"YourUserName", "type":"private"}, "date":1483150094, "text":"Hello bot !"}}]}
 

You can see here that your user ID is 11223344.

This is the client ID that Telegram Bot should use to send me a message.

1.3. Get Channel ID
If your server must notify multiple users, it should notify a channel.

First, from your Telegram client, create a channel and keep it public as you need to obtain the channel ID. Give a name @YourNewChannelName to your channel.

Next, from your Bot thread, go to the Bot parameters page. You should see that your Bot is in both Members and Administrators list.

The Bot is now ready to publish messages.

To get your channel ID, you just need to send a message to @YourNewChannelName from any web browser using your Bot API key :

 URL	https://api.telegram.org/bot123456789:AABBCCDD_abcdefghijklmnopqrstuvwxyz/sendMessage?chat_id=@YourNewChannelName&text=FirstMessage
 Answer	
{"ok":true, "result":{"message_id":2, "chat":{"id":-1234567890123, "title":"Your Channel", "username":"YourNewChannelName", "type":"channel"}, "date":1483383169, "text":"FirstMessage"}} 

 

You can see in the resulting JSON string that your new channel ID is -1234567890123.

You can now edit your Channel and convert its type to Private.

Your Bot can still send a message to the channel using the Channel ID :

 URL	https://api.telegram.org/bot123456789:AABBCCDD_abcdefghijklmnopqrstuvwxyz/sendMessage?chat_id=-1234567890123&text=SecondMessage
 Answer	
{"ok":true, "result":{"message_id":2, "chat":{"id":-1234567890123, "title":"Your Channel", "username":"YourNewChannelName", "type":"channel"}, "date":1483383169, "text":"SecondMessage"}}

 

2. Telegram Notification Script
It's now time to install and configure Telegram notification script on the server.

The script will be in charge of sending Telegram notification and can be used by any script, service, daemon, … on the server.

2.1. Functionalities
Main functionality of Telegram notification script is to … send a Telegram notification.

It allows to :

display plain text message or a file content (log, …)
display a title
display an emoji as leading icon  (codes may be found in this emoji table)
add a photo or a document
select formatting style as Markdown (default) or HTML
disable message notification on the client side
select Bot to use to send the message (thru API key)
set message recipient ID

2.2. Script
Main notification script is placed under /usr/local/sbin/telegram-notify.

2.3. Configuration
Telegram notification script is using 2 default parameters that should be configured in /etc/telegram-notify.conf :

api-key : Your Bot API key
user-id : Default User ID or Channel ID to send messages to

