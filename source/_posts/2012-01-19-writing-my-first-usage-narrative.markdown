---
layout: post
title: "Writing My First Usage Narrative"
date: 2012-01-19 12:19
comments: true
categories: [Use Cases, Usage Narratives]
---
In an effort to understand the analysis and design of object-oriented systems, I picked up a book by Alistair Cockburn (pronounced COH-burn) called _Writing Effective Use Cases_. Building object-oriented systems can become quite complex. If you haven't thoroughly analyzed how your system is supposed to work, you may come to wonder whether or not you are meeting the needs of the stakeholders. This is where use cases come in to play. According to Cockburn, a use case "captures a contract between the stakeholders of a system about its behavior" (Cockburn 1).  But if you've never written a use case before, you might wonder how you would begin. This is where usage narratives come in to play.

## Usage Narratives: Catalysts for Writing Use Cases

Toward the end of the first chapter, Cockburn talks about writing usage narratives as a way to warm up prior to writing use cases. Usage narratives are stories. It is a story with a specific actor/actress in mind. When I say actor/actress (for simplicity, 'actor'), I mean the main character of the story who will use the system you are designing. To keep the reader engaged in the usage narrative, it helps to be very specific with your actor and to add just enough details to paint a picture of who the actor is. A usage narrative will help you see how your system will be used. It will put you in the right mode of thinking to delve deeper if needed when you write your use cases.

One of the exercises in the chapter asks you to write a usage narrative for the ATM you use. Here is my first run at it:

***
__Usage Narrative: Withdraw Cash__

Daniel, a 27-year-old absent-minded engineer who keeps track of his expenditures by saving every receipt, walks up to the ATM. He slides his card in the ATM. He selects "Withdraw Cash". He selects the amount $100. When prompted by the system to finish his transaction, he selects "Yes". The system asks if he would like a receipt. He selects "Yes". The ATM prints out a receipt. Next, the ATM alerts Daniel that it has ejected his card to avoid having him forget to retrieve it. Daniel takes his card and heads home.
***
Then, I read Cockburn's example:
***
__Usage Narrative: Getting "Fash Cash"__

Mary, taking her two daughters to the day care center on the way to work, drives up to the ATM, runs her card across the card reader, enters her PIN code, selects FAST CASH, and enters $35 as the amount. The ATM issues a $20 and three $5 bills, plus a receipt showing her account balance after the $35 is debited. The ATM resets its screens after each transaction with FAST CASH, so Mary can drive away and not worry that the next driver will have access to her account. Mary likes FAST CASH because it avoid the many questions that slow down the interaction. She comes to this particular ATM because it issues $5 bills, which she uses to pay the day care provider, and she doesn't have to get out of her car to use it. (Cockburn 18)
***

After reading his example, I thought: I never got my money! Only after having read his example and then read mine did I notice that my ATM machine never dispensed the $100 I requested! I thought it funny that I described myself as absent-minded before noticing I had forgot to pick up my money from the ATM. I probably revealed more than I had wanted in this example. There's nothing wrong about the way I wrote my usage narrative; however, I missed some important details. This isn't a bad thing especially since I realized this quickly enough. The key is that I recognized what was missing upon reading it.

## Comparing and Contrasting
Let's take a closer look at how I could have improved my usage narrative. 

To start off, I could have been more specific with the amount of money I received from the ATM. I probably don't want to get back a $100 bill. I would rather get $20 bills because it is more likely I will get change easily. Another thing I could have done is elaborate on how this ATM provides _value_ to me. I noticed that Cockburn's example mentions three key reasons why this ATM has value to Mary:

1. It issues $5 bills. Mary can use the $5 bills to pay the day care provider.
2. It is accessible. She doesn't have to get out of her car and walk up to it. She can use it from the driver's seat of a vehicle.
3. It dispenses cash quickly. The ATM has a FAST CASH feature that make withdrawals a snap.

My usage narrative did include one reason how this ATM provides value to me although it wasn't made as clear as Cockburn's example. I described myself as being absent-minded and one who collects every receipt in order to track my expenditures. This particular ATM does provide a receipt printout which is exactly the feature that meets my needs.

I tried to be specific with the character I used in my example. In particular, because I noted my actor as being absent-minded, it became clear that the ATM needed to have a feature that reminded the actor to retrieve their card after dispensing cash. In that respect, my example is comparable in quality to the detailed description of the actor in Cockburn's example. Since I noted this ATM reminds its users to retrieve their card, one can argue that I implicitly demonstrated the value of the ATM in my example: by alerting the user when the transaction is complete, the ATM reminds the user to not forget their card at the ATM.

## Essential Elements of a Usage Narrative

Now that you've had a chance to look at what a usage narrative looks like, I will wrap up with the elements that are essential to writing a usage narrative according to Cockburn. A usage narrative must be brief in order for the reader to comprehend it easily. It must capture the mental state of the actor who will be using the system, that is, "why he wants what he wants or what conditions drive him to act as he does" (Cockburn 18). He goes on to say that "details and motives, or emotional content, are important so that every reader… can see how the system should be optimized to add value to the user" (Cockburn 18). Thus, a usage narrative must:

* Be brief
* Capture the mental state of the actor using the system
* Demonstrate value to the actor

Combining the above elements in a usage narrative will help you see the system in use. Cockburn also reminds us that "the narrative is not the requirements; rather, it sets the stage for more detailed and generalized descriptions of the requirements" (Cockburn 18).

## Your Turn
The next time you try writing use cases, consider writing a usage narrative instead to warm you up for the real thing. You could even try writing a usage narrative for the ATM you go to and add it in the comments below.

### Bibliography
Cockburn, Alistair. _Writing Effective Use Cases_. Boston: Addison-Wesley, 2001. Print
