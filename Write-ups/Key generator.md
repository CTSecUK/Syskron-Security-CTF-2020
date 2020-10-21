# Key Generator

![Category](http://img.shields.io/badge/Category-Wednesday-orange?style=for-the-badge) ![Points](http://img.shields.io/badge/Points-300-brightgreen?style=for-the-badge)

![Tag](https://img.shields.io/badge/Tag-reverse%20engineering-blue?style=plastic)

## Details

![Details](images/keygen_details.png)

After downloading, we'll first run the binaryv to try and understand what it is doing. 

It seems to ask for some input (a machine number) and then returns if its valid.

![Run Invalid](images/keygen_run_keygen_invalid_input.png)

Running it again and entering the suggested example "**machine number**" we can see a differnet output.

![Run valid](images/keygen_run_keygen_valid_input.png)

Ok lets open this up in radare2 and analyse it!

We start by running "**aaa**" to analyse and autoname functions.

Next we run "**afl**" to list the discovered functions.

Here we can see the function "**main**" which is usualy a good place to start, so we use the command "**s main**" to seek to main and the "**VV**" to open visual view. 

(If your view doesnt look like the below press the "**P**" key to cyle through the visual display options.

![Radare2 Analyse](images/keygen_r2_step1.png)

Ok so lets start looking through the main function to see if we can understand whats going on.

![View Main Function](images/keygen_r2_step2.png)

It looks as though lots of information is being output using the "**puts**" function before receiving an input. 

It then calls the function "**sym.imp.strlen**" agains the value that has been input and then compares to see if it is equal to 7 (characters long).

If it is, it jumps to another part of the code.

> So this now explains how the **"Valid machine Format"** check is taking place!

![strlen](images/keygen_r2_strlen.png)

Next we see a call to a function called genserial, so lets take a closer look at that by typing "**:**" and entering "**s sym.genserial**" before hitting  the Enter key (twice).

![call genserial](images/keygen_seek_genserial.png)

Now lets examine this funtion!

Looking more closely and steping through the function we can see at address **0x0000123fa** a value is moved to "**rax**".

Radare2 helpfuly tells us the string for this value to the right of it ; "**!!aksal**".

![genserial function](images/keygen_genserial.png)

A bit further down we can see that the funtion "**sym.strrev**" is called before calling the "**sym.imp.strcmp**" function. 

We then Jump to another part of the code if the compariosn is equal (**je 0x138c**).

![genserial reverse and compare](images/keygen_genserial_JE.png)

With the information we now have, I think we should try to run the program again with some different input!

Running again and entering the reversed value of the string "**!!aksal**" we saw in the assembly code before ("**laska!!**") gives us a new output!

![Run correct](images/keygen_run_keygen_valid_correct.png)

Trying this string as the flag will return a failure, but the program does tell you "**You are not done yet! ಠ‿ಠ**"

Back in Radare2 though, there is another clue to what might be going on here. 

After returning from the **genserial** function another call is made to a function called **octal**.

![genserial reverse and compare](images/keygen_genserial_call_octal.png)

We could continue to dissassemble and try to understand how this function works but before we go down that path, lets instead see if we can do anything with the new string we've been given. 

```
1639171916391539162915791569103912491069173967911091119123955915191639156967955916396391439125916296395591439609104911191169719175
```

Lets jump over to an online Ascii Conversion Tool, [dcode.fr](https://www.dcode.fr/ascii-code).

Putting the string into the input box and hitting the "Decrypt/Convert" button shows the flag on the left and sure enough, the Ascii conversion method is listed as "**OCT (N Digits)**" as was hinted at by the function in Radare2.

![genserial reverse and compare](images/keygen_ascii_dcode.png)

So that completes this challenge, the flag for which is **syskronCTF{7HIS-isn7-s3cUr3-c0DIN9}**

