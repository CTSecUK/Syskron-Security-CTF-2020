# Stolen Licenses

![Category](http://img.shields.io/badge/Category-Wednesday-orange?style=for-the-badge) ![Points](http://img.shields.io/badge/Points-300-brightgreen?style=for-the-badge)

![Tag](https://img.shields.io/badge/Tag-ocr-blue?style=plastic) ![Tag](https://img.shields.io/badge/Tag-checksum-blue?style=plastic)

## Details

![Details](images/stolen_licenses_details.png)








[https://www.geeksforgeeks.org/luhn-algorithm/](https://www.geeksforgeeks.org/luhn-algorithm/)

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

```
[jaxigt@MBA stolen_licenses]$ python3 test.py /home/jaxigt/ctf/syskron/stolen_licenses/img/
File (288 - B999582-0112.png): Contains a valid key: ['78124512846934984669']
1000 Files checked / 1 valid keys found!

```

So there we have our flag (in a slightly different format this time): **78124512846934984669**
