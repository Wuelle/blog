---
title: "Playing with magnetic stripe cards"
date: 2022-07-03T16:12:30+02:00
---

In this post, I am going to take you through the process of reading the encoded data
from a magnetic stripe card, without any technical equipment.

While researching this topic, i came across a [really old, but also really good explanation](http://phrack.org/issues/37/6.html) of
how these work on a physical level, so I am not going to cover that here.
TLDR; Magnetic Stripes contain tiny ferromagnetic particles whose polarity can be controlled. They produce magnetic fields which can
then be detected by a reader.

As any eighth grader can tell you, magnetic fields can be visualized using iron powder, and that is exactly what we are going to do.
Magnetic Stripes can contain up to three tracks, the first and third one are encoded with 210 bits per inch and the second one is using
75 bits per inch. (or 82/29 bits per cm in non-freedom units) Cards that only contain a small amount of data seem to only contain track 2 and I have seen some with
Track 2 and 3, but no Track 1.

The grains of iron powder i am using have a size of around 90&#956;m. Every bit stored on the card consists of two vertical stripes, so at 75 bits per inch
that's 169&#956;m per stripe. 
![my iron powder](https://i.imgur.com/9ZwoZ6s.jpeg)

I first tried to cover the card in iron powder, then shake it to remove excess particles. That worked to some degree, i could
clearly see the different tracks but was unable to make out individual bits, especially since most of the card was still covered with powder.

![my first unsucessful attempt](https://i.imgur.com/QsrdQim.jpeg)

I then added some powder into a cup of water, stirred it, let the larger particles settle down and then dipped the card into the mixture.
That prevented powder from sticking to non-magnetic parts of the card but the powder still contained bigger particles that prevented me from reading
the individual bits. On my third attempt, I filtered the iron-water mixture through a piece of cloth and the repeated the same process as above. 
And finally, the bits were clearly visible! 
![successful attempt](https://i.imgur.com/wiRu0LL.jpg)

On to the next step, actually decoding the data!
The bits are encoded as stripes followed by either another stripe (=> 1) or a missing stripe (=> 0),
so the sequence `||| | ||` would be `1001`.
Decoding this is quite a laborous process.

![bits](https://i.imgur.com/OzL5fuN.png)

The format i am covering here is the ANSI BCD format.
Every track starts with some zero bits (to calibrate the reader, since the bit frequency of individual cards may vary).
After that, bits are grouped into groups of five, with 4 data bits and one parity bit. The parity bit is always set such that
the total number of set bits in the group is odd. Bits are ordered LSB-first (so 13 would be `1011` and not `1101` like you are used to)
Certain bit combinations have special meanings, which are as follows:

| Data Bits | Parity | Character | Function |
| --- | --- | --- | --- |
| 0000 |  1 | 0 (0H) | Data |
| 1000 |  0 | 1 (1H) |   " |
| 0100 |  0 | 2 (2H) |   " |
| 1100 |  1 | 3 (3H) |   " |
| 0010 |  0 | 4 (4H) |   " |
| 1010 |  1 | 5 (5H) |   " |
| 0110 |  1 | 6 (6H) |   " |
| 1110 |  0 | 7 (7H) |   " |
| 0001 |  0 | 8 (8H) |   " |
| 1001 |  1 | 9 (9H) |   " |
| 0101 |  1 | : (AH) | Control |
| 1101 |  0 | ; (BH) | Start Sentinel |
| 0011 |  1 | < (CH) | Control |
| 1011 |  0 | = (DH) | Field Separator |
| 0111 |  0 | > (EH) | Control |
| 1111 |  1 | ? (FH) | End Sentinel |

[source](http://phrack.org/issues/37/6.html)

I was quite confused at first because the format did not seem to match the one on the card.
Luckily, some friends of mine noticed that the bits were in reversed order which makes sense, since I applied the powder to the back of the card.
*Sighs*, time to add all the labels *again*, this time in the correct order.
![bits, this time correct](https://i.imgur.com/qpVifiY.png)

After the initial stream of zeros, there must be a `Start Sentinel`(SS) group marking the beginning of the data stream.
The encoded data must end with a `End Sentinel`(ES) group (after which another parity group follows, but we are not going to concern
ourselves with that one)
These are the decoded groups on the stripe:

![decoded groups](https://i.imgur.com/OBZ4V9Y.png)

So the data contained on the stripe is `6364538724104860-49121013673`. 
The first number matches the one printed on the card, the second one is probably some sort of secret
that is not shown on the card to prevent you from being able to clone other peoples cards
just by looking at them.
