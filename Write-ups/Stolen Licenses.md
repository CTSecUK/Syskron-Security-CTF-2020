# Stolen Licenses

![Category](http://img.shields.io/badge/Category-Wednesday-orange?style=for-the-badge) ![Points](http://img.shields.io/badge/Points-300-brightgreen?style=for-the-badge)

![Tag](https://img.shields.io/badge/Tag-ocr-blue?style=plastic) ![Tag](https://img.shields.io/badge/Tag-checksum-blue?style=plastic)

## Details

![Details](images/stolen_licenses_details.png)



```python
import sys
import tesserocr
from PIL import Image

image_file = sys.argv[1]

image = Image.open(image_file)
print(tesserocr.image_to_text(image))
```

```
[jaxigt@MBA stolen_licenses]$ python ocr.py img/B999582-0001.png 
libpng warning: iCCP: known incorrect sRGB profile
R i

SW serial number

activation key

only valid if purchased together with a machine



``` 

A stack overflow post for a simialr OCR python library [https://stackoverflow.com/questions/55994807/pytesseract-fail-to-recognise-digits-from-image](https://stackoverflow.com/questions/55994807/pytesseract-fail-to-recognise-digits-from-image)

suggested adding the following code to optimise the processsing of the image;

```python
import cv2
from PIL import Image

img = cv2.imread('gradient.png')
# If your image is not already grayscale :
# img = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
threshold = 180 # to be determined
_, img_binarized = cv2.threshold(img, threshold, 255, cv2.THRESH_BINARY)
pil_img = Image.fromarray(img_binarized)
```

The updated code now looks like this;

```python
import sys
import cv2
import tesserocr
from PIL import Image

image_file = sys.argv[1]

img = cv2.imread(image_file)
img = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
threshold = 180 # to be determined
_, img_binarized = cv2.threshold(img, threshold, 255, cv2.THRESH_BINARY)
pil_img = Image.fromarray(img_binarized)

print(tesserocr.image_to_text(pil_img))
```

Running this new code we now see an improved OCR output;

```
[jaxigt@MBA stolen_licenses]$ python ocr.py img/B999582-0001.png 
L I IV TR WAL

 

)
) =
SW serial number .

B999582-0001

o)V [ufe] sl 5%

78121994415279564775

' 4

 

e Ui


```

Here we can see the serial key listed in the output.

Next we need to add and tweak some code to extract the key from the output using "**regular expressions**.; 

A quick look through some of the image files confirms that they all begin with the same sequemce of numbers, so lets use that to find the string we wish to extract;

```python
import sys
import cv2
import re
import tesserocr
from PIL import Image

image_file = sys.argv[1]

img = cv2.imread(image_file)
img = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
threshold = 180 # to be determined
_, img_binarized = cv2.threshold(img, threshold, 255, cv2.THRESH_BINARY)
pil_img = Image.fromarray(img_binarized)

img_ocr_data = tesserocr.image_to_text(pil_img)
pattern = r"^7812\d+"
multiline = re.compile(pattern, re.MULTILINE)
license_key = multiline.findall(img_ocr_data)

print(license_key[0])
```

Now when we run the script we see just the 

```
[jaxigt@MBA stolen_licenses]$ python ocr.py img/B999582-0001.png 
78121994415279564775
```

So now we need to make it perfom this function on every fiule in the img directory.

```python
import sys
import cv2
import re
import os
import tesserocr
from PIL import Image

dir_path = sys.argv[1]

for file_name in os.listdir(dir_path):

    img = cv2.imread(dir_path + "/" + file_name)
    img = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    threshold = 180 # to be determined
    _, img_binarized = cv2.threshold(img, threshold, 255, cv2.THRESH_BINARY)
    pil_img = Image.fromarray(img_binarized)

    img_ocr_data = tesserocr.image_to_text(pil_img)
    pattern = r"^7812\d+"
    multiline = re.compile(pattern, re.MULTILINE)
    license_key = multiline.findall(img_ocr_data)

    print(license_key[0])

```

This works perfectly... but i stop it before it processes the 1000 files in the directory!

```
[jaxigt@MBA stolen_licenses]$ python ocr.py /home/jaxigt/ctf/syskron/stolen_licenses/img
78123683757359944769
78124972664118984564
78129129384587213955
78129529239355741238
78127895886947342345
78123422121896614143
78129543851214852447
78129631245481594161
^CTraceback (most recent call last):
  File "ocr.py", line 18, in <module>
    img_ocr_data = tesserocr.image_to_text(pil_img)
KeyboardInterrupt
```

Ok, so the final piece of the puzzle is running the Lunh algorithm check to determine if the keys are valid.

I must admit i spent some time trying various PyPi implimentations which didn't work correctly before stubling aggross this site;[https://www.geeksforgeeks.org/luhn-algorithm/](https://www.geeksforgeeks.org/luhn-algorithm/).

Not only didi it explain in an an easy to follow way how the algorithm works... but also helpfully included some code to implement it into our script!

I added the code and our python script now looks like this;

```python
import sys
import cv2
import re
import os
import tesserocr
from PIL import Image

dir_path = sys.argv[1]

i=0
j=0

def verifyLuhn(numberstring):
     
    nDigits = len(numberstring)
    nSum = 0
    isSecond = False
     
    for i in range(nDigits - 1, -1, -1):
        d = ord(numberstring[i]) - ord('0')
     
        if (isSecond == True):
            d = d * 2
  
        nSum += d // 10
        nSum += d % 10
  
        isSecond = not isSecond
     
    if (nSum % 10 == 0):
        return True
    else:
        return False


for file_name in os.listdir(dir_path):
	
	i += 1
	#print("Opening file: " + file_name)

	img = cv2.imread(dir_path + file_name)
	img = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
	threshold = 180 # to be determined
	_, img_binarized = cv2.threshold(img, threshold, 255, cv2.THRESH_BINARY)
	pil_img = Image.fromarray(img_binarized)

	img_ocr_data = tesserocr.image_to_text(pil_img)
	pattern = r"^7812\d+"
	multiline = re.compile(pattern, re.MULTILINE)
	license_key = multiline.findall(img_ocr_data)

	if verifyLuhn(license_key[0]):
		j += 1
		print("File (" + str(i) + " - " + str(file_name) + "): Contains a valid key: " + str(license_key))

print(str(i) + " Files checked / " + str(j) + " valid keys found!")
```

Running the script... which does take a bit of time (given its performing OCR on 1000 image files!) proced the velow output;

```
[jaxigt@MBA stolen_licenses]$ python3 ocr.py /home/jaxigt/ctf/syskron/stolen_licenses/img/
File (288 - B999582-0112.png): Contains a valid key: ['78124512846934984669']
1000 Files checked / 1 valid keys found!

```

So there we have our flag (in a slightly different format this time): **78124512846934984669**
