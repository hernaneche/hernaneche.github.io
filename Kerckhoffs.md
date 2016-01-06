#The Kerckhoffs's Principle 

[Auguste Kerckhoffs](http://en.wikipedia.org/wiki/Auguste_Kerckhoffs) was a linguist and a cryptographer who, back in 1883, published various essays about Military Cryptography (two words usually related, but not necessarily). Kerckhoffs's principle is one of the many rules of thumb included in his essays, it reads as follows: _«The design of a system should not require secrecy»_.

What does it means!? Here, we'll try to unravel it, reviewing its meaning and questioning our understanding about what privacy is.

#### What's to hide for concealing

Keeping a secret is about restrict its accesses, avoiding the unintended, while allowing those intended, but as you could imagine, it's not easy to distinguish intentions, nor intention's owners. Secrets need to be visible for some, and invisible to others, so how to hide? what to hide?. Kerckhoffs's principle can also be stated in this way "A cryptosystem should be secure _even_ if everything about the system, except the key, is public knowledge", and it immediately makes sense: _"A house should remain secure even if everybody knows where the door is!"_.

Today we are already using some form of data encryption on most of our global communications, and most of these systems were designed being aware of this principle, it means that _its designs_ are not secrets, but what is hidden are their _keys_, like the passwords, secret questions, among other kinds of keys.

#### Can a secrecy system be _perfect_?

[Claude Shannon](http://en.wikipedia.org/wiki/Claude_Shannon) asked it before us, and he answered yes; how? by using a random different key for every single message, and being each key the same length as each message. This schema is called -[random one time pad](http://en.wikipedia.org/wiki/One-time_pad)-, the method is like using a random invented language for every word, even for every letter we write, so a third person will find impossible to decipher our conversation from there, because the encrypted message will have no clues of the original message, but it's not a very practical technique. Why? Well, how we will tell the other what random language/keys we are using for every word? Could we both have a list of the successive language sequence? Perhaps, but how to send that list? What if someone else see our list of keys? Once you have random numbers written in a list, are they still random? as you could see randomness is fragile. So, can we send those varying keys along with the messages? Well, yes, but sending our messages along with its translations would not hide them for too long[1]. Shannon "one time pad", despite not being very practical, it's unbreakable, and is Kerckhoffs-approved!, because it's a _public_ mechanism where all its successive keys are _hidden_.

[1] For completeness sake we have to mention that it's possible in principle to send keys through a Quantum scheme, but that's very far from our issue now

### Why does it work?

An obvious question arises, how designs which meet Kerckhoffs's Principle can lead to a stronger cryptosystems than without using it?. Let's fall in the extreme opposite naïve case of using only a hidden secret mechanism to encode a message, with a fixed secret (a not changeable password), this would be like hiding a secret with another, as having a secret passage door, but without lock (perhaps behind the bookcase), yes, the system will remain secure but only until it's found by an attacker, since then, all successive communications will be endanger, and even worse, it would take a long time to redesign the whole system, so it will be compromised for that while. These methods are called "security through obscurity" to belittle them, it refers to designs that hide mechanisms that are not easily changeable, imagine you have to give a specific shape and colour to a complex physical machine to fits certain hiding place, with a camouflage colour to blend into that background, it could ends up having a clumsy shape, perhaps not very suitable for what it was designed[2], and if "enemy" discover our hiding place, we have to re-conceal the machine elsewhere, shaping it again and painting every gear again; a bad idea.

Shannon outlined Kerckhoffs idea in a minimalist way: «The enemy knows the system», a straightforward manner to encourage designers to consider that _others_ may already know our security mechanism, to be ready for when that happens.

On the other hand, a secure algorithm means that, without the proper key, it will forbid the access to protected data (or make it very difficult), even knowing how the system works, no right key, no right data, it also needs to be fast enough to be useful in practice; so if a key is compromised it can be changed faster than replacing the whole algorithm (Analogy: changing the locks is a lot easier than making a new door!, so a secure algorithm is like a well-crafted door).

If those techniques are used together, a hidden password _plus_ a hidden _and secure_ algorithm, it _could_ lead for a while to a stronger and harder to break system than applying only one strategy at a time, of course if we were constrained to choose only one of them, in principle, the hidden key strategy alone is a lot harder to discover and faster to change than the hidden mechanism alone.

[2] Except that the machine were specifically built for hiding purpose _**only**_, with no other function, that would be a funny exception.

#### Hiding passwords vs hiding algorithms

Until now we were comparing the _time and resources_ it takes to _redesign_ a whole system, versus what it takes to create a new password; and it turns out that passwords can be produced faster (and cheaper). Now let's consider a different and also important question, **Is it easier to unravel a hidden algorithm than a hidden key?**

Yes, although in practice it depends, in theory it's a resounding yes, we can even explain it without using analogies of secret doorways nor hidden machines, the fact of the matter is that most of any working algorithm **needs** an inner structure to work, and cryptosystems algorithms are not an exception, they usually need an structure, while passwords doesn't, so in general it's theoretically easier to get clues of that structure, to reverse-engineer or find patterns on an obscure machinery design, than to "reverse" a password _that has no design structure at all by definition_.

### Why it doesn't

So far so good, but those are _theoretical_ differences, however in practice the enemy often doesn't know the system, and some hidden mechanisms remain hidden, moreover although there is nothing behind a password to be reversed, in practice we don't pull passwords out from a hat, and generating a password related to nothing is not an easy job (and of course, the names of your pets are not a good resource). Anyway Kerckhoffs's principle is just a design criterion for cryptosystems, is a warning, not a full instruction manual, isn't it?.

#### Trees, forests, and standardized yardsticks

Is a public algorithm _better_ than a private one ?[3] This is often a topic of discussion (or even a non debatable topic), since any volunteer observer can review the public mechanism and report a bug, as well as could be the first of taking advantage of it without reporting anything, etc... But such discussions end involving people and trust (not even a perfect system could abolish these issues); so it might or it should lead us to ask, how much does the algorithm matter? what's the relative importance of an algorithm design over others factors? Ok, it's probable that a reviewed public algorithm can be more secure than a private one, but having so many other affecting things around, the question could be very misleading!. "Well, someone have to take care about the algorithm", but if the algorithm ends in a global usage recommendation, that's no longer about the algorithm.

[3]In this context the use of the word public only means that the mechanism can be known by everybody, and private here means that inner mechanism are intended to be hidden, it has nothing to do with public goods from a state nor private goods from individuals, please don't confuse them.

#### What questions we make to history?

The Enigma Machine was a cryptosystem used during World War II, whose encryption it's said it was broken _because_ of some human errors together with a poor security design based on hiding the machinery (it was NOT Kerckhoffs-approved!). This example from history is often shown as a kind of triumph of the theory, sometimes it's used to show how security through obscurity is poor, and how it has failed in practice.

But there are more details that worth to mention; for example, what resources were used to break it? There were a lot of people at the time involved in trying to break Enigma Machine from many powerful countries (including [Alan Turing](http://en.wikipedia.org/wiki/Alan_Turing) ). So, was it easy to break it?. How hard it could be to break many many different Enigma like Machines? (We have already ran out of Alan Turing's, and alternates will not be enough for every machine), I think that a large number of hidden machinery would be still a hard subject, because obscurities are not all created equal.

We should also emphasize that Enigma is not a good pedagogical example, it's misleading from scratch, because the system was used to hide war movements, so it's not an example that let us focus on cryptography, it's about war, so what is the point of coldly pointing out the weaknesses of this system ? _In which sense we are saying that a more secure system would have been _better_?_ Are we suggesting to use better systems for a next war? It sounds like an advertising from an unscrupulous seller of war cryptosystems. **There is no such thing as the best system isolated from all else, on the contrary, any criterion about what is best is related with people and even with everything else**. Secrecy is not only about war, it's about privacy, and it's about having a space to think for ourselves, to talk with our partners, with our loved ones, with all our recipients, and with no one else.

#### History again: Scope, global and local

The world has learned Kerckhoffs's principle, among other theories. Today we are all using the same tested algorithms to protect everyone's privacy (?), all for one, and one for all. Despite of that, there is a huge and growing lack of privacy, and every human word, every move is being recorded in computers. If a system knows in advance what our movements are, then under certain circumstances it could anticipate our next moves, we can be controlled by that system, be the government, be the market, be Skynet, or whoever. Is that what we want? If not, are we doing something wrong? what can we do better? The algorithm? Maybe not!, by getting the best ever public algorithm (and even because of it) the scope of its use could become dangerously global, as well as its flaws, sometimes the best is the enemy of the good.

_The global_: we have evolved a technology that let us communicate worldwide using same symbols, protocols, using converters, automatic translators, and compatible systems, our data is now spread worldwide like never before in history, our scope became worldwide, while most of us have a localized life, and all of us have a limited capacity to cope the whole world, certainly not enough to live spread globally as data.

There is a singular and well known story over this issue that could be traced back to the Bible, it's called _The Tower of Babel_, and goes something like this: there were people trying to build a big tower, so high, that it reaches heavens, what for? to avoid being scattered around, to give themselves a name, but then The Lord confuse their languages so they will not understand each other, that's it. Something similar is happening now, we could "reach heaven" (at least some internet clouds) by sharing, and through world cooperation, but that's not easy, our selfishness, our myopia, evilness, desire of control, request of control, and so on, in resume: all of our human limitations, leads us to the _need_ of different languages, localized, limited, like echoing our own limitations.

_The local hit again_: what if many universities, countries, districts, companies, groups of people uses each one a particular non-standard communication algorithm, then they can not be understood globally, but strangely as it could seems, that's sometimes what we want. Kerckhoffs's principle doesn't imply we should use all the same method. A single secure algorithm for all ? Is this idea a gimmick invented by those who want to spy easier? What about one hundred million of unknown private methods? But that way we would lose Babel, but Babel tower is dangerous, and so on...

[That it's all just a little bit of history repeating](https://www.youtube.com/watch?v=yzLT6_TQmq8&t=1m24s)

#### Human future, theory and practice

We face today a worldwide vulnerability on privacy (or voluntary surrender of privacy), inside and outside computers, and as with many other humanitarian problems of higher priority, the answer can't just be "maths is ok", because perhaps we knew that already, or nobody ask about it, we are facing problems of many levels, requiring everyone's voices and presence, it's self evident that is not enough to talk about algorithms. Encryption systems are issues of computational science _mainly because_ secrecy and privacy are issues of human life.

A mathematical model could be perfectly correct, but applying it unquestioningly is definitely wrong, it's the old issue about the tools and its different uses, today's problems of privacy are also [ethical problems](http://xkcd.com/538/), and there is an opportunity for scholars to speak about this, to join discussing about differences between general and particular, between theory and practice, to help practitioners by challenging them to _think_, as well to be involved in ethics, it's fine to know a theory, provided that the personal views are never silenced; but surprisingly enough the opposite advice is often given, encouraging to stick the theory, to follow the proven rules, it's like saying "don't think for yourself, someone already did it for you", but nobody can think for someone else!, it's a lie, and a very rotten idea for a general approach, "don't reinvent the wheel" is sometimes confused with "don't think for yourself", although it may be an efficient tactic for certain works, it's a very dangerous and profoundly sick ideology.

Computer software interacts with communities on a daily basis, so there is no excuse for being "detached from algorithms consequences", we should accept some kind of responsibility regarding this ways of acting. Scientist needs to think outside science. Making pure science seems like heating pure water, both may boil over.

We are humans, it's a pretty relevant fact to put aside, or it should be, [dehumanization of exact sciences](http://devlinsangle.blogspot.com.ar/2013/08/will-this-mathematics-be-of-any-use.html) is sometimes getting as far as "the abstraction from being a human", and we should face that it can have very harmful consequences that are not only humanitarian ones, but also technical.

Using distributed systems, does not guarantees privacy, the central topic here is not what system is better for avoiding some limitations or weaknesses of human behaviour, but just the opposite, i.e. to avoid ignoring it, to be aware of our limits, and ourselves. What could it mean wanting to implement a single global and infallible security system but a dangerous act of alleged omnipotence that we don't have? Either the attempt of making a global system _to_ spying, or the attempt of making a global system _against_ spying, are two faces of the same mistake.

**References**  

A book about cryptography history  
[http://simonsingh.net/books/the-code-book/](http://simonsingh.net/books/the-code-book/)  

A great post talking about ethics in science  
[http://devlinsangle.blogspot.com.ar/2013/08/will-this-mathematics-be-of-any-use.html](http://devlinsangle.blogspot.com.ar/2013/08/will-this-mathematics-be-of-any-use.html)  

A related video  
[http://www.youtube.com/watch?v=KsN0FCXw914](http://www.youtube.com/watch?v=KsN0FCXw914)  

Wikipedia links  
[http://en.wikipedia.org/wiki/Auguste_Kerckhoffs](http://en.wikipedia.org/wiki/Auguste_Kerckhoffs)  
[http://en.wikipedia.org/wiki/Claude_Shannon](http://en.wikipedia.org/wiki/Claude_Shannon)  
[http://en.wikipedia.org/wiki/One-time_pad](http://en.wikipedia.org/wiki/One-time_pad)  
[http://en.wikipedia.org/wiki/Enigma_machine](http://en.wikipedia.org/wiki/Enigma_machine)  

xkcd  
[http://xkcd.com/538/](http://xkcd.com/538/)
