---
layout: post
title: 2021-11-14-HKCERT-CTF2021-BWSI
---

This is my first CTF writeup ;)
![image](https://user-images.githubusercontent.com/94167587/141765929-ed49c543-7d05-4ef2-abd7-f4d5cd423d4a.png)

Invited to the **HKCERT CTF 2021**, although shamefully didn't have time to fully participate in the competition, I am happy to try out one of the CTF questions written by an old friend, which happens to be my high school frd. Nice chap by the way XD

![image](https://user-images.githubusercontent.com/94167587/142050576-c7a1846b-5a77-41f6-a6ac-8eee8fa4e4ad.png)



# Starting wrongly

![image](https://user-images.githubusercontent.com/94167587/141700134-cc95a816-959c-44fb-a775-fa301abf658b.png)


Given my background, I started out wrongly by performing an Nmap scan and directory listing (gobuster) on the dedicated server without looking at the hint of this CTF question. I even attempted opened my Burp Suite Professional to inspect the Request and Responses sent to this .php written server (while contemplating what bruteforcing attack I can initiate cough...coughc.... very wrong) 

# Hints

The hint itself is actually the source code of the login.php, which sets out the criteria of the good password that should be filled in to solve this challenge. According to the background of this challenge, this challenge has already appeared in other public CTF competitions before. 
![image](https://user-images.githubusercontent.com/94167587/141700302-1c35acb5-7ab3-444c-9e72-97b7a259b0f0.png)


So if we break down the source code bits by bits, we will first know the correct username is "**hkcert**" while the rest is the criteria the password should meet, for example, the inputs should not exceed 24 characters, the inputs should not carry special characters etc. 

### the inputs should not exceed 24 characters
```
if($_SERVER["REQUEST_METHOD"] == "POST"){

    if ((strlen($_POST["username"]) > 24) or strlen($_POST["password"]) > 24) {
        header("location: https://www.youtube.com/watch?v=2ocykBzWDiM");
        exit();
    }
```

### Special characters not allowed 
Where [**!ctype_alnum**](https://www.php.net/manual/en/function.ctype-alnum.php) is the hint 
```
$password = trim($_POST["password"]);
            if (!ctype_alnum(trim($_POST["password"])) or !ctype_alnum(trim($_POST["username"]))) {
                switch ( rand(0,2) ) {
                    case 0:
                    header("location: https://www.youtube.com/watch?v=l7pP3ydt3tU");
                    break;
                    case 1:
                    header("location: https://www.youtube.com/watch?v=G094II5gIsI");
                    break;
                    case 2:
                    header("location: https://www.youtube.com/watch?v=0YQtsez-_D4");
                    break;
                    default:
                    header("location: https://www.youtube.com/watch?v=2ocykBzWDiM");
                    exit();
```
# Magic Hash
Next, when exploring the rest of the codes will reveal what composes the key to this challenge.
Here then comes the second hints regarding the used password at **./used_pw.txt** where it revealed the password used by other participants. Now I know it sounds cheating to look at other participants' used password so let's take a second look of the codes, you'll notice that while the others notations are using "===", the only exception is the "==" at **if( hash('md5', $password) == 0** which is the hint with respect to the [Magic Hashes](https://www.whitehatsec.com/blog/magic-hashes/). 

To have a better understanding of this, we must first know what does magic hash means to the PHP language. Magic hash is a PHP vulnerability discovered by Robert Hansen of WhiteHat Security. When "!=" or "==" operators were being utilized to compare hashes, PHP interprets every hashed value that begins with the ‘0e’ with 0 as the value for it. Now the string itself comes in a form of scientific notation [(E Notation)](https://stattrek.com/statistics/dictionary.aspx?definition=e-notation) and accordingly it will be interpreted as the **zero to the power of something**, which equates zero as well. If our hashed value of a password equals to a string with the same scientific notation (i.e. starting with a 0e that eventually equates to zero), then the returned value will be **0** and pass the checks (which requires zero as well on the right hand side of the equation)


*So what does it mean in security / infosec?*
*I found an article back in 2015 which is of a good reference - [PHP hash comparison flaw is a risk for million users](https://securityaffairs.co/wordpress/36732/hacking/php-hash-comparison-flaw.html)*

*"==" or "!=" are often refered as **Loose comparison** / [PHP Magic Tricks: Type Juggling](https://owasp.org/www-pdf-archive/PHPMagicTricks-TypeJuggling.pdf) where it can result in the application returning an unintended answer to the true or false statement, and can lead to severe authorization and/or authentication bugs. And therefore nowadays, **Strict comparisons** (i.e. ===) are mostly encouraged.*


Back to the challenge, so what we actually need here as the input of the password is the md5 of the password that equates to zero - i.e. the password has to be a magic hash.

However, this is NOT a normal magic hash you can find on the Public Internet

If you look at the source code again, the second criteria that comes after && is **substr($password,0,strlen('hkcert')) === 'hkcert')** where the password which resolves to a magic hash must begin with "**hkcert**" as well.
```
 if ($username === 'hkcert') {
        if( hash('md5', $password) == 0 &&
            substr($password,0,strlen('hkcert')) === 'hkcert') {
            if (!exec('grep '.escapeshellarg($password).' ./used_pw.txt')) {

                $_SESSION["loggedin"] = true;
                $_SESSION["username"] = $username;

                $myfile = fopen("./used_pw.txt", "a") or die("Unable to open file!");
                fwrite($myfile, $password."\n");
                fclose($myfile);
                header("location: welcome.php");

            } else {
                $login_err = "Password has been used.";
            }

        } else {
            $login_err = "Invalid username or password.";
        }
    } else {
        $login_err = "Invalid username or password.";
    }
}
?>
```
# Expecto Patronum! - Casting our own spell
Now we know what to do, we have to write our own script to bruteforce a magic hash with "hkcert" at the beginning. 
However, when writing the python script, I made a careless mistake by producing a wrong magic hash, here is my original script:

```
import string
from random import *
from hashlib import md5
import hashlib
import re

ctf = "hkcert"
characters = string.ascii_letters + string.digits
while True:
    password =  "".join(choice(characters) for x in range(randint(8, 10)))

    newvalue = ctf+password

 
    h = hashlib.md5(newvalue.encode('utf-8')).hexdigest()
    print(h)
    verifyVar = re.match("0+e[0-9]+", h)
    if verifyVar:
        print(newvalue)
    
        break
            
```
Can you spot the problems? 
First, to bruteforce, we should not use randomize but instead try and error one by one in order in favor the concept of probability (permutations)
Second, the characters after "0e" of  magic hash has to be digits only as well
Lastly, let's see the amended script and produce a good magic hash


```
import string
from random import *
from hashlib import md5
import hashlib
import re

ctf = "hkcertScottish"

for p1 in range(1,10000000000):
    pwd = str(p1)
    print('pwd:' + pwd)
    newvalue = ctf+pwd
    print(newvalue)
    h = hashlib.md5(newvalue.encode('utf-8')).hexdigest()
    print(h)
    if h[0:2]=="0e" and h[2:].isdigit()==True:
        print(newvalue)    
        break
```

# Flag
Depends on luck, at the time of writing and doing this challenge, since I was moving house to Edinburgh, so I picked up a wrong word to append to hkcert (in order to avoid computing the same reused magic hashes used already by other participant) for bruteforcing a tailored magic hash...took forever but voila!

![image](https://user-images.githubusercontent.com/94167587/141701265-18a10f2a-cc70-4313-b804-c872d267cdbc.png)

Special thanks to [poorme](https://github.com/poormemememe/poorme.github.io/blob/main/myfirstwriteup.md) and TPY :-) 

Happy CTF 
