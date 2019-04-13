# Discord CryptoBot

A discord bot originally made for ZEON, it can be easily adapted for any other currency.

# Index

- [How to install](#how-to-install)
- [Bot commands](#bot-commands)
- [Bot configuration](#bot-configuration)
- [Currently supported exchanges](#currently-supported-exchanges)
- [Image examples](#image-examples)
- [Additional](#additional)

# <a name = "how-to-install"></a> How to install

First, you can use this guide to crate the bot: https://www.digitaltrends.com/gaming/how-to-make-a-discord-bot/

The bot runs on Node.js, version 8.x or higher is recommended, it can be obtained here: https://nodejs.org/en/ 

The bot can run on any machine, but it's recommended to use a linux machine, since some commands may require a wallet that accepts RPC commands (almost all the commands can be run by calling the explorer API with `curl -s`).
In case of using a Linux machine for running the bot, you can install Node.js with these 2 commands:
```
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
sudo apt-get install -y nodejs
```
After installing Node.js, you'll need to install these 2 dependencies with the commands:
```
npm install discord.js
npm install xmlhttprequest
```
Modify the config.json to fit with your currency.

Finally, run the bot with:
```
node bot.js
```
Optionally you can run the bot on a screen with:
```
screen -S  mybot
node bot.js 
CRTL AD (to quit the screen )

```

# <a name = "bot-commands"></a> Bot commands

- **!price:** Shows the last price, volume (in BTC), ask, bid and a link to the exchange for every exchange where the coin is listed.

- **!stats:** Shows the block count, MN count, supply, collateral, MN reward, POS reward, Locked coins and agerage MN reward frecuency.
- **!stages:** Shows the upcoming reward structure changes.
- **!earnings ```[amount_on_mns]```:** Shows the ROI (in percentage and days), the MN price, the daily/weekly/monthly/yearly earnings for your cryptocurrency and optionally you can input the amount of mns to calculate the earnings.
- **!mining ```<number>``` ```[K/M/G/T]```:** Shows the expected earnings with the given hashrate, optionally you can use K,M,G or T to indicate the multiplier, example: ```!mining 500``` => asks for 500 H/s, ```!mining 20 M``` => asks for 20 MH/s, etc.

- **!balance ```<address>```:** Shows the sent, received and current balance of the given address.
- **!block-index ```<number>```:** Shows the block stats of the given block number.
- **!block-hash ```<hash>```:** Shows the block stats of the given block hash.

- **!my-address-add ```<address>```:** Adds the given address to the user assigned addresses list.
- **!my-address-del ```<address>```:** Removes the given address from the user assigned addresses list.
- **!my-address-list:** Shows the user addresses list.
- **!my-balance:** Shows the sent, received and current balance of user.

- **!my-masternode-add ```<address>```:** Adds the given address to the user assigned masternode addresses list.
- **!my-masternode-del ```<address>```:** Removes the given address from the user assigned masternode addresses list.
- **!my-mastenode-list:** Shows the user masternode addresses list and their status.
- **!my-earnings:** Shows the user expected earnings.

- **!help:** Shows every available command.
- **!about:** Shows the bot info.

- **!conf-get:** Receive a dm with the config file, requires devs permissions (see **Bot configuration**).
- **!conf-set:** Receive a dm asking to drag and drop a update of the config file, requires devs permissions (see **Bot configuration**).

# <a name ="bot-configuration"></a> Bot configuration

You'll have to modify the "config.json" file to make it fit with your cryptocurrency, there's a list of every parameter:

- **ticker:** Here is where you put the name of every exchange where you're listed, the name be equal (uppercases not necessary but recommended) to the names from **Currently supported exchanges**, follow this scheme to add the links of the API and the market: 
```
"ticker": [
    "CryptoBridge",
    "Crex24",
    "CoinExchange",
    ["Crex24", "BTC", "USD"]  // optionally you can set custom pairs like this
]
```
- **special_ticker:** CoinExchange requires a specific market id from https://www.coinexchange.io/api/v1/getmarkets to get a filtered ticker, not necessary to touch this if you don't use this exchange.
- **color:** Just the color of the embed messages.
- **devs:** List of the unique discord ids from the people who have permisions to use **!conf-get** and **!conf-set**.
- **stages:** List of every stage of the coin (MN/POS rewards and collateral from block X to Y), it follows this scheme: 
```
"stages": [
    {
      "block": 18000, // from block 0 to 18000
      "coll": 1000, // collateral = 1000 coins
      "mn": 9.50, // MN reward = 10.50 coins
      "pow": 0.50 // POW reward = 0.50 coins
    },
    {
      "block": 36000, // from block 18001 to 36000
      "coll": 2000, // collateral increased to 2000 coins
      "mn": 14.25,
      "pow": 0.35,
      "pos": 0.35   // POS reward added on this stage
    },
    {
      "block": -1, // from block 36001 to infinite
      "coll": 2000, // collateral didn't changed, but you have to put the value anyway
      "mn": 4.75,
      "pos": 0.25  // POW deleted on this stage, only POS remains
    }
]
```
- **requests:** The bash commands and/or urls used to get the data for **!stats**, **!earnings**, **!balance**, **!block-index**, **!block-hash** and **!mining**. Leaving a empty string or removing the parameter will block or partially block the bot commands that makes use of the data. It's expected to use RPC commands and explorer urls, but you can customize them to retrieve the data from other sources (Example: "blockcount": "customProgramToGetBlockCount.exe"), example of typical requests:
<pre>
"requests": {
    "blockcount": "mywalletname-cli getblockcount",
    "mncount":    "mywalletname-cli masternode count | jq .enabled", // some wallets or urls return a json and others return a number, use jq (<a href= "https://stedolan.github.io/jq/">https://stedolan.github.io/jq/</a>) if it returns a json.
    "supply":     "curl -s http://mycoinexplorer.com/ext/getmoneysupply", 
    "balance":    "curl -s http://mycoinexplorer.com/ext/getaddress/",    
    "blockindex": "mywalletname-cli getblockhash ",    // <b>!!!</b> trailing space added on purpose or won't work, remove it only if using a url, same for "balance" and "blockhash".
    "blockhash":  "mywalletname-cli getblock ",
    "hashrate": "curl -s http://mycoinexplorer.com/api/getnetworkhashps",
    "mnstat": "mywalletname-cli masternode list " // some wallets may require a custom script instead
}
Important note if you customize the requests: 
    - "blockcount" must return a string convertible into a number.
    - "mncount" must return a string convertible into a number.
    - "supply" must return a string convertible into a number.
    - "balance" expects to receive a string (the address) and must return a json type string with a number or string in three attributes called "sent", "received" and "balance".
    - "blockindex": expects to receive a string convertible into a number and must return a string that indicates the block hash of the given block number.
    - "blockhash": expects to receive a string (the hash) and must return a json type string with the attributes "height": (block number), "hash": (block hash), "confirmations": (number), "size": (size of the block), "previousblockhash": (last block hash), "nextblockhash": (next block hash) and "tx": [ (list of the block transactions) ].
    - "hashrate": expects to receive a string convertible into a number.
    - "mnstat": expects a json with "addr" and "status" attributes.
</pre>
- **statorder**: Order of the <b>!stats</b> values, you can even remove some of them if you don't want them to be displayed, adding a empty string **""** will put a blank space as if it were a offset. Available values: 
```
"statorder": [
    "blockcount",  // requires requests.blockcount
    "mncount",     // requires requests.mncount
    "supply",      // requires requests.supply
    "collateral",  // requires requests.blockcount
    "mnreward",    // requires requests.blockcount
    "powreward",   // requires requests.blockcount
    "posreward",   // requires requests.blockcount
    "locked",      // requires requests.blockcount, requests.mncount and requests.supply
    "avgmnreward", // requires requests.mncount
    "nextstage"    // requires requests.blockcount
  ]
```
- **monitor**: Use separate channel where only the bot have permission to send messages to post every X time the price stats and earnings. It follows this scheme:
```
"monitor": {
    "enabled": true,
    "channel": "531519255519428638", // channel id where the bot will post the info    
    "interval": 60                   // refresh the data every 60 seconds
}
```
- **hidenotsupported**: Hide the ticker values from exchanges APIs that doesn't support that feature instead of showing "Not Supported".
- **useraddrs:** Enable the user address commands (`!my-address-add`, `!my-address-del`, `!my-address-list`, `!my-balance`).
- **usermns:** Enable the user masternode commands (`!my-masternode-add`, `!my-masternode-del`, `!my-masternode-list`, `!my-balance`).
- **sourcecode:** You don't need to touch this, it's just in case I change the repo in the future.
- **channel:** List of the ids of the channels where the bot will listen and reply the commands (leaving the list empty will listen all the channels).
- **prefix:** The initial character for the commands.
- **coin:** The name of your coin.
- **blocktime:** Block time in seconds (used for some calculations).
- **token:** The token of your bot.

NOTE: The token on config.json is just an example, not the real one (for obvious reasons), use yours to work with your discord server.

# <a name = "currently-supported-exchanges"></a> Currently supported exchanges

- CryptoBridge
- Crex24
- CoinExchange
- Graviex
- Escodex
- Cryptopia
- Stex (24h BTC volume not 100% accurate)
- C-CEX (24h change not supported)
- HitBTC (24h change not supported)
- YoBit (24h change not supported)
- Bittrex (24h change not 100% accurate)
- SouthXchange
- Exrates
- Binance
- Bitfinex (24h BTC volume not 100% accurate)
- Coinex (24h BTC volume not 100% accurate)
- P2PB2B
- CoinsBit

NOTE: The non-supported or not 100% accurate features are due to the exchange API.

NOTE 2: Feel free to ask for adding support to a concrete exchange.

# <a name = "image-examples"></a> Image examples

![Screenshot](/img/Mining20.png) 

![Screenshot](/img/Priceticker.png) 

![Screenshot](/img/Statslevel.png) 


# <a name = "additional"></a> Additional

If you're too lazy or just not sure how to make a profile, I can make one for your cryptocurrency for a very tiny fee.

```
BTC Donations:   16wfHjVqr2z4x4o7S21tpZSZJuLRNy9RFt
ETH Donations:   0x88c55724ce65a0d502ae38af17fe776585d5f2b1
LTC Donations:   LRyK1Z4gxVAF3ZpimQHVNat9678J8TZ4xA
BOO Donations:   BBC6H7UKy8TyKo2JZS2aRtoSaUs18rs8AW
ZEON Donations:  ZJzz4YmRjBhpYizhKphY9Z1DtCn7ZcwdUH
```

A big thanks to https://github.com/discordjs/discord.js for this amazing library for interfacing with the discord API.
# ZEON_Info_Bot
