You are given the file [Mandiant.pdf](https://github.com/hoppersroppers/ctfWriteups/blob/main/CSAW15/FOR300-Mandiant/Mandiant_c920fc463eaf996489749457abc9b2eb.pdf). It's a long, legit looking file, so you probably don't waste your time reading through it (or do, it's interesting). Most of the time you get legit looking files for high points like this (300 points in a hard competition) it won't require reading 70 pages looking for a secret string. Most of the time.

First step, like any file format challenge, is to run your favorite scan tools on it like binwalk or scalpel and look for extra files that pop out. That's right, we are talking file pinatas again. 

After that we look for any anomalies in the file format. The time it takes for you to identify this is based off of your tools available, your knowledge of the file format, and luck, in that order. 

* If your tool can find it for you, that is always the best option
* If you can look at a file's specification, check the headers and know what is happening, that also works, but requires a lot more knowledge
* If you are going with luck, you will find the answer on a long enough timeline, but it will be painful
   * If you are going with luck, make sure to document what you do well. That way you won't duplicate your own work, or if someone else tries to help you, they won't have to try the same guesses you did.
   * I like Google Sheets for collaborative notetaking, try to force your teammates to do this and you'll all get better

In this case, it's a PDF, and plenty of tools exist. I'm a fan of pdf-parser <https://blog.didierstevens.com/programs/pdf-tools/>. It comes pre-installed on some distros like Kali, others you'll need to get it from here <https://www.aldeid.com/wiki/Pdf-parser>. If you don't have pdf-parser available, you can also try to muddle through reading specs (cough [PDF spec](https://www.adobe.com/content/dam/acom/en/devnet/acrobat/pdfs/PDF32000_2008.pdf) 7.11.4) or using other tools, but it will take a bit longer. 

What speed you find abnormalities will be determined by the tools you use, your skill with them, and your understanding of the file formats that are flying in front of your face. This goes for all file formats and forensics problems, and in this case, eventually you will see there is a file embedded in a stream inside the given .pdf. 

Using your knowledge of PDF tools, you can go with:

* pdf-parser: this is somewhat straight forward, use the command $ pdf-parser --object 3 --raw --filter Mandiant.pdf > out as demonstrated in <https://github.com/krx/CTF-Writeups/blob/master/CSAW%2015%20Finals/for300%20-%20Mandiant/README.md>
* If you don't have pdf-parser, you are still likely to have qpdf. Use of qpdf is shown here: <https://github.com/ctfs/write-ups-2015/tree/master/csaw-finals-ctf-2015/forensic/mandiant>
   * qpdf is able to pull streams (and other parts of the complicated pdf file format) into plaintext for easy parsing using the command $ qpdf --qdf --object-streams=disable
      * Read this to understand what is going on: <http://qpdf.sourceforge.net/files/qpdf-manual.html#ref.filtered-streams>
   * After using this, you will be able to scan through or search the content of the .pdf in a generally plaintext format which makes life easier
      * You can even search for the words "Embeddedfile" ;)
      
No matter what tool you use, if you scan through you will find a very odd lump of base64 text. Anytime you find base64 text in a CTF, you're probably on the right path. You still need to isolate the hex, so do that in whatever way feels right.

I like to open whatever file it is in vim, go to the bottom of the base64 ("==") and use 'VGx' to enter Visual, go to End of File, and delete. To clear the other way get to the top of the base64 and use 'Vggx' to delete from the current position to the beginning of the file. This leaves you with just the text you need.

Use the magic of base64 -decode and pipes (ensure to trim out new line characters with tr -d '\n') to convert the base64 text into a new file. 

From there, run file on it to see what it is. 

Boom! A png! 

Now, what do we do when we get a new file while doing a Forensics CTF??? We hit it with Scalpel/binwalk and hope it's a pinata!

Pop goes the weasel, there's a 7Zip archive. 

Inside the 7z is a file named secret.txt, cat'ing that gives us more base64, so what do we do? Same thing as the last base64! Open that file up and there it is, another meme. 

From there, we just hope there aren't 1000 layers to this (a common CTF scripting challenge), so what do we do with a new file??? You guessed it, scalpel/binwalk.

What happened???? Alright, bit dissapointing. No pinata this time.

The next move for file analysis after scalpel/binwalk doesn't work is to go open up the file in a hexeditor and look for something funky. This is where knowing file formats comes in big. Pull up that .png specification and go for an adventure. Scan through the headers, scan through the body, scan to the end.... 

and wouldn't you know it, there's some funky town going on at the backend, two more base64 strings! This time was actually pretty obvious, usually it is way harder and requires a much stronger understanding of the target file format to "see weirdness".

You can play around with them and try to decrypt/xor whatever, but this challenge is an example of why forensics is the worst. 

Because nobody could figure out what to do with these strings, the organizers had to drop a hint in chat (remember how I told you to always monitor chat?) telling people to use the tool "Free File Camouflage". It would have been basically impossible to figure that out independently, and the people who made the challenge should feel bad about making it a guessing game, as forensics is often accused of being.

On the plus side, you now know to use "Free File Camouflage" on this last image and what do you know, out pops a new file. Running 'file' on us lets us know it's an executable ELF file.

Momma taught me to blindly run executables given to me during CTF competitions, so here we gooooooo! You'll have to change perms to get it to execute and if you 777 it I'll hop through this monitor so help me...

And out pops the flag. Bit of a downer at the end that we needed a hint, but at least you didn't waste time trying to figure out what worked, right?

Now goes submit a screenshot and move on to the next challenge!

- Dennis

<https://academy.hoppersroppers.org/mod/assign/view.php?id=575&forceview=1>








  





