# **A High-Level Look at How Kasada Bot Protection Works**
A conceptual look at modern web protection technologies 

### What does Kasada mean?
Kasada is a bot protection system used by big websites like PlayStation, Sony, and Ticketmaster to keep automated access from happening. Kasada doesn't just limit the number of requests; it also uses advanced client-side challenges to tell humans from bots.

When you try to get to an API that Kasada protects:

```python
response = requests.post('https://protected-site.com/api/endpoint') 
print(response.status_code) 

# Output : 429/403 Not allowed
```

You get stuck. Not because of your IP address or user agent, but because you don't have any digital evidence that you are a real client.

---

### Main Ideas

**The Unique Headers**
Kasada-protected requests have custom headers that show the client is real:

- **x-kpsdk-ct** : Token for the client (CT)
- **x-kpsdk-cd** : Client Data (CD) - has answers to proof-of-work questions
- **x-kpsdk-h** : signature checking that CT and CD match
- **x-kpsdk-v** : Identifier for the version
- **x-kpsdk-r** : Request ID

```text
The Three-Layer Defense: 

Layer 1: is the p.js Script

Kasada puts a JavaScript file (p.js) into pages that are protected.
- VM bytecode makes this script very hard to read. Has a full virtual machine that can run encrypted bytecode. It is very hard to reverse engineer this.

Layer 2: Challenges for Proof of Work

- The script gets cryptographic puzzles from Kasada servers.
- The client has to solve all of these issues, which are like crypto mining.
- The CD token has solutions in it.
- The server checks to make sure the answers are right.

Layer 3: Token Features

- CT Token: costs a lot to make, lasts for 30 minutes, and can be used again
- CD Token: It's cheaper to make, can only be used once, and has to be new.
- HMAC Signature: connects CT and CD, stops tokens from being mixed up or changed
```

### Why easy fixes don't work and why copying headers doesn't work

**The tokens have:**
- Timestamps that have to be new
- Solutions to challenges that have to match puzzles given by the server
- HMAC signatures that prove that a token is real
- Unique IDs tracked on the server to stop reuse

**TLS fingerprinting alone isn't enough**
Kasada does check tls fingerprints, but even if you have a perfect chrome fingerprint, you still need:
- Tokens for CT and CD that are valid
- Proof-of-work answers
- Fix HMAC signatures

### The Problem of Reusing CDs
This is the most important point:

**Normal flow:**
1. The browser sends a request
2. KPSDK adds headers for CT and CD
3. The server got the request
4. The server checks and marks the CD as "used."
5. The CD token is now used up.

If you get tokens from a completed request, the CD is already used up. You can't use it again.

---

### **The Life Cycle of a Token**

**Making CT Tokens:**
- Made when the page first loads
- Has fingerprint data from clients
- Costly to figure out
- Good for about 30 minutes
- Same CT used for more than one request

**Making CD Tokens:**
- Unique for each order
- has a time stamp and challenge responses
- single-use calculations are cheaper.
- Must be new (typically under 5 seconds)

**This signature shows:**
- CT and CD are both from the same session.
- There has been no tampering with the tokens.
- The request is real.

---

### Standard method (doesn't work):
1. Make a request, KPSDK adds tokens, and your request is finished.
2. Get tokens from the request that was finished
3. CD has already been used 

**Strategy for Caching can be used on CT only**
- CT is expensive but can be used again, while CD is cheap but can only be used once.

==========================================

### How p.js Protection Works:-

There are several layers of obfuscation in the Kasada script:
- **VM Obfuscation**: The core logic is compiled to bytecode that is run by the embedded VM.
- **String Encryption**: All strings are encrypted and decrypted while the program is running.
- **Flattening Control Flow**: The flow of code is made non-linear and hard to follow.
- **Dead Code Injection**: Code paths that don't really run
- **Variable Name Mangling**: All variables are given names that don't mean anything.

==========================================

## System of Proof of Work:
The CD token has answers to cryptographic problems:

## Structure of the Challenge:
The server gives the puzzle parameters, such as difficulty, prefix, and so on. The client has to find solutions, like crypto mining. CD token has solutions in it. The server checks that the solutions are correct for the challenge. These solutions are in the "answers" array in CD. 

## Validating the Timestamp:
CD must be new (usually less than 5 seconds old)
Stops old tokens from being used in replay attacks

## Validation of Uniqueness:
Server-side tracking keeps track of each CD's unique ID.
Once used, it is marked as consumed. stops the same CD from being used again.

---

### Get in Touch
Did you find this interesting? Do you have any questions? Want to work together?

⭐ If you learned something, give the repo a star.
💬 Start a conversation to share ideas Make things better

But most importantly, be ethical and responsible with this information. For more information, please contact **@Nixnode** on Telegram.