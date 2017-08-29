[<img align="left" src="https://user-images.githubusercontent.com/31250469/29787597-18d2a772-8c62-11e7-92df-e40b94050b0a.jpg" width="1350">](https://mvp.blindcroupier.io/)
`If you don't have ETH ON KOVAN TESTNET - Ask in our Telegram/WeChat channel`

# Resources Blind Croupier
[**English Documentation**](https://github.com/BlindCroupier/Documentation/tree/master/English%20Documentation) | [**中文文件**](https://github.com/BlindCroupier/Documentation/tree/master/Chinese%20Documentation)| [**Русская Документация**](https://github.com/BlindCroupier/Documentation/tree/master/Russian%20Documentation)

<img align="left" src="https://user-images.githubusercontent.com/31250469/29752563-2ad17fa4-8b93-11e7-9c40-64e62feb64eb.jpg" width="25">[**Telegram Channel**](https://t.me/blindcroupier)

<img align="left" src="https://user-images.githubusercontent.com/31250469/29752596-b779b930-8b93-11e7-9e3b-7d3e1ec9ae28.jpg" width="25">**WeChat:** blcico

<img align="left" src="https://user-images.githubusercontent.com/31250469/29752590-9785e734-8b93-11e7-8eb3-e5de22b70f97.jpg" width="25">[**Twitter:**](https://twitter.com/BlindCroupier)

* [**WebSite**](https://blindcroupier.io/index-ru.html)

* [**White Paper English**](https://github.com/BlindCroupier/Documentation/blob/master/English%20Documentation/WhitePaper.md#blindcroupierio-white-paper)

* [**Token WIN White Paper**](https://github.com/BlindCroupier/Documentation/blob/master/English%20Documentation/Token%20WIN%20White%20Paper.md#content)

* [**Token Distribution Smart Contract**](https://etherscan.io/address/0xaa3e80a42e7bac1072bee7ee5ac72123969b8400)

* [**Road Map**](https://github.com/BlindCroupier/Documentation/blob/master/English%20Documentation/Token%20WIN%20White%20Paper.md#roadmap)

# Game Fairness Check

<img align="left" src="https://user-images.githubusercontent.com/31250469/29790352-230912f8-8c6c-11e7-8f38-ee19836f843a.gif" width="1200">

The easies way to check the game fairness is to request hash list from Blind Croupier server. Please note that all the information is requested from Ethereum blockchain.

Let us create a link: 

`https://mvp.blindcroupier.io/api/v1/transactions?address=**ADDRESS**&gameId=**GAMEID**`

You need to put your Ethereum wallet address instead of `**ADDRESS**` and the actual id of the game you played instead of `**GAMEID**`.  ``https://mvp.blindcroupier.io/api/v1/transactions?address=0x8D3B8a80D85Dd5877Dac75D4cc70d8CfAB56f10D&gameId=93``

After you open the link, the server responds with the transaction list of the game: bet, seeds, initial hand, replacement order and the final hand:


```{
    "BetSubmitted": {
        "txHash": "0x36ded355dd1fea92de72adc627900d1fcc6597d61556b6ca91b929cc3ad7700f",
        "bet": 1,
        "level": 1,
        "betPayment": "0.001 eth (1 chips)"
    },
    "CroupierSeedSubmitted: 0": {
        "txHash": "0x71288219fa03634ae430585ae15187dffb33cd64c08f4d28133f3cd56c84b27f",
        "seed": "354157056"
    },
    "CroupierSeedSubmitted: 1": {
        "txHash": "0x48182961725511449d2d0d18a25070aedf26a35d811464fe62ea9ce2d5940890",
        "seed": "3391019893"
    },
    "PlayerSeedSubmitted: 0": {
        "txHash": "0x947571c451780e90351a1227124157aacdd137605f869b50655f4b8762cf50fb",
        "seed": "461411042"
    },
    "PlayerSeedSubmitted: 1": {
        "txHash": "0x5baecfd309f4e8d331e8c688656708ea781dc077a825aa69ecdb7511a23d61b4",
        "seed": "579925600"
    },
    "InitialHand": "3♧, 10♤, 8♡, K♢, 4♢",
    "ReplacementOrderSubmitted": {
        "txHash": "0xb2c08afba7417cb797a0699a08087fec9997c71b9e41bfe86d84366daa6dc983",
        "replacementOrder": [
            "1",
            "1",
            "1",
            "0",
            "1"
        ]
    },
    "GameFinishedEvent": {
        "txHash": "0xd11fe761a2f8c4c1573955b882335ae9e7e29d55fcf128949e2be7a0004495a3",
        "winCoef": 0,
        "winPayment": "0 eth (0 chips)"
    },
    "FinalHand": "2♧, 9♡, 7♤, K♢, 6♧"
```

As you can see, it's really easy to retrieve the game from blockchain and check its fairness. We are working on the improved fairness check UI.

# Deep Transaction Study

Deep study requires some advanced tech skills. We are not going to describe basic cryptography or details on some calculations here. You can find extensive explanation in the technical White Paper or ask in the Blind Croupier Slack or Telegram. 

In this guide we are going to briefly describe transactions. To lookup up the method called and its parameters we will use the Etherscan link:

`http://kovan.etherscan.io/tx/**TXHASH**`

where `**TXHASH**` is the transaction hash (unique identifier) contained in the server answer (see above, methods are  (`submitBet`, `submitCrouiperSeedAsSignature`, `submitReplacementOrder` и т.д. )).

After opening the link we are mostly interested in the `Input Data` field, which contains function name and arguments list. We will study all the methods use the same algorithm.

## Step One: Bet Made

To verify bet information, we will need to use the corresponding `txHash` for the `submitBet` method (or `BetSubmitted` event).

`Input Data` contains function parameters:

```
MethodID: 0x021525af
[0]:000000000000000000000000f0005d5b6bf7f42a1e92d0cbbbc75f9773d4158d
[1]:0000000000000000000000008d3b8a80d85dd5877dac75d4cc70d8cfab56f10d
[2]:0000000000000000000000000000000000000000000000000000000000000001
[3]:0000000000000000000000000000000000000000000000000000000000000001
[4]:000000000000000000000000000000000000000000000000000000000000001c
[5]:f73a2163cf4cca8b53b32a1ab675c466b6331e9bece4976cccb8faf0b1ae849d
[6]:1a6e75428750cfe298d42509ea42af7cab09083561912aaeef93c05450d3af2f
```

Parameter `[0]` is the Croupier's address:

```
[0]:000000000000000000000000f0005d5b6bf7f42a1e92d0cbbbc75f9773d4158d == 
                              0x5d5b6bf7f42a1e92d0cbbbc75f9773d4158d
```

Parameter `[1]` is the Player's address:

```
0000000000000000000000008d3b8a80d85dd5877dac75d4cc70d8cfab56f10d == 
                      0x8d3b8a80d85dd5877dac75d4cc70d8cfab56f10d
```

Parameter `[2]` is the bet made:

```
0000000000000000000000000000000000000000000000000000000000000001 === 1
```

And parameter `[3]` is the level:

```
0000000000000000000000000000000000000000000000000000000000000001 === 1
```

## Step Two: Random Seeds

Like we did in the Step One, use the corresponding `txHash` for the method `submitCrouiperSeedAsSignature` (or `CroupierSeedSubmitted` event).

`https://kovan.etherscan.io/tx/0x71288219fa03634ae430585ae15187dffb33cd64c08f4d28133f3cd56c84b27f`

``InputData``:

```
MethodID: 0xf21950bd
[0]:000000000000000000000000f0005d5b6bf7f42a1e92d0cbbbc75f9773d4158d
[1]:0000000000000000000000008d3b8a80d85dd5877dac75d4cc70d8cfab56f10d
[2]:000000000000000000000000000000000000000000000000000000000000001b
[3]:f5dd9d20b5cadfbe488cefdb6f89daeb51b575e9a211d24ab8ea8ef8151c0200
[4]:30db06242f67e7e364002c45b73f382cad42c4bf658578d786b5ad05b8fc43af
[5]:0000000000000000000000000000000000000000000000000000000000000000
```

We are interested in parameter `[3]` (`r` signature component): 

```
r = f5dd9d20b5cadfbe488cefdb6f89daeb51b575e9a211d24ab8ea8ef8151c0200
```

To calculate the random seed we need to take last **4 bytes** of this number (i.e. **last 8 hex digits**):

```
seed = f5dd9d20b5cadfbe488cefdb6f89daeb51b575e9a211d24ab8ea8ef8151c0200 ^ ffffff == 151c0200
```

Converting the number to the decimal representation (e.g. [here](http://www.binaryhexconverter.com/hex-to-decimal-converter))

```
151c0200 = 354157056
```

which equals `seed` field in the server provided information

```
"seed": "354157056"
```

Other seeds (Croupier 1, Player 0, Player 1) are calculated the same way.


## Step Three: Replacement Order

Method name: `submitReplacementOrder` (event name: `ReplacementOrderSubmitted`).

Transaction: 
https://kovan.etherscan.io/tx/0xb2c08afba7417cb797a0699a08087fec9997c71b9e41bfe86d84366daa6dc983


``InputData``:

```
MethodID: 0xd41abbdc
[0]:000000000000000000000000f0005d5b6bf7f42a1e92d0cbbbc75f9773d4158d
[1]:0000000000000000000000008d3b8a80d85dd5877dac75d4cc70d8cfab56f10d
[2]:0000000000000000000000000000000000000000000000000000000000000001
[3]:0000000000000000000000000000000000000000000000000000000000000001
[4]:0000000000000000000000000000000000000000000000000000000000000001
[5]:0000000000000000000000000000000000000000000000000000000000000000
[6]:0000000000000000000000000000000000000000000000000000000000000001
[7]:000000000000000000000000000000000000000000000000000000000000001b
[8]:1abe8b7df52caad86b36756b862d6bd6bb32ab076d3646b728a23e20280bada8
[9]:6cdcdac6165da4116609551598ab90e121c832dd091d1adc16b4e8031b572d0b
```

Parameters `[0]` and `[1]` are the Croupier's and Player's addresses.

Parameters `2` - `6` is the replacement order (5 cards from left to right). `0` means hold, `1` means replace the card.

## Step Five: Submit Order And Game Result

Let's open the last transaction logs  (`GameFinishedEvent`): 

https://kovan.etherscan.io/tx/0xd11fe761a2f8c4c1573955b882335ae9e7e29d55fcf128949e2be7a0004495a3#eventlog

There are two events in the logs:

```
[0] 000000000000000000000000f0005d5b6bf7f42a1e92d0cbbbc75f9773d4158d
[1] 0000000000000000000000008d3b8a80d85dd5877dac75d4cc70d8cfab56f10d
[2]  0000000000000000000000000000000000000000000000000000000000000000
 		
		[0] 0x2e26679f09e74f31e637088f9820b9f39ae1da7796c9ab18fb06227464eeff59
		[1] 0x000000000000000000000000f0005d5b6bf7f42a1e92d0cbbbc75f9773d4158d
		[2] 0x0000000000000000000000008d3b8a80d85dd5877dac75d4cc70d8cfab56f10d
		[3] 0x000000000000000000000000000000000000000000000000000000000000005d
 ```

The first one is the MoneyMoved event. Fields from top to bottom:

Croupier's address: 
```
[0] 000000000000000000000000f0005d5b6bf7f42a1e92d0cbbbc75f9773d4158d
```

Player's address: 
```
[1] 0000000000000000000000008d3b8a80d85dd5877dac75d4cc70d8cfab56f10d
```

Wei paid (using the same [hex to decimal converter](http://www.binaryhexconverter.com/hex-to-decimal-converter)):

```
[2]  0000000000000000000000000000000000000000000000000000000000000000
0 Wei = 
0.0 ETH
```

## Checking The Hand Was Generated Properly 

Can we check Croupier and the Bank generated the Hand right?

We have 4 random seeds:

```
354157056 (Крупье 0)
461411042 (Игрок 0)
3391019893 (Крупье 1)
579925600 (Игрок 1)
```

First, let's mix seeds in pairs (Player 0 + Croupier 0, Player 1 + Crouiper 1, using XOR, (^). We can use for example: http://xor.pw/ (use Base 10 for input **and** for ouput). 

The result: 

```
245141730
3901631765
```

The first seed is used for initial deck shuffling. After that 5 cards are drawn. The rest of the deck is shuffled again using the second seed and the amount of cards required for the replacement is drawn.

To check the final Player's hand given two seeds and the replacement order, we need to call `generateHand` function from the Bank, using seeds and the replacement order as the parameters:

```
generateHand([seed0, seed1], replacementOrder)
```

The result is published in the last line of the initial server reply:

```
"FinalHand": "2♧, 9♡, 7♤, K♢, 6♧"
```

We are working on the tool which can independently do the calculations and prove that everything is calculated correctly.

#### Thank you for attention!
<img align="left" src="https://user-images.githubusercontent.com/31250469/29752217-87d61d60-8b8c-11e7-92fc-5ddf220c5c2b.jpg" width="1200">

#### Best Regards,

#### Team Blind Croupier

[<img align="left" src="https://user-images.githubusercontent.com/31250469/29752563-2ad17fa4-8b93-11e7-9c40-64e62feb64eb.jpg" width="25">](https://t.me/blindcroupier) [<img align="left" src="https://user-images.githubusercontent.com/31250469/29752590-9785e734-8b93-11e7-8eb3-e5de22b70f97.jpg" width="25">](https://twitter.com/BlindCroupier)
