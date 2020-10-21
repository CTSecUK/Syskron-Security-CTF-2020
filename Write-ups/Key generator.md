# Key Generator

![Category](http://img.shields.io/badge/Category-Wednesday-orange?style=for-the-badge) ![Points](http://img.shields.io/badge/Points-300-brightgreen?style=for-the-badge)

![tag-reverse-engineering](https://img.shields.io/badge/Tag-reverse-engineering-blue?style=plastic)

## Details

After downloading and extracting the file we see a .db extension, running the file command on it confirms it's a SQLLite Database.

![Run Invalid](images/keygen_run_keygen_invalid_input.png)

![Run valid](images/keygen_run_keygen_valid_input.png)

![Radare2 Analyse](images/keygen_r2_step1.png)

![View Main Function](images/keygen_r2_step1.png)

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

