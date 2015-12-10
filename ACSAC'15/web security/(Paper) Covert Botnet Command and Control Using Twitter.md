
# Covert Botnet Command and Control Using Twitter

By Nicholas Pantic

December 9th, 2015

## Original research goal

Study how an attacker can utilize the power of social networks

### What is involved?

1. Choose a social network (Twitter)
1. Develop method of communication (stegano system)
1. Apply communication method to botnet C&C and study potential weakness
1. To help accomplish these tasks, we used a database of 7.3M tweets from 3700 verified twitter accounts.

### Why Twitter?

* 320M users
* In general, network traffic to Twitter is not suspicious 
* Nearly 100% up time globally with fast access times
* Reliably posts all tweets without loosing info
* Bottom line: why spend time worrying about your botnet C&C architecture if Twitter will do it for you?

### Existing work

* Stegobot: embeds commands and data in to images posted on FB
* Use of Twitter to send commands directly to bots by placing commands in tweets
* There are other tools that use direct messages

### Can we avoid embedding commands in message data?

* A secondary goal of this work is to only use metadata
* This can make the system more flexible
* However, it restricts the transmission rate

### What kind of metadata?
* Must use metadata that can be controlled by sender
* Can't control posting time
* Can't control tweet ID
* Sender controls the message, so they can use message length:
    * Tweet length restricted to 140 chars
    * A char is a UTF-8 character

## Steganography: prisoners' problem

```
Alice <---> Warden <---> Bob
```

If Alice and Bob want to coordinate an escape, they must hide messages because everything is read by
the warden. Encryption is too much suspicious. Instead, need to write a message that looks completely normal but that 
embed the real message relative to escape.

### Why?

For botnet C&C, botmasters want to prevent adversary from even detecting communications.

### How?

```
Alice --- (m) --> Embed --- (x^m) --> Extract --- (m) ---> Bob
```

### What can Warden do?

#### Passive Warden

Just looking at x^m, and try to figure out if there is a hidden message or not.

#### Active Warden

Here, Warden actually modifies the sent message. And so Bob cannot recover m correctly.

### What does active Warden implies here?
Someone else gets control of the botmaster's Twitter account and post fake messages
Someone intercepts bot's read operation to feed it fake messages
* For us, we'll assume only passive Warden scenarios.


## High level overview

Consider sending the message `HELLO`. 

1. Break message into tokens (here letters)
2. Use encoding to convert that token into a tweet length
3. Generate a tweet of that length
4. Post that tweet (using twitter API)
5. Read tweet from bot side (idem)
6. Recover token from tweet length
7. Reconstruct initial message

### Encoder

* Finite weighted alphabet A such that |A| <= 140 to act as input tokens for messages, e.g. ASCII
* Finite weighted output alphabet N (the possible tweet lengths) => try to avoid tweets of length 1 for instance
* A procedure `Encode: A -> N` for mapping tokens to tweet length
* A procedure `Generate` for creating tweets of appropriate length

#### Sending the messages

Both sides share a key used by `Encode`/`Decode` function.

#### Performing `Encode`

* Input: finite alphabet A
* Output: an encoding table A -> N

1. For each a in A:
    * Probabilistically choose an n of N
    * store that mapping (a, n)
    * remove n from N
2. While N is not empty
    * Probabilistically choose an n in N and a a in A
    * store the mapping (a, n)
    * remove n from N

#### Encoder generation example

Using A = {a, b} and N = {1, .., 10}

1. (a, 5) ; (b, 1)
2. (a, 2); (b, 3); (b, 4); etc.

=> several length for a given symbol if |A| < |N|

### What appears on Twitter?

Let's encode "FOO". 

3 tweets (twitter account: [@alicesends](https://twitter.com/alicesend))

## For Botnet control

* Generate a language to control a botnet
* Bots only need a twitter access (no account)

### How much data can we send?
* 140 symbols
* Seven bits per tweet

### Making a botnet on Emulab

One single mistake in transmission. Why? Twitter trims leading and trailing spaces.

### How many tweets can we send each day?

Average number of tweets/day/account: around 10.

### Usernames 

* Generated using Markov chains to look like real names
* Actually fools real persons (tests using MTurk): approx. same result than real names (ie. confidence average of 0 in a range of [-2, +2]) and far better than random names (-2)

### How to avoid detection?
* For issuing commands, bots don't need their own accounts, only botmaster needs account => gives flexibility in how botmaster organizes follower network
* Generated user names in how botmaster organizes follower network
* Do the tweets look suspicious? Main challenge actually.

## Remaining challenges
* Tweet generation
* Overall system bandwidth is low so it is only suitable for short communications => compression?

