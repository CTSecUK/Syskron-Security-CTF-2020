# Security Headers

![Category](http://img.shields.io/badge/Category-Monday-orange?style=for-the-badge) ![Points](http://img.shields.io/badge/Points-100-brightgreen?style=for-the-badge)

![Tag](https://img.shields.io/badge/Tag-web-blue?style=plastic)

## Details

![Details](https://github.com/CTSecUK/Syskron-Security-CTF-2020/blob/main/Write-ups/images/security_headers_details.png)

I started by navigating to the webpage in Firefox, i then enabled the proxy(using the extension FoxyProxy) so i could direct the traffic through BurpSuite. 

![Image](https://github.com/CTSecUK/Syskron-Security-CTF-2020/blob/main/Write-ups/images/security_headers_send_to_burp.png)

I also made sure intercept was enabled so i could see the request and response to "www.senork.de", i went back to Firefox and refereshed the page, went back to Burp and clicked on Forward.

![Image](https://github.com/CTSecUK/Syskron-Security-CTF-2020/blob/main/Write-ups/images/security_headers_burp_request.png)

I then clicked on **HTTP History** and scrolled down till i could find the request to **GET /**

![Image](https://github.com/CTSecUK/Syskron-Security-CTF-2020/blob/main/Write-ups/images/security_headers_burp_response.png)

I checked the **Response** window and **RAW** tab and looked at the Headers.

Flag: ***syskronCTF{y0u-f0und-a-header-flag}***
