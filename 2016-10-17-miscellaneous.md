## Make use of the Webhooks in Slack to post messages into a channel using the API
The used bot is `Incoming WebHooks`. You can add it to your own slack team, and connect it to other services like gitlab or others. If you want to monitor applications from your slack channel without developping a bot, as long as your app can output a JSON, this is your goto app/bot.
[More information](https://ardinghelli.slack.com/services/B2Q8MB75G?added=1)
### Example of the command using curl:
Here I chose the `general` channel, you can use random or any other `existing` channel. You can give an interestin number of parameters. I do not know if you can extend this (à la json schema) to support specialized parameters.
```bash
curl -X POST --data-urlencode 'payload={"channel": "#general", "username": "webhookbot", "text": "Je poste ce message depuis un Curl de mon terminal. Il utilise un bot appele webhookbot.", "icon_emoji": ":ghost:"}' https://hooks.slack.com/services/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
```
