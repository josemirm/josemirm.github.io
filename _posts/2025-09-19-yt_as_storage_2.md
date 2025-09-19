---
layout: post
title:  "Using YouTube as a storage II (Error correcting is hard)"
date:   2025-09-19 22:00:00 +0200
categories: Software
tags: Python YouTube Storage
---

## Continuing where I let it...

(This blog post ins the continuation of THIS ONE) <<-- ADD SOME LINK HERE

I should have written this a couple weeks ago, when I continued the experiment with some error
correcting and got some interesting result, but hey, learning different algorithms and applying them
is a lot more of fun than writing a blog post (which I also like, don't get me wrong).

I think this will be the last time, and I will write the post at the same time I code or research
just to avoid forgetting anything. This is a really good way to both document not only how something
works, but also why I took certain decisions and the result of those (even if they aren't coded).
Like the logbook of a merchant ship.

## Analysing before actuating
Instead of using the previous approach of getting the "image difference" with ImageMagick, I focused
on the raw data before and after video compression. I done that getting the different frames
generated before and 

![](assets/img/hamming84_flipped_bits_comparing_whole_bytes.png)
*Comparing all the bits between between the compressed and the uncompressed images. Around 34% of
bits were flipped. Yikes!*

In the previous image you're what a disaster the compression makes to the data. That's a loss of a
third of all the data! But wait, in the previous implementation I only saved data in the higher part
of the byte instead of the whole one. This was the result only checking those bits:

![](assets/img/hamming84_ber_with_only_the_high_bits.png)
*That's a lot better! Around a 21% of data loss is a lot better.*

From losing a third to a fourth of all the data compressed is a lot better result, but then I also
have to consider how is re-scaled from 720p to 1080p. Would that BER change when the process is
reverse?:

![](assets/img/hamming84_ber_high_bits_half_size.png)
*The same 21% than before! Notice how the amount of bits changes between every analysis.*

Not a single improvement!

After getting those numbers it wasn't *that* bad, and with a simple Hamming 8,4 it would solve it,
right? Wrong! I didn't done the math, and the 21% of 8 bits are 1.68 bit. Hamming 8,4 can only
correct a single bit flip for every 8 bit of code (4 bits of real data), and detect up to 2 bits
flipped. 1 bit is less than 1.68 bits.

I spend a lot of time implementing that algorithm just to be invalid after test it, but hey, at
least I've learnt not only to implement that Hamming code, but also to be more careful and "measure
first, measure twice, and cut after".


# Picking a correct algorithm for the task


## Hamming 8,4 and its implementation

( MAYBE ADD THIS IN ANOTHER FILE )
