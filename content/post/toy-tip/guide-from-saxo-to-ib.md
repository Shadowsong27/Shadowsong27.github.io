---
title: "A guide to transfer securities from Saxo To Interactive Brokers"
thumbnailImagePosition: "left"
thumbnailImage: https://download.logo.wine/logo/Interactive_Brokers/Interactive_Brokers-Logo.wine.png
coverImage: https://mms.businesswire.com/media/20210128005689/en/694824/23/IB_logo_stacked_-_Copy.jpg
metaAlignment: center
coverMeta: out
date: 2021-08-31
categories:
- toy-tip
---

I have been using SAXO for three years and Interactive Brokers for more than one year, and recently I have decided to switch from SAXO to IB completely, but I could not find a proper instruction on the Internet. 
<!--more-->

Note: I am in Singapore and I believe my account was under SAXO Singapore, but I think it does not really matter when it comes to security transfer. However, obviously you can only transfer assets that are available in IB. e.g., you can't transfer SGX stocks into IB.

Disclaimer: This article is just a recollection of me doing something a few days ago, please always check for the detail input of the table. Use at Your Own Risk.

This article is going to cover all the steps involved and address some of the confusions I have during the process of transferring securities, but before that ...

### Major reasons for switching

1. SAXO has a way higher minimum commission per trade - which is pretty costly if you have developed the bad habits of regular trading
2. SAXO forced you to convert to and from SGD to USD when trading US stocks, your trading routines are essentially deprived of control over the currency conversion timing
3. SAXO has a pretty bad conversion rate compared with other platforms

### Major advantages for switching:

1. You avoid paying for the closing cost (conversion cost plus the commission) on SAXO especially when you do not wish completely exit the US market
2. You automatically have a multi-currency account
3. You can easily carry out margin trading (don't do it if you do not know what that is)

### Major disadvantages for switching

1. Bad UI (both Mobile and Desktop)
2. Bad server connection on peak period

# How

### IB

- Just like the funding process in IB, you will need to initiate a transfer request, search it in the search bar, it could also be found in some of the dropdown menu, but I realise there is an UI bug which might somehow make the dropdown menu disappeared, so just stick to the search bar, click `Transfer Positions` 

![alt](/img/ib-search-bar.png)

- Read / Google all available methods for transfer `IN` to IB, that is you need to select the `Incoming` tab. I used the `Basic FOP Transfer` type, because SAXO can only be found there.
2. Filling the form accordingly, the confusing part is the contact info. so I am going to write it down in detail in the `Note` column - I think this is going to work since my transfer request is completed.

![alt](/img/ib-table.png)

- So once the form is filled, proceed to add in the incoming assets and you are done with the work on IB side
- I believe, I can't remember clearly now but I think IB will give you a message containing the information on their side such as the contact email and name. This will take a while, like one or two days, so I think they are handling the securities transfer manually.

### Saxo

- Once you have received the information from IB, follow this partially useful and outdated documentation provided by Saxo to find the transfer security window [https://www.help.saxo/hc/en-sg/articles/360001270546-How-do-I-request-a-securities-transfer-](https://www.help.saxo/hc/en-sg/articles/360001270546-How-do-I-request-a-securities-transfer-) It's now under the `hamburger button` → `portfolio transfer`
- The form detail

![alt](/img/saxo-table.png)

- Assuming you are going to close your SAXO account, you should be prepared to have at least 160 EUR cash in your bank - that's the total amount of cost this process will incur, once this is done you can just sit back and relax

# Final note:

1. Most of the fields in both forms are poorly designed, key information is hard to retrieved or located online, this is particularly triggering for users who are trying to move a huge amount of money across bank - it should be designed with more accuracy and precision
2. That being said, I found the entire process is most likely done manually by both parties, so you should be fine even if you made some typos or entered a wrong value (they will probably email you and clarify with you)
3. The original open price on SAXO will NOT be transferred into IB, so that kinda messed up your performance calculation a bit. You will have to do it yourself
4. Happy trading everyone