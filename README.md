![](https://img.shields.io/github/license/notabombe/tradingview-webhooks-bot?style=for-the-badge)
![](https://img.shields.io/github/commit-activity/y/notabombe/tradingview-webhooks-bot?style=for-the-badge)


[tvwb_demo.webm](https://user-images.githubusercontent.com/38849824/192352217-0bd08426-98b7-4188-8e5b-67d7aa93ba09.webm)


# The What 🔬

Tradingview-webhooks-bot (TVWB) is a small, Python-based framework that allows you to extend or implement your own logic
using data from [Tradingview's webhooks](https://www.tradingview.com/support/solutions/43000529348-about-webhooks/). TVWB is not a trading library, it's a framework for building your own trading logic.

# The How 🏗

TVWB is fundamentally a set of components with a webapp serving as the GUI. TVWB was built with event-driven architecture in mind that provides you with the building blocks to extend or implement your own custom logic.
TVWB uses [Flask](https://flask.palletsprojects.com/en/2.2.x/) to handle the webhooks and provides you with a simple API to interact with the data.

# Quickstart 📘

### Installation

* [Docker](https://github.com/notabombe/tradingview-webhooks-bot/wiki/Docker) (recommended)
* [Manual](https://github.com/notabombe/tradingview-webhooks-bot/wiki/Installation)

### Hosting

* [Deploying](https://github.com/notabombe/tradingview-webhooks-bot/wiki/Hosting)
  * [Cloud](https://github.com/notabombe/tradingview-webhooks-bot/wiki/Hosting#cloud-hosting) (recommended)
  * [Local](https://github.com/notabombe/tradingview-webhooks-bot/wiki/Hosting#using-a-personal-pc)


---
Ensure you're in the `src` directory. When running the following commands, **if you installed manually**.  
**If you used docker**,
start the tvwb.py shell with `docker-compose run app shell` (in the project root directory) and omit the `python3 tvwb.py` portion of the commands.

---
### Creating an action

```bash
python3 tvwb.py action:create NewAction --register
```

This creates an action and automatically registers it with the app.  [Learn more on registering here](https://github.com/notabombe/tradingview-webhooks-bot/wiki/Registering).

_Note, action and event names should **_always_** be in PascalCase._

You can also check out some "pre-made" [community actions](https://github.com/notabombe/tradingview-webhooks-bot/tree/master/src/components/actions/community_created_actions)!

### Linking an action to an event

```bash
python3 tvwb.py action:link NewAction WebhookReceived
```

This links an action to the `WebhookReceived` event.  The `WebhookReceived` event is fired when a webhook is received by the app and is currently the only default event.

### Editing an action

Navigate to `src/components/actions/NewAction.py` and edit the `run` method.  You will see something similar to the following code.
Feel free to delete the "Custom run method" comment and replace it with your own logic.  Below is an example of how you can access
the webhook data.

```python
class NewAction(Action):
    def __init__(self):
        super().__init__()

    def run(self, *args, **kwargs):
        super().run(*args, **kwargs)  # this is required
        """
        Custom run method. Add your custom logic here.
        """
        data = self.validate_data()  # always get data from webhook by calling this method!
        print('Data from webhook:', data)
```

### Running the app

```bash
python3 tvwb.py start
```

### Sending a webhook

Navigate to `http://localhost:5000`.  Ensure you see the `WebhookReceived` Event.  Click "details" to expand the event box.
Find the "Key" field and note the value.  This is the key you will use to send a webhook to the app.  Copy the JSON data below,
replacing "YOUR_KEY_HERE" with the key you copied.

```json
{
    "key": "YOUR_KEY_HERE",
    "message": "I'm a webhook!"
}
```

The `key` field is required, as it both authenticates the webhook and tells the app which event to fire.  Besides that, you can
send any data you want.  The data will be available to your action via the `validate_data()` method. (see above, editing action)

On tradingview, create a new webhook with the above JSON data and send it to `http://ipaddr:5000/webhook`.  You should see the data from the webhook printed to the console.

### FAQs

#### So how do I actually trade?

To actually submit trades, you will have to use a library like [ccxt](https://github.com/ccxt/ccxt) for crypto currency.  For other brokers, usually there are 
SDKs or APIs available.  The general workflow would look something like: webhook signal -> tvwb (use ccxt here) -> broker.  Your trade submission would take place within the `run` method of a custom action.

#### The tvwb.py shell

You can use the `tvwb.py shell` command to open a python shell with the app context.  This allows you to interact with the app without having to enter `python3 tvwb.py` every time.
