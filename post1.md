---
layout: default
title: Nifty things I did
description: Thanks for reading this
---

# CCM0118 Computation 1 - EP(LFSR)
## The Assignment
***
The purpose of this assignment is to implement a criptography method for images. The cryptographic method used was one-time pad, utilizadin linear feedback shift registers (LFSR) to encrypt the files.

The explanation of LSFR and the rest of the assignment can be found <a href="http://www.cs.princeton.edu/courses/archive/fall10/cos126/assignments/lfsr.html"> here </a>.

TL;DR: To implement a `LFSR` class, this class should be used to build a `PhotoMagic` class. The last one should get a `.png` file and a `n`-bit encryption key. These bits will be fed into the `LFSR` object, which will initialize the `LFSR` initial state with it.

## Why? (a better TL;DR)
*** 
So what is a _linear feedback shift register_ and how can it be used as a tool for encyption? The thing about this mathematical device is that it is an interesting way to build a pseudo-random number generator. Which means it can be used as a device to generate a series of numbers which _looks_ random. But as the _pseudo_ lexicon might suggest, it is not truly random, as the initial state of the device is all we need to precisely determine all the future numbers. 

At first, the _pseudo_ thing might sound like a disadvantage, but it is exactly what we needed. This device generates a series of random-looking numbers, which will _feel_ random unless you know the initial state of the LFSR. The randomness can be used to hide our relevant info, and the initial state which will unravel the randomness will be our encryption keys.

<img src="assets\waluigi.png" alt="WAAA" width="500px"/>

## How?
***
```java
public class LFSR{
	private String sSequence;
	private int iTap;
	
	public LFSR(String seed, int iTap){
		this.sSequence = seed;
		this.iTap = iTap;
	}
	
	public int step(){
		...
	}
	
	public int generate(int iStep){
		...
	}
	
	public static void main(String[] args){
		...
	}
}
```

The `LFSR` class itself is very simple, it is does exactly as described everywhere. It has only two privates: `iTap` and `sSequence`. `iTap` stores the `LFSR`'s tap, which can be thought as part of the encryption key, as its value is crucial to determine the pseudo-random number sequence. `sSequence` is the current state of the `LFSR`, it is stored as a `String`, a sequence of `1` and `0` characters. In hindsight, I think I would've implemented differently, as the `String` implementation feels very wasteful.

At the end, `PhotoMagic` class doesn't do much besides interpreting the `.png` file, create a `LFSR` instance starting with the encryption key and the desired tap, generating the pseudo-random numbers, XORing the random numbers with the RGB pixel intensities and spitting out our encrypted file. Check out the following example to understand what I mean by XORing:

<img src="assets\xor.png" alt="Making this took longer than I wanted to" width="500px"/>

In this example, the color cyan, like any colour, has three intensities associated with the primary colours red, green, and blue. These three numbers are XORed with three random numbers (which could've been generated from LFSR using the adequates keys and so on...) 39, 230, 7. **This operation resulted in a dark blue colour**. One can verify that we can **repeat the same procedure**, XORing with the same three random numbers with the dark blue, and this will **recover the cyan colour**. That's a rather interesting property of the XOR operation, and I will comment no further on this. But what is important is that to recover the encrypted data, all we have to do is to execute the same script using the same encryption keys. So programming a single program, we've made **both the encryption and decryption softwares**. Neat, right?

## Did it work?
See for yourself an example of an encrypted image:
<img src="assets\Xwhat.png" alt="it is a rather old example" width="200px"/>
Now, you can see that it is not perfectly random. If you look closely, there's a pattern. Which means that our LFSR at some time it reaches the original state again, which will in turn generate the same numbers all over again, creating a weird looking pattern over the image. Interestingly enough, it still concealed rather well the original contents of the image. This can be fixed by using a longer encryption key, resulting on a longer <a href="#note">~~or perhaps infinite~~</a> period of repetition with a higher probability (this is an educated guess confirmed by some empirical experiments, I did not mathematically prove this nor I intend to). 

## Can we break it?
I regret to inform you reader that I'm not quite sure what the original image I presented pre-encrypted looks like. That's because I've included the image with the assignment as a test of the `BreakMagic.java`. It is an script designed to try to break the encryption by brute-force method. It merely uses the `PhotoMagic.java` class multiple times, checking if the image still looks random. It works on the premise the original image should be far from the random noise, thus, the frequency of all RGB intensities should be far from the original random noise. Of course, it will never work if you actually encrypt a random looking image such as the encrypted file itself, but this feels deliberately pedantic _(kinda)_. 

## What did we learn?
This assignment was one of the most fun and interesting I've made during my undergrad course. I expect you could at least feel the thrill I felt while coding this interesting contraption.


## Note
<ul>
    <ui>I wrote that the period of repetition of LFSR could be infinite, but after 5 seconds I just realized it made no sense. That's because this generator depends only on the present state to precisely determine all future outputs. This also means that if it somehow ends up on a state it already had been, it will start repeating the same outputs, resulting in a repeating pattern. But we also know that there's a limited amount of states accessible to this system (which is $2^n$ in which $n$ is the number of bits on `LFSR`). Therefore, if we run this system for $2^n + 1$ times, it would've at least visited one state more than one (see <a href="https://en.wikipedia.org/wiki/Pigeonhole_principle">Pigeonhole principle</a>). Therefore, it will stabilish an orbit, repeating the same sequence again and again. </ui>
        </ul>