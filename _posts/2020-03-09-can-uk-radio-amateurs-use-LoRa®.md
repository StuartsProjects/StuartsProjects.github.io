---
layout: post
title: "Can UK Radio Amateurs use LoRa® ?"
date: "2020-03-09"
---

I gave a short talk recently on the use of 2.4Ghz LoRa®.  One item that was raised during the talk was the legality of Amateur license holders using LoRa®. It has been suggested in the past that LoRa® is outside the scope of an Amateur radio license, because it is proprietary and 'encrypted'.  I could not recall the relevant part of the Amateur license at the time, so I decided to check what the UK Amateur licence conditions actually are.

There does seem to be a general assumption that 'encrypted' messages cannot be sent under the Amateur radio license. But that is not what the UK Amateur license Terms, conditions and limitations actually appears to say.

11(2)(b) of the UK Amateur license Terms, conditions and limitations says;

"Messages sent from the station shall not be encrypted for the purposes of rendering the Message unintelligible to other radio spectrum users"

Clearly there is a reason here behind the specific use of the word 'purposes'. If the objective of the condition was to make clear that Amateurs could not use encryption in messages (in all circumstances) then you would expect the following wording or similar;

"Messages sent from the station shall not be encrypted such that the Message is unintelligible to other radio spectrum users"

So the intention behind the use of 'purposes' appears to be to permit Amateurs to use 'encrypted' messages as long as the purpose behind (using the encryption) is not to render the message unintelligible to other radio spectrum users. So if the purpose of using LoRa® is for long range or low power uses, then even if LoRa® were to be considred encrypted it appears to be permitted.

If the Amateur is using 'encryption' then if the messages can be read by other users of the radio spectrum by for instance publishing the decoding key, that would appear to be permitted also.

There does seem to be an assumption that LoRa® by its nature is encrypted, this seems to be a false to me. LoRa® is encoded for sure, like a lot of radio protocols are, but the purpose behind the encoding is purely functional, its not to obscure or restrict access to or encrypt the data within. Its also very difficult to see why an encoding method that can be easily read either by readily available LoRa® devices or open source decoders (such as SDRs) can be considered 'encrypted'.

Maybe the Amateur radio license conditions have not kept up with modern communication techniques, but then it is for the regulator to amend them.

Note that these are my own views and maybe Ofcom (the UK regulator) takes a different view.
