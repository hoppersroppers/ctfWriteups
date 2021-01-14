You are given the file Mandiant.pdf. It's a long, legitiamite looking file, so don't waste your time reading through it (or do, it's interesting).

First step, like any file format challenge, is to run your favorite scan tools on it and look for extra files that pop out or any anomalies.

In this case, I'm a fan of pdf-parser <https://blog.didierstevens.com/programs/pdf-tools/>. It comes pre-installed on some distros, others you'll need to get it from here <https://www.aldeid.com/wiki/Pdf-parser>. If you don't have pdf-parser available, you can also try to muddle through reading specs or using other tools, but it will take a bit longer. 

Scanning through the given .pdf you will see an embedded file in a stream. The time it takes for you to identify this is based off of your tools available, your knowledge of the file format, and luck, in that order. 

* If your tool can find it for you, that is always the best option
* If you can look at a file's headers and know what is happening, that also works, but requires a lot more knowledge
* If you are going with luck, you will find the answer on a long enough timeline, but it will be painful. 

This is why I believe in teaching tools and file formats when learning how to succeed in CTFs. 

So let's say you have found the stream through some way or another. Using your knowledge of tools, you can go with:

* If you have pdf-parser, this is somewhat straight forward, use the command $ pdf-parser --object 3 --raw --filter Mandiant.pdf > out as demonstrated in <https://github.com/krx/CTF-Writeups/blob/master/CSAW%2015%20Finals/for300%20-%20Mandiant/README.md>
* If you don't have pdf-parser, you are basically guaranteed to have qpdf. Use of qpdf is shown here: <https://github.com/ctfs/write-ups-2015/tree/master/csaw-finals-ctf-2015/forensic/mandiant>
   * qpdf is able to pull streams (and other parts of the complicated pdf file format) into plaintext for easy parsing using the command $ qpdf --qdf --object-streams=disable
      * Read this to understand what is going on: <http://qpdf.sourceforge.net/files/qpdf-manual.html#ref.filtered-streams>
   * After using this, you will be able to scan through or search the content of the .pdf in a generally plaintext format
      * You can even search for the words "Embeddedfile" ;)
      
No matter what you use, if you scan through you will find a very odd lump of base64 text. Anytime you find base64 text in a CTF, you're probably on the right path. You still need to isolate the hex, so open whatever files you have right now in vim, go to the bottom of the base64 ("==") and use 'VGx' to enter Visual, go to End of File, delete. To go the other way get to the top of the base64 and use 'Vggx' to delete from the current position to the beginning of the file.

Use the magic of base64 -decode (ensure to trim out new line characters, /n) to convert the base64 into a new file. 

From there, run file on it to see what it is. 

Boom! A png! 

Now, what do we do when we get a new file while doing a Forensics CTF??? We hit it with Scalpel/binwalk!

Pop goes the weasel, there's a 7Zip archive. 

Inside the 7z is a file named secret.txt, cat'ing that gives us more base64, so what do we do? Same thing as the last base64! Open that file up and there it is, another meme. 

From there, we just hope there aren't 1000 layers to this (I've seen it before), so what do we do with a new file??? You guessed it, scalpel/binwalk.

What happened???? Alright, bit dissapointing. No new archives this time.

The next move for file analysis after scalpel/binwalk didn't work is to go open up the file in a hexeditor and look for something funky. This is where knowing file formats comes in big. Pull up that .png specification and go for an adventure. Scan through the headers, scan through the body, scan to the end.... 

and wouldn't you know it, there's some funky town going on at the backend, two more base64 files.... 

You can play around with them and try to decrypt/xor whatever, but this challenge is an example of why forensics is the worst. 

Because nobody could figure out what to do with these strings, the organizers had to drop a hint in chat (remember how I told you to always monitor chat?) telling people to use the tool "Free File Camouflage". It would have been basically impossible to figure that out independently, and the people who made the challenge should feel bad about making it a guessing game, as forensics is often accused of being.

On the plus side, you now know to use "Free File Camouflage" on this last image and what do you know, out pops a new file. Running 'file' on us lets us know it's an executable ELF file.

Momma taught me to blindly run executables given to me during CTF competitions, so here we gooooooo.... 

And out pops the flag. Bit of a downer at the end that we needed a hint, but at least you didn't waste time trying to figure out what worked, right?



W






  





