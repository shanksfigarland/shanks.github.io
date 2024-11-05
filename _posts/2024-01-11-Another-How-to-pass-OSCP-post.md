---
title: 'Another "How to pass OSCP" post'
layout: mypost
categories: [OSCP]
---

I [passed my OSCP last year](https://shanks.zip/posts/2023/10/06/finally-oscp.html) and I'll share some tips and advice because I'm seeing a lot of people failing over the last months and I'd be one of them if I didn't persevere and try everything until the last minute.

## <span style="color: #74C7D2">1. Don't panic if you get stuck</span>
I got stuck for the first 16 hours and managed to root all three standalones in the last ~6 hours of the exam. 

I manage to do that mainly because:

- I stepped aside and took a break. 
    - Go for a walk, drink some cold water... 
- I slept. 
    - Sleep is important and I could sleep a few times which made my brain *reset* and think a bit better. 


## <span style="color: #74C7D2">2. The exam is not "hard"</span>
Of course this is relative, but the exam is *not as hard as people make it to be*, it's just too **tricky**. 

It **WILL** play tricks with you unless you **SEE** them and are able to **distinguish** between a rabbit hole or not.


## <span style="color: #74C7D2">3. Do the labs correctly</span>
If you've done your part, did all the exercises, **did all the challenges (Medtech, Relia and OSCP sets)**, revised your notes/cheatsheet, and did some PG (if you can), you're all set.

- **DO NOT** skip the labs, they're a great resource. 
- Try **redoing** the labs with different **tooling** and different **paths**. 
    - Example: **[Rubeus](https://github.com/r3motecontrol/Ghostpack-CompiledBinaries/blob/master/Rubeus.exe)** first time, then the second time you use **[GetUserSPNs](https://github.com/fortra/impacket/blob/master/examples/GetUserSPNs.py)**, and so on.
- Be ready to use an alternative tool if one doesn't work out in the exam.

I redid all sets (OSCP A, B, C) at least 2 times trying out new paths, new tools and ways of doing it and making it easier. If you have the time, try it.

## <span style="color: #74C7D2">4. You're never ready</span>
We are never prepared but we gotta face the exam and try everything we can.

Do not wait until you're ready. You'll possibly *never*  be.


## <span style="color: #74C7D2">5. Don't give up</span>
- **DO NOT** give up until the last minute, there's always a way to do things.
- GOOGLE IT! Google everything you find, don't look only at the first results page, go through 2, 3, 4 pages if needed.


## <span style="color: #74C7D2">6. Learn your system</span>
- Learn to spot rabbit holes. 
- Read carefully **everything**. It's very EASY TO MISS SIMPLE STUFF. READ READ READ READ, didn't find anything? GO BACK AND READ AGAIN YOUR NMAP OR WHATEVER ELSE. READ MORE AND READ AGAIN, READ 10 TIMES.
- Know what's default or not in the system you're in. 
    - Go through Windows program's folder, check what's default there. Is there something unusual?
    - Enumerate manually, is there anything that you find weird? 
        - A file that's not supposed to have a version in it, is it worth checking?
        - This SUID binary is not supposed to be here, right?


## <span style="color: #74C7D2">7. Some general tips</span>

1. People overlook how taking breaks and sleep is important. 
Please sleep and take breaks. This helped me a ton on how to think and find the answers I needed.
2. Two days before my exam I did some PG boxes rated by the community, I did all **[Easy/Intermediate]** boxes.
3. Help the community: I'm active on Offsec Discord helping people on their challenges, this made me learn a lot by teaching.
4. Don't be afraid to ask, but learn how to do so. https://dontasktoask.com/
5. Have fun, try not to be nervous (easier said than done, I know) and enjoy the process!
6. It's not easy and don't beat yourself up if you can't make it on your first try, you're not any less for that. It's a tricky exam. Good luck!

I'm always helping on Discord, you can find me on Offsec Discord as **shanks**. 

Any questions hit me up on there. (I don't take DM's)

Happy journey!

![passing](/assets/img/passing.png)