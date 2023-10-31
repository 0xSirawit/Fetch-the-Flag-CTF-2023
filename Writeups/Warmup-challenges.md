# Beep64
## Description
<sup>Author: @soups71</sup><br>
Every CTF needs a base64 challenge... right?  

Download the file(s) below.
**Attachments:**  [chall.zip](/assets/files/chall.zip)
## Solution
1. unzip `chall.zip`
```bash
$ unzip chall.zip 
Archive:  chall.zip
  inflating: sine.wav 
```
2. I listen to `sine.wav`, It makes me think about the sound when I push the number on the old telephone. <br>It's called `DTMF (dual tone multi-frequency)`. <br>There is a tool to decode `DTMF`. [dtmf-decoder](https://github.com/ribt/dtmf-decoder)  
```bash
$ ./dtmf.py sine.wav > numbers.txt
$ cat numbers.txt 
8*44*33*333*555*2*4*444*7777*9999*33*777*666*7777*7*2*222*33*666*66*33*7777*7*2*222*33*9999*33*777*666*7777*7*2*222*33*666*66*33*7777*7*2*222*33*666*66*33*7777*7*2*222*33*9999*33*777*666*7777*7*2*222*33*6.....
```
3. The script to decode `T9 (Text input on 9 Keys)`:
```python
T9_Mapping = {2: "abc", 3: "def", 4: "ghi", 5: "jkl", 6: "mno", 7: "pqrs", 8: "tuv", 9: "wxyz", 0: " "}

f = open("numbers.txt", "r")
numbers = filter(None, ''.join(f.read().splitlines()).split("*"))
plain_text = ""
for i in numbers:
    offset = len(i) - 1
    plain_text += T9_Mapping[int(i[0])][offset]
print(plain_text)
```
after I ran this script. I got `theflagiszerospaceonespacezerospaceonespaceonespacezerospaceonespacezerozerospaceonespaceonespace.....`
4. Almost get the flag. Just write a bit more script to decode the message. <br>**Binary in alphabet form > Binary  > Base64 > Text**
```python
T9_Mapping = {2: "abc", 3: "def", 4: "ghi", 5: "jkl", 6: "mno", 7: "pqrs", 8: "tuv", 9: "wxyz", 0: " "}

f = open("numbers.txt", "r")
numbers = filter(None, ''.join(f.read().splitlines()).split("*"))
plain_text = ""
for i in numbers:
    offset = len(i) - 1
    plain_text += T9_Mapping[int(i[0])][offset]

#Translate binary in alphabet form to actual binary numbers '1' and '0'.
binary_text = plain_text[9:].split("space")
binary_result = ''
for j in range(len(binary_text)):
    if len(binary_text[j]) > 4:
        if 'zeroone' in binary_text[j]: binary_result += '0 1'
        if 'oneone ' in binary_text[j]: binary_result += '1 1'
        if 'zerozero' in binary_text[j]: binary_result += '0 0'
        if 'onezero' in binary_text[j]: binary_result += '1 0'
    else:
        if 'zero' in binary_text[j]: binary_result += '0'
        if 'one' in binary_text[j]: binary_result += '1'

#Translate binary to base64.
base64_text = ''.join([chr(int(binary, 2)) for binary in binary_result.split(' ')])

#Translate base64 to text.
import base64
flag = base64.b64decode(base64_text)
print(flag.decode())
```

> Flag: flag{0421a964add97ff041431e2418e64508}
---
# Quick Maths
## Description
<sup>Author: @soups71</sup><br>
To try and get better grades in math, I made a program that gave me timed quizzes. Funny thing is that as I got better at the questions in this test, I got worse grades on my math tests.  
  
**NOTE: Float answers are rounded to 1 decimal points.**  
**NOTE: And here's another twist... the answers to division questions depend on _integer division_ or _double division_. I.e.,**  
`3/5 = 0`  
`3/5.0 = .6`  

**Press the `Start` button in the top-right to begin this challenge.**
## Solution
```bash
$ nc challenge.ctf.games 31006

Welcome! To be honest, I am a Computer Science major but I was never any good at math in school. I seemed to always get Cs.  

Note to self: Round all answers to 1 decimal point, where applicable.

Do you want to give it a chance? (Y/n): Y
Awesome, good luck!
What is 68.1 * 69.8? 4753.38
Too Slow!!!
Good bye :(
```
I connect to `challenge.ctf.games`. It responds when I answer `too slow`. That means I need to write the script to answer it. I  use the tool call [pwntools](https://github.com/Gallopsled/pwntools) in python.
```python
from pwn import *
target = remote('challenge.ctf.games',32493)
target.recvuntil("(Y/n):")
target.sendline("y")
count = 0
# answer 100 questions.
for j in range(100): 
    print(target.recvline().decode())
    x = target.recvuntil(b"?").decode().split()
    print(x)
    #Make this conditional because of division problem.
    if '.' not in ''.join(x):
        x[-3] = int(x[-3])
        x[-1] = int(x[-1][:-1])
        if x[-2] == '+':
            target.sendline(str(round(x[-3]+x[-1], 1)))
        if x[-2] == '*':
            target.sendline(str(round(x[-3]*x[-1], 1)))
        if x[-2] == '-':
            target.sendline(str(round(x[-3]-x[-1], 1)))
        if x[-2] == '/':
            print(x[-3],"/",x[-1],str(round(x[-3]//x[-1], 1)))
            target.sendline(str(round(x[-3]//x[-1], 1)))
    else:
        x[-3] = float(x[-3])
        x[-1] = float(x[-1][:-1])
        if x[-2] == '+':
            target.sendline(str(round(x[-3]+x[-1], 1)))
        if x[-2] == '*':
            target.sendline(str(round(x[-3]*x[-1], 1)))
        if x[-2] == '-':
            target.sendline(str(round(x[-3]-x[-1], 1)))
        if x[-2] == '/':
            print(x[-3],"/",x[-1],str(round(x[-3]/x[-1], 1)))
            target.sendline(str(round(x[-3]/x[-1], 1)))
    count +=1
    print(count)
print(target.recvline().decode())
print(target.recvuntil(b"}").decode())
target.interactive()
```

after I ran the script. Got it!
```
Great job, you just answered 100 math problems! You might not fail the next math test. Here is your reward!!!
flag{77ba0346d9565e77344b9fe40ecf1369}
```

> Flag: flag{77ba0346d9565e77344b9fe40ecf1369}
---
# Finders Keepers
## Description
<sup>Author: @JohnHammond</sup><br>
Patch found a flag! He stored it in his home directory... should be able to keep it?

Press the Start button in the top-right to begin this challenge.
Connect with:
	`Password is "userpass"
	`ssh -p 31380 user@challenge.ctf.games
## Solution
1. connect ssh `ssh -p 31380 user@challenge.ctf.games` with `"userpass"` password.<br>`user@finders-keepers-b930cde70263f2f9-58d4447d9d-zwqt7:~$ `
2. According to the description, There is a flag in `patch/` home directory. So I need to find commands that's in `'patch' group` to access files inside.
```bash 
user@finders-keepers-b930cde70263f2f9-58d4447d9d-zwqt7:~$ find / -group patch -type f 2>/dev/null
/usr/bin/find
/home/patch/.bash_logout
/home/patch/.profile
/home/patch/.bashrc
/home/patch/flag.txt
```
3. I found **`find`** command that can access `flag.txt`. I run this command to read `flag.txt`.
```bash 
user@finders-keepers-b930cde70263f2f9-58d4447d9d-zwqt7:~$ find . -exec /bin/cat /home/patch/flag.txt \;
flag{e4bd38e78379a5a0b29f047b91598add}
```

> Flag: flag{e4bd38e78379a5a0b29f047b91598add}
---
