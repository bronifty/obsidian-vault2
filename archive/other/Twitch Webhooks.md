post to the endpoint to get your bearer token with client id and secret off the twitch dev console - https://dev.twitch.tv/console/apps

```bash
curl -X POST 'https://id.twitch.tv/oauth2/token' \
-H 'Content-Type: application/x-www-form-urlencoded' \
-d 'client_id=ml6ya3ioxyp8gzn7megap7e4k00n8b&client_secret=6387rdmvq297rr9dmuwimeb90lyvk9&grant_type=client_credentials'
```


https://discuss.dev.twitch.tv/t/attempting-to-understand-how-twitch-webhooks-work-with-discord-webhooks/28121

Twitch Webhooks will send a notification to your server when an event on the topic you’ve subscribed to happens.

Simply put, you would:

1.  Send a subscribe request to Twitch for a particular topic, eg Stream Changed would be used for streams going online/offline/changing.
2.  Twitch sends a verification GET request to your webserver, to ensure it’s accessible and indeed wants that subscription. Your web server has to respond with the challenge that Twitch sent.
3.  Once you finish the verify step, Twitch will POST notifications to your web server when that topic triggers.
4.  Your web server, on receiving that notification, would need to respond to Twitch a 2xx status code, then from the notification your server would work out if the stream went online, offline, or if it was just a change of title/game. If it was going live, your server would then send whatever message you want to the Discord Webhook.
