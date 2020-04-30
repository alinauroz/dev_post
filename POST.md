[Comment]: # (All of this is placeholder text. Use this format or any other format of your choosing to best describe your project.)

[Reminder]: # (Make sure you've submitted the Twilio CodeExchange agreement: https://ahoy.twilio.com/code-exchange-community)

[Important]: # (By making a submission, you agree to the competition's terms: https://www.twilio.com/legal/twilio-dev-hackathon-terms)


## What I built

The main purpose of UNO App is to fill the technology gap among the people. In current era, people dont have to carry cash, they dont have to do to markets to buy prodcuts or to pay utlility bills. But still there are around 4 billion people who dont have access to internet. They have to go to markets and carry cash. They have no idea of Paypal or Amazon.

![Limited Access](https://i.imgur.com/5P2u1W7.png)

In this time of pandemic, it is very dangerous to go to markets or deal in cash. But the people who are unfamiliar with the internet and smartphones have to go to markets. _Uno app_ has a feature that will connect these people to the powerful APIs such as online fund transfers, online shopping and getting aid from governement. This app exploits the information that goverments have in order to provide as much as possible ease of access to these people with the help of **feature phone** or a smartphone without internet.

![Access to All](https://imgur.com/vD8iDGR.png)

**How this app can help in minimizing the Virus**

The problems of the poor countries which enhance the risk of damage due to the Virus are:
    
- Markets are Open
People can not do online shopping because they do not have internet or they can't use technology. People gather in markets which enhance the risk of damage.

_UNO App has solved this problem. People can easily buy food and other items of daily need using a feature phone. They can send a message to a Twilio Number then twilio will connect this person to some Webhook. In this way people poor people do not have to go to markets._

- Dealing in Cash
People deal in cash. Cash pass from one person to another. There is a chance that this cash gets infected by some corona virus patient and then passes to some healthy person.

_UNO App has a service called FundTransfer. People can easily transfer funds to other people using feature phone. This is like using Paypal on a feature phone._

- Uneven distribution of Aid: Areas which don't get highlighted on Social Media or Television don't get aid from governement. They people who don't have internet can neither use social media nor apply for aid.  

_UNO App has a serive called FoodAtHome which is very easy to use. Send an SMS with text `foodathome` to Organization's Twilio Number and twilio will send a request to this organization and register you for aid. You dont event have to enter you details becuase government already have it._


#### Category Submission: 


## Demo Link

This app is deployed on Heroku. You can check it [here](https://frozen-sierra-78630.herokuapp.com/dashboard). My account is Twilio's Trial Account so it can only work with limited numbers. But fortunatley you can test it through whatsapp.

Below is a demo of the app. I used whatsapp. You can also use SMS to test. The procedure is almost same.

![Demo 1 - Transferring Funds](https://github.com/alinauroz/dev_post/blob/master/shopping_demo.gif?raw=true)

**Note: Your number is not present in our database so you can't transfer funds or buy something. You can olny get help and view products available for shopping. Commands with [!] will not work with any unregistered number.**

Send a message with `possible-those` to +14155238886 and then use following commands:

### FundTransfer

_For Help:_ send `fundtransfer help` to your Twilio number

_[!]To Transfer Fund:_ send `fundtransfer <Recipient's Phone Number> <Amount>` to your Twilio number 

### Shopping

_For Help:_ send `shopping help` to your Twilio number

_To View List of Items:_ send `shopping list` to your Twilio number

_[!]To Purchase an Item:_ send `shopping <Product Id>` to your Twilio number

### FoodAtHome

_For Help:_ send `foodathome help` to your Twilio number

_[!]To Register:_ send `foodathome <no. of people>` to your Twilio number

_All other requirements such as home address, cnic, user's balance etc will be retrieved using user's phone number_

## Link to Code

Code is available on [GitHub](https://github.com/alinauroz/virtual_assitance). Follow Readme to deploy this app on your server.

## How I built it (what's the stack? did I run into issues or discover something new along the way?)

NodeJS is used to develop this app. Below is a breif description of stack used to develop this.

- FileSystem + Lodash to develop database
- Twilio NodeJS SDK to communicate with Twilio
- Socket.io to give live updates to admins

The app follows three layer archittecture. API layer receives requests. This layer also have webhooks to which Twilio sends requests. The request is also authneticated here. If authentication is not done, anyone send a POST request to the webhook. This may result in unauthorize balance deduction in our case.

After authetication, the received message is parsed using `parse` function which can be found in functions directory. In service layer, there are three services. Every service have a handle function. Handle function receives the parsed input that user has sent and process it.

```javascript

const handle = input => {
    if (input.help) {
        help(input.from, input.via);
        io.sockets.emit("update", {updateType : "help", msg : `${formatNumber(input.from)} asked for help to do shopping`, date : Date.now()});
    }
    else if (input.list) {
        //users wants shopping list
        sendShoppingList(input.from, input.via);
        io.sockets.emit("update", {updateType : "purchase", msg : `${formatNumber(input.from)} requested for shopping list`, date : Date.now()});
    }
    else if (input.id) {
        if(buy(input.from, input.via, input.id)) {
            io.sockets.emit("update", {updateType : "purchase", msg : `${formatNumber(input.from)} bought ${inventory.get(input.id).name}`, date : Date.now()});
        }
    }
}

```

When a command is runs it sends update to admins using socket io. After processing a request, result is sent to user either on whatsapp or using sms. Twilio's Whatsapp and SMS SDK is present in integration layer. This layer also has direct connection to database.

```javascript

client.messages.create({
    from: 'whatsapp:' + env.whatsapp_number,
    body : msg,
    to: to
})


```

_You can find complete code on github and run it on your server. For testing, you dont have to send messages everytime you want to check your app. Just use POST and send request to your webhook and don't forget to turn off authetification while testing otherwise you will get an error._
