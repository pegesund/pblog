---
title: To type or not to type, that is the question
tags: golang, neural network
---


The border between typed and untyped languages is as old as the programming history. I learned programming on a Sharp Mz-721, moved on to the fantastic BBC microcomputer before I got a Sinclair QL.

The first border between typed and non-typed was my experience from moving to Basic to assembler code. In Basic one could change types on a variable as you wanted, but in 6502 assembler which was 8 bit you had in reality one type, the 255 bits byte. All other types, like floats and strings, was derived from this type and to do algebraic operation one had to use algorithms. 

I later moved on to the 68000 assembler, Turbo Pascal for Windows and later C-programming for the Unix-environment.

After twiddling with C for several years, Pearl appeared on the stage and gave an extremely increase in development speed, compared to the older languages. In these first years the history of Pearl and the growth of Internet was tightly connected.

Even though Pearl still has its corners, the programming language has to a large degree lost much of its domain. These days there the number of programming language is out figuring the number of animals in Noah´s arch. Like in a jungle there is a Darwinian evolution going on, and the least fitted will disappear in time. Plotting this evolution in chart and looking at how the languages evolve, based on each other, would have been great fun – but that is for later. This post is about the single programming-language-gene which turns is connected to typing only.

When I learned programming, speed and limited ram was two of the most valuable resources. While programming one would have to take into account how the <a href="https://en.wikipedia.org/wiki/Raster_interrupt">raster interrupts</a> was handled by the hardware, while drawing graphics.

While I encountered the C programming language, I was pretty amazed. I could code at assembly speed, while still having many of the benefits from Basic. The execution time of a C-program was really fantastic, and I throwed all other programming languages over boards.

Even when Pearl appeared, I was skeptical – but by time I did program more and more in Pearl, even being official skeptical. In hindsight it was easy to see what happened. In the early days the sparsest resources were connected to hardware – cpu and memory. But while the hardware evolved the most important factor turned out to be development time. 

The barrier between typed and not-typed languages are still an existing one. Dynamic languages like Python, Perl, Ruby and PHP are untyped while Java, C# and C are static typed. Actors with strong typing, like Scala, Rust, Haskell and Ocaml have gotten attention and put the weaker typed (but still typed) language in the middle ground.

In this area I thought it would be fun to look at the origin of some of the larges sites that has appeared from “nowhere” and from there has taken over Internet. My target is to find out if typing or non-typing has been the winning factor in the past. I have pretty randomly picked some large sites which I have though is important, and done some Internet digging on which programming languages these systems were written in. My research is pretty shallow and is probably prone to bugs, keep that in mind. But as far as I have found out, it looks like this.

<table>
<tr><td>google </td><td> python, java</td></tr>
<tr><td>facebook </td><td> php</td></tr>
<tr><td>twitter </td><td> ruby</td></tr>
<tr><td>airbnb </td><td> ruby</td></tr>
<tr><td>github </td><td> ruby</td></tr>
<tr><td>kickstarter </td><td> ruby</td></tr>
<tr><td>Instagram </td><td> python</td></tr>
<tr><td>spotify </td><td> python</td></tr>
<tr><td>Netflix </td><td> python</td></tr>
<tr><td>uber </td><td> python, js</td></tr>
<tr><td>dropbox </td><td> python</td></tr>
<tr><td>reddit </td><td> python</td></tr>
<tr><td>yahoo </td><td> php</td></tr>
<tr><td>Wikipedia </td><td> php</td></tr>
<tr><td>flickr </td><td> php</td></tr>
<tr><td>amazon </td><td> perl</td></tr>
<tr><td>ebay </td><td> java</td></tr>
<tr><td>linkedin </td><td> java</td></tr>
<tr><td>stackoverflow </td><td> c#</td></tr>
<tr><td>duckduckgo </td><td> perl</td></tr>
<tr><td>bing </td><td> c#</td></tr>
<tr><td>whatsapp </td><td> erlang</td></tr>
<tr><td>snapchat </td><td> python/php</td></tr>
</table>

One of the sources is <a href="https://en.wikipedia.org/wiki/Programming_languages_used_in_most_popular_websites">wikipedia</a>

The times has off course changed since the early versions of these systems, and I know that these days Facebook for example is using for example the pretty programming language <a href="https://engineering.fb.com/security/fighting-spam-with-haskell/">Haskell</a>, but still think this overview is interesting.

It seems that in the history of Internet those who has valued the development speed, rather that execution speed or all the good that comes with type safety has had a clear advantage. Pretty much all the larges sites have their origin in a untyped language and from the point of statistics, it seems coding untyped has given the availability to reach marked faster. Features like shorter debug-cycles, better debugging tools, fewer code lines and great libraries using features like run time introspections (like in Rails) have given higher survival rates.

Looking at the list above it seems moving along with a dynamically typed language has been one the most important genes in the Internet jungle so far. One question is if this factor is under change, or if is still is like this. Until new statistical numbers are coming it seems that untyped is the way to go for companies how would like to conquer new land quickly. 

Off course you should take this with a grain of salt – I would never code a cpu intensive algorithm in Ruby or Pharo (really nice, have a look at it!). But still, most programmers overestimate the cpu and ram factors in their projects. The sparsest resource is still the human brain.

