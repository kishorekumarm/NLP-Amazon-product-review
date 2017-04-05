
# Natural Language Processing - Amazon product reviews

## Objective

To predict the positive and negative scores for any random 10 comments web scraped for a product from Amazon website

## Steps Involved

<b>Step-1:</b> Web scrap data using rvest package <br />
<b>Step-2:</b> Tokenization to sentence/word using tm package <br />
<b>Step-3:</b> Feed tokens to nltk package and use naive bayesian classifier to assess scores for positivity and negativity <br /> <br />
<i><u><b>Note:</b></u></i> nltk has the corpus library to assess the positive and negative score

### 1. Web scrap data using rvest package 

<img src='webscrap.png'>


```python
import pandas as pd
pd.set_option('display.max_colwidth', -1)
df=pd.read_csv("review.csv")
df2=df[['Type']]
df2
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>My brother Robert who has been bed ridden and paralyzed  with Multiple Sclerosis from his neck down for more than 30 years now has a new  friend named Alexa! He was in tears with happiness when Alexa played 70's music, played Jeopardy, answered all his questions and wakes him up every morning. Thank you Amazon for giving my brother a new bedside companion.Happy HolidaysRoy</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Having worked in the electronics retail industry for years now, I've seen scores of Smart devices come and go.  Until now, nobody quite got it right.  In the Echo Dot, Amazon has created a near perfect blend of hardware and software.  I've seen plenty of the former, but truly seamless multi platform software has eluded everyone but Amazon.  We're talking major players like Samsung and Google who have been at it for much longer than Amazon.  The main problem is that excellent products like the Samsung Smart Things hub, which do a fantastic job of unifying a slew of different connected devices from different companies (Nest, Honeywell, Phillips, and so on), still lacked the web connectivity and entertainment support I wanted, so I'd still end up needing my tablet or phone.  Thanks to fantastic third party support, the Dot has no problem controlling all of my smart stuff while allowing me to listen to music, order food, check the weather, listen to the radio, set alarms and timers, all of which is easily accomplished through simple voice commands.  To me, this is the exceedingly rare product that I didn't know I needed, and now can't live without.  Similar to the smartphone and tablet I use every day that didn't exist just a few short years ago.If you're even a little bit curious you owe it to yourself to give the dot a try.  Add a good speaker and enjoy just how simple a connected life can be!Update:  After a bit more time with the dot, or maybe I should say dots since I went out and bought another one for my living room, I've come up with a few tips.1.  Use the best speakers you can with it.  I found that while Bluetooth was convenient I got much better sound out of my JBL duet computer speakers.2.  Take the time to voice train Alexa at least once.  It's kinda tedious but really improves the accuracy.  I've now gone through three trainings with each dot, the phrasing gets more intricate with each, and it really is amazing how much of an improvement it makes.  Kinda hard to quantify, but I'd guess Alexa is at least twice as likely to understand long, complex phrases and has also gained noticeable accuracy when ambient noise I'd present.3.  If a phrase doesn't yield the results you're looking for, reword it and try again.  For instance, "Alexa, lower the temperature to 75 degrees" got no result, so I tried "Alexa, Honeywell Thermostat, 75 degrees" and she picked it up perfectly.4.  Take the time to look through all the skills.  There's a lot of helpful and just plain fun stuff in there, from strange facts to a calculator and everything in between, that really helps to enhance the experience.5.  I'd never really used my prime music prior to setting up my dots.  Now I can't live without it!  I can say basically whatever I want and I get a result.  My favs so far:  "Alexa, play 90s music", "Alexa, play indie music", and "Alexa, play thunderstorm sounds".  The last one I ask to repeat and it plays all night.  Really a great "freebie" if you're a prime member.6.  I was a bit worried initially that Alexa might be triggered accidentally by ambient TV or general household noise, so I'm really impressed that it's only happened twice so far.  Both times in my living room when I was watching TV at high volume.  If it's a concern, the mic can be temporarily disabled, so the dot won't trigger and listen accidentally.7.  I've had no problem pairing the dot to a variety of devices including:  two different Bluetooth speakers, my Galaxy S7 edge, and pioneer receiver.  I need to look into it further, but each time I paired my phone the Bluetooth connection to the speaker was lost, so I ended up having to listen to the built in speaker.  Definitely not ideal for music, but no big deal if you're using wired speakers.  Plus, most Bluetooth sets offer an auxiliary input for wired listening.8.  It's fun to ask Alexa general questions to see if she's capable of finding the answers.  So far I've gotten accurate responses to "Alexa, what's the definition of", "Alexa, how far away is", "Alexa, Wikipedia" (just about anything you can think of and she'll tell you more if you ask "Alexa, tell me more").  If you have the time, ask her a set of questions and you'll quickly get used to her nuances.9.  Even though the microphones are extremely sensitive and quite accurate, I've found that the Dot works best when placed on a surface that's close to the level of the person speaking to it.  Generally speaking, three to five feet off the ground.  Alexa had some trouble hearing my requests when the dot was placed above or behind me.  If you'd like to place the dot higher, it works much better when flush with the wall, instead of sitting on a shelf etc.  I tried both setups and found with a couple nails set apart to make a cradle facing the dot out towards the room works best for me (sorry for the run on sentence lol).Suggestions for Improvement:1.  Unlike the upcoming Google Home BT speaker, the Dot and other Alexa devices are unable to answer general web queries.  They do a decent job of answering factual questions like "How far away is the sun" but I'd really like too see Amazon add a "search" function.2.  Not Amazon's fault, but several of the news briefing skills update infrequently and volume levels vary enough that I'd find myself constantly changing the volume level to match.3.  I enabled briefings from several outlets like NPR, BBC, AP, and so on.  I'd suggest you pick one or two and stick with them, otherwise you'll here the same news over and over.  I ended up going with BBC and AP briefings.</td>
    </tr>
    <tr>
      <th>2</th>
      <td>So I don't normally write reviews but I just has to this time. Recently, I lost my cat best friend to cancer 2 days after Christmas. It was the worst day of my life. Imagine losing your friend that's been with you through every breakup, every apartment, nearly being homeless and more. When he passed I felt such an emptiness in my home.A few days ago I was searching for something to put my mind at ease and saw the advertisement for the Amazon Echo on the Amazon app. I remember watching a Mr. Robot episode where some FBI agent who suffers from insomnia was using Alexa to keep her entertained.So I started researching YouTube videos and reviews and took a chance and ordered it. It arrived today and the setup was super easy. I immediately started asking it questions and had it play music and play the news. Then I discovered some zen type apps that go with the echo and I completely fell in love with it. It's almost like having a real personal assistant who lifts up your spirits when your down. It suggested new music which I actually liked and the built in speaker isn't bad at all for the price.The Amazon echo did not replace my beautiful cat kouga but it did bring stop the empty feeling around the house for now. I added a picture of my cat instead of the echo since everyone already knows what the echo looks like.I would totally recommend buying this product. I don't see any negatives other than the fact you can't really ask it followup questions like google assistant but I'm sure they'll make it happen.Thanks for letting me rant</td>
    </tr>
    <tr>
      <th>3</th>
      <td>There's already a ton reviews for the Amazon Echo, but I just wanted say how happy we are with this product.I initially thought that I should get the regular larger speaker Amazon Echo for our living room/kitchen for the first floor of our 2- level condo and because it's a larger room than our bedrooms upstairs; however, when the Echo Dot went on sale for $40, I thought it was a really good value over the more expensive big brother and too good to pass up. What I found was that the Echo Dot was perfectly sufficient for the first floor. It's hard to know if the sound will be loud enough or if Alexa could hear from all parts of the room just by seeing it at the retail store, but Alexa (Echo Dot) had no problems hearing and responding to our commands. We typically set the volume setting to volume level 7 and music is loud enough for us to hear everywhere. It helps to have it centrally located in the room, and I am no farther than 20 feet away from the device speaking at relatively normal voice volume.My wife, an initial skeptic, is really surprised by how smart the Echo Dot is, and my kid loves it as well. The only thing that is CON for me is that the Amazon Echo "skills" that can be learned still feels like in the infant stages and many of the "skills" not as polished. Certainly there are several companies that have helped designed some really good skills like Capital One, Fitbit, and Domino's, but there's still not a lot to choose from. I hope that more and more companies will jump on this quickly.Considering everything, for $40/$50 bucks this is a great value and easy way to get yourself introduced to home automation and voice recognition technology, and overall, I would highly recommend this product.The product comes in a small package, and everything that comes in the box can be seen in pictures attached.</td>
    </tr>
    <tr>
      <th>4</th>
      <td>This was a surprise Christmas gift and it turned out to be a very nice surprise! We had thought about purchasing it so the kids could ask it questions, but never seriously considered it. Then we received it gift.  Turns out it does a lot (a LOT) more than answer questions. The answering questions part is great, sometimes you have to rephrase it so Alexa understands what you are saying, but she does a good job answering 90% of the time.She easily recognizes our voices. Even my 4 year old can trigger it most of the time. When there is a lot of noise in the background she can add a word or two, but she seems to be learning as you go and we have not tried the voice training with her yet (there are 6 of us in the house so a lot of background noise). The key seems to be learning how to say what you are asking. If you want a song then use the word "play" first, if you have a question then use the question word first, etc.She does so much more than just answer questions. I love the news updates. I don't need to check my phone for top stories only to get distracted by the random junk that pops up with the news. You can choose the news channel (I saw BBC, NPR, CNN, Fox, and more offered). At the end of the top stories (goes about 5 minutes) she gives a weather update and forecast for your area. The update changes every hour so if you feel the need to get more news than go for it. Ask her if it is going to rain in the next hour, what the traffic is like for your commute, and more.Playing whatever song/artist/station you want is a great way to listen to music. Want to hear a song - just ask.The timer is useful. I used to grab my phone when I needed to set a timer, now I just ask Alexa. When the kids ask for tablet time or they need to do homework/reading time I can just ask Alexa to set the timer for me. This saves me going to my own tablet or phone (which I usually forget to do) and means the kids really get 15 minutes tablet time when I say they can rather than 45 minutes when I realize I forgot to set the timer.She has so many skills that we have yet to try, but the ones we have are fun additions. There is a great game, magic door, that works like a choose your own adventure (complete with sound effects). The kids are currently in the middle of trying to find a wizard. You can pause the game and re-enter at any time. Alexa guesses what animal (or thing) you are thinking when you play 20 questions or the animal game. There is a bedtime story skill where she tells a random bedtime story using your child's name. After telling my 4 year old a princess story last night (using her name) she said "now it is time for bed and no you can't have another glass of water". My daughter thought it was hilarious. It would be great for an elderly or bed-ridden person looking for something to talk to. It will also read audiobooks.The kids love the joke feature (Alexa tell me a joke) and my husband likes trying to trip her up with random odd questions (What is the meaning of life, who's your daddy, who let the dogs out, etc). Ask her what her resolutions are or the highlights of the year. It is good for a laugh.I haven't hooked her up to our thermostat yet and we don't have any smart lights/locks so I can't comment on that feature. I am seriously considering moving all my calendar stuff to google so she can plan my day for me. We put a code on ordering so the kids don't accidentally buy something (you can turn the feature off completely if you would rather), but you can still add things to lists when you think about them (then access that list in your amazon account).Overall, great! I would even consider getting a few more (you could use it as an alarm clock plus extra features in a bedroom, to control things in your living room, this one is in our kitchen for lists/timers/etc).</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Amazon Echo is a great device (I was one of the first to have one and waiting for months for my first Echo to ship as Amazon vastly underestimated the demand for a device like this) and the Echo Dot (2nd generation) is fantastic in its size and capability (I bought a six pack to get one free). There is no volume ring on the 2nd gen Dot like there is on the original Echo and the 1st gen Echo Dot but it's no big deal as there are volume buttons now and the cost of the volume ring wasn't worth it if you ask me (it was nice, but for most people not worth the money). Most of the time you'll want to adjust the volume by voice anyway (like "Alexa volume 5"). The 2nd gen Echo Dot is also available in white but I'm sticking with black. UPDATE: I actually prefer the cheaper volume buttons on the Dot 2 to the volume ring now.The fact that you can just talk from across the room and it can hear you (most of the time) really sets this apart. If you think you already have an Echo because you have Siri, Cortana, or Google, then know that it's just not the same.Things I do: set timers, set alarms, get weather info, ask basic questions, listen to music (like Prime Music, Prime Stations, songs I bought, etc), listen to radio, ask for jokes, turn things on and off, and I'm sure I'm forgetting some things. The Echo really excels at playing music. I love it than I can ask for a song I want to hear and it will play it (most of the time anyway). At night I often listen to a local radio station and I just say "Alexa stop" without having to get out of bed when I want to stop listening.When I listen to Prime Station I can tell it "thumbs up" if I like the song or "thumbs down" if I don't... and when I listen to the station again it won't play the songs that I've thumbed down.Using the built-in speaker, the sound quality is decent for voice. For music you'll want to connect it to a speaker system because the Dot's small internal speaker is weak. I do wish I could AirPlay to a device from the Echo when I'm playing music and want the best sound quality, however Bluetooth is pretty impressive when it uses the near-CD quality codecs.Positives:* The new feature that Amazon added (ESP - Echo Spatial Perception) so that the nearest Echo responds is fantastic. I was also very happy to hear that the first generation Echoes will also support this in the next firmware update (I think devices are updating now). From my research, if the Echo app shows that you have a device software version greater than 4008 then you should have the update with ESP support. Unfortunately there does seem to be a minor downside: the Echo's response can sometimes be slightly delayed while multiple Echoes figure out which Echo is closest and should respond.* It seems to hear better when playing music than the first gen. It was annoying when you would try to talk to it while it was playing but it couldn't hear you. The new Dot hears better during music playback but it's still not "Star Trek quality" hearing; sometimes you may still need to raise your voice.* It's relatively cheap and well worth the asking price.* It has a 3.5mm stereo audio jack, which I use with two of my Dots to connect to the line-in on one of my computers and another to the line-in on an old compact stereo/CD player. Sound quality should be good enough for anyone except audiophiles.* Some people complain of connection issues, but all of my Echoes stay connected without issue to my�Asus RT-AC68U�router.* If you want to 'Wikipedia' something then you can say 'Alexa, Wikipedia &lt;subject&gt;'. She'll read a little from Wikipedia. If you want her to read more after she stops then just say 'Alexa, tell me more'. This can be useful sometimes.Negatives (mostly minor but take note):* Warranty is only 90 days. I think it should be 1 year. Of course they will sell you a SquareTrade 1, 2, or 3 year warranty for $5/year if you want. Perhaps another cost-cutting move.* The shiny (glossy) black surface can be a fingerprint magnet but you probably won't be touching it often. The previous generation wasn't glossy.* I had trouble configured my 2nd Echo Dot (the first one went fine). The Alexa app (on Android) gave me the cryptic message "Authorization expired. Please reconnect your phone to the internet, log out from the app and retry". Got it several times. I resolved it by signing out of the Alexa app and then signing back in, having to re-enter my email and password.... annoying... and I wonder why I didn't have this issue setting up my first Echo Dot just minutes prior???? Oh well. UPDATE: This just happened again on another day when setting up two more Echo Dots. This first Echo Dot setup went fine, but on the second one I got the "Authorization expired." message again. Beware of this issue if setting up more than one Echo Dot.* While trying out Amazon Music Unlimited with the Echo I immediately ran into the issue of the Echo playing the explicit version of songs instead of the clean version. There should be an option to never play explicit songs and to play the clean version instead (some people have children). The workaround until Amazon addresses this problem is to add the clean song to your music library and then say "Alexa play &lt;song&gt; from my library".* This doesn't affect me, but the Echo does not support enterprise networks that require centralized network authentication with a login. Some might want to know this (like teachers who might want to use it in a school that uses this type of network authentication).* The Android Amazon Alexa app can be slow and frustrating sometimes. For example, when launching it sometimes just sits there on the intro screen and spins forever until you shut down the app and restart it again.* Doesn't hear well in a noisy environment, or when other people (including on TV) are talking at the same time.Home Automation:* I use several�Belkin WeMo�switches to control some lights and fans. They work pretty well although not always perfectly. Fortunately they work better now after all the firmware updates.* I can control my Honeywell WiFi thermostats using the "Honeywell Total Connect Comfort" skill. I have two downstairs for 2 zones. I have one�Honeywell RTH9580WF1005/W1�and one�Honeywell RTH6580WF.* I recently set it up to turn on and off certain activities with a�Logitech Smart Hub. For example, I can say "Alexa, turn on TiVo" and it will turn on the TV and my receiver and set the TV and receiver inputs correctly. I can then use the remote which is synced... and turn it all off with "Alexa turn off TiVo". There were some annoyances getting it all setup but I think it's pretty "cool" even though you can only start and stop activities with Alexa so you still need the remote to do other things (change channel, adjust volume, mute, pause, etc).* I think I've only really scratched the surface of what is possible with home automation. I have not tried IFTTT ("if this then that") recipes yet but I hear it is quite powerful and hope to try it out when I have more time to "play".Wishes:* High-quality digital audio output that is better than Bluetooth. If it had this then you could really get high quality audio output from it, but most people probably don't really care that much as analog out is "good enough".* Smarter. After 2 years I wish the Echo was smarter. Yes, it has definitely improved over time but Google can still answer more questions than Alexa. It's going to be hard to beat Google in answering general questions.* Weather alerts. I suggested this a long time ago and they still haven't added it... perhaps due to legal reasons? If would be great if it would sound some sort of alarm or notification for severe weather.* Built-in support for notifying others that you might need help. This would be good for the elderly. There is a "Ask My Buddy" skill however, but the verbiage is a little awkward ("Alexa, Ask My Buddy to Send Help") and it's free so if the developer decides to stop support then that might leave a lot of people without service at an inopportune time.Compared to Google Home (which I just bought):* Setup on Google Home was super-simple, easier than Echo (the Android app worked really well and setup seemed streamlined)* Google Home is smarter for general questions... significantly smarter. I asked Google "How long does it take to get to the Honda dealer in Lewisville" and it told me about 26 minutes in current traffic conditions. Awesome. The Echo totally failed here.* No AUX audio output like on Echo Dot* I prefer saying "Alexa" to "OK Google" as the wake word (UPDATE: "Hey Google" works now too!)* I currently prefer Echo for music and smart home control but I consider Google Home a serious threat to Echo because it is more "intelligent" than EchoOther:* It doesn't come with a 3.5mm audio cable but I can understand why as many people would probably not use it and Amazon wants to get the cost as low as possible so this isn't a big deal.* The USB power cable is about 5 feet.* The 1st gen Echo Dot was almost double the price. I feel a little disappointed that they now have a 2nd gen that is overall better for much less money... and that it came out very shortly after the 1st gen Echo Dots.* You don't need any other Echo units to use the Dot... some people might think you do but this is just like the "big Echo", only without the "big" sound. Instead a little speaker is included.* It's USB powered and comes with a USB power adapter and USB cable. NOTE: If you use a USB power connection other than from the included power adapter then there could be power and/or sound quality issues. I had some sound quality issues (background noises) and eliminated them by switching to the included USB power adapter.* About 10 or 15 minutes after setting it up, it said something like "your Echo received an important update and must restart". So if you are having issues with it then you may want to give it awhile so it can update if needed.* For better understanding of what you say, go to the Alexa app and Settings-&gt;Voice Training.* I bought some of these powered bookshelf speakers, hooked them to the Echo Dot, and have been impressed with what you get for the money:�Edifier R1280T Powered Bookshelf SpeakersWhile not perfect, I really like the Amazon Echo. I think the technology and potential is still in its infancy but the Echo is already really useful and excels at home automation, playing music, podcasts, and radio. Highly recommended, especially for the price. UPDATE: I'm reducing my rating from 4.5 to 4 stars due to Google Home being much smarter at answering general questions. The Echo is a good device but I think you should also seriously consider Google Home.</td>
    </tr>
    <tr>
      <th>6</th>
      <td>So I bought a second Echo Dot to experiment on making it portable. Here's the basics. Connected an Echo Dot to a Sungale 3000 mAh power bank disc (https://www.amazon.com/gp/product/B0184VF24U/ref=oh_aui_detailpage_o00_s00?ie=UTF8&amp;psc=1) and used a short usb to micro cable to power. On a full charge I was able to get about 3-4 hours of use. Added a LANMU Echo Dot case (https://www.amazon.com/gp/product/B01MXJ9XAF/ref=oh_aui_detailpage_o04_s00?ie=UTF8&amp;psc=1) and placed the Dot on one side, the cable in the top gusset pocket of the case along with a short 3.5mm audio cable (just in case) and the power bank fit snugly on top of the cables. Using this rig through my phones hotspot along with Amazons unlimited music to replace XM radio for only $3.99 a month plus full functionality of hands free Alexa services.</td>
    </tr>
    <tr>
      <th>7</th>
      <td>I got Dot about a week after the big Echo from my son as a gift. We set her up and all was fine. Until nothing eas fine anymore &lt;f0&gt;&lt;U+009F&gt;&lt;U+0098&gt;&lt;U+00A2&gt; . She would respond initially, but not always though, and when she was playing music she would not respond to the wake word. At all. I reset, few times, but nithing worked. I called Amazon. Within minutes they had me connected to Echo's tech support, and in no time did we have Dot fixed. They reset her factory settings with me. We tested and now our darling little Dot is happy and healthy! So call Amazon if you do have issues, it's a matter of setup or wifi or in any case, something that they can fix. Wonderful support for a wondrful device. I do wish I could call her Dot though. Or a name that I choose. Because now I have two Alexa's in one household &lt;f0&gt;&lt;U+009F&gt;&lt;U+00A4&gt;&lt;U+0094&gt;, and calling her Echo or Amazon ain't gonna work for me &lt;f0&gt;&lt;U+009F&gt;&lt;U+0098&gt;&lt;U+00A2&gt;. Please vote my review up if you liked it.</td>
    </tr>
    <tr>
      <th>8</th>
      <td>I'm loving my dot.  It's the perfect product to transform your ordinarty home into a SMART HOME!  Everyone in my family uses it and I mean everyone.  My wife loves to use the timers when cooking, I love it using with all my smart home technology to include the Nest Thermosta, Philips Hue Bulbs, Smarthings, Automatic, the list goes on!  Not to mention my 2 year old son has even learned how to get Alexa to play his favoriite songs!  I purchased two of them and I'm about to buy my 3rd!  Have I mentioned it's about to work with SONOS!</td>
    </tr>
  </tbody>
</table>
</div>



### 2. Tokenization to sentence/word using tm package 

<img src='tokenization.png'>

  <i><b>corpus structure:</b></i>

<img src="corpus1.png">

### 3. Feeding tokens to nltk package and assess positive and negative scores

<img src="posscore.png">

<b><i>Overall final scores</b></i>

<img src="overall.png">

<font size=4><b><u>Inference:</b></u> Of the latest 9 reviews pulled for this product, overall positivity shoots to 95%, which significantly shows the product is well received in the market.</font>
