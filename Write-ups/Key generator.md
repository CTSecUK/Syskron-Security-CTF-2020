# Key Generator

![Category](http://img.shields.io/badge/Category-Wednesday-orange?style=for-the-badge) ![Points](http://img.shields.io/badge/Points-300-brightgreen?style=for-the-badge)

![Tag](https://img.shields.io/badge/Tag-reverse-engineering-blue?style=plastic)

## Details

![Details](images/keygen_details.png)

After downloading we first erun the binaryv to try and understand what it is doing. It seems to ask for some input (a machine number) and then returns if its valid.

![Run Invalid](images/keygen_run_keygen_invalid_input.png)

Running it again and entering the suggested example "Machine Number" we can see a differnet output.

![Run valid](images/keygen_run_keygen_valid_input.png)

Ok lets open this up in radare2 and analyse it!
We start by running "**aaa**" to analyse and autoname functions.
Next we run "**afl**" to list the discovered functions.
Heer we can see the function "**main**" which is usualy a good place to start, so we use the command "**s main**" to seek to main and the "**VV**" to open visual view. (if your view doesnt look like the below press the "**P**" key to cyle through the visual display options.

![Radare2 Analyse](images/keygen_r2_step1.png)

Ok so lets start looking through the main function tio see if we can understand whats going on.

![View Main Function](images/keygen_r2_step2.png)

It looks as though lost of information is being output using the "**puts**" function befor asking for an input and then calling the function "**sym.imp.strlen**" on that value. It appears the value is then compared to see if it is equal to 7 (characters long) before jumping to another part of the code if it is.
So that now explains how the **"Valid machine Format"** check is taking place!

![strlen](images/keygen_r2_strlen.png)


![call genserial](images/keygen_seek_genserial.png)

![genserial function](images/keygen_genserial.png)

![genserial reverse and compare](images/keygen_genserial_JE.png)

![Run correct](images/keygen_run_keygen_valid_correct.png)

Now back in radare there is another clue to what might be going on here, after returnign from the **genserial** function another clal is made to a function called **octal**.

![genserial reverse and compare](images/keygen_genserial_call_octal.png)

Rather than trying to dissassemble this funtion in Radare2, we instead can jump over to an online Ascii Conversion Tool, [dcode.fr](https://www.dcode.fr/ascii-code).
Putting the string into the inpit box and hitting decrypt  shows the flag on the left and sure enough, the Ascii conversion methos is listed as "**OCT (N Digits)**"
![genserial reverse and compare](images/keygen_ascii_dcode.png)


**syskronCTF{7HIS-isn7-s3cUr3-c0DIN9}**

