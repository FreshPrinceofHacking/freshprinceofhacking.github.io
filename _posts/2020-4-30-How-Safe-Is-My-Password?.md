---
title: "How Safe is my Password?"
categories:
  - Privacy, Security and Safety
header:
  image: assets/images/4eseqn.jpg
---

This question is probably one that is most asked when it comes to ensuring your various accounts are secure. Hopefully after reading this you'll have a good understanding on what good passwords look like, how hackers actually crack your passwords and how you can stay secure. 

### Password: password 

I think we can all agree that using "password" for your password is not a good idea. Most websites won't even let you use that password without throwing in some numbers and symbols. But how are hackers able to figure out your password? It seems every other month there is a major security breach at a company, and millions of peoples' information is compromised, including site passwords. How is the website actually storing these passwords for protection?

Are websites keeping the passwords in **plaintext** (the original form)? Are they encrypting them? Where are the passwords being held? These are important questions think about considering the vast amount of services you may have accounts with. It would be great if we knew passwords were stored in a manner that A) made them difficult to get to, and B) even if they were compromised, the hacker still couldn't decipher the passwords easily.

Assuming our database is secure, our passwords still need to be stored in a proper form to ease our concerns about a potential compromise. Encryption sounds like the right answer, but let's talk about hashing for a bit...

### Password: 5f4dcc3b5aa765d61d8327deb882cf99

If you can't tell what the password is, then good (Hint: it's 'password'). As a matter of fact, this is what your password is converted to when it is stored on a database. This gibberish is called a **hash**. A hash is an alphanumeric string that is a mathematical representation of a piece of data. Hash algorithms are **one-way functions** that will take in some input and output a string. There are various hashing algorithms in existence, and hashing has [many applications beyond just password security](https://www.geeksforgeeks.org/applications-of-hashing/). The above example is called an [MD5 hash](https://searchsecurity.techtarget.com/definition/MD5). 

Here is our example illustrated:
![hash]({{ site.url }}{{ site.baseurl }}/assets/images/hash.jpg)

Hashes have certain properities that they must abide by in order to be considered secure:

* Given a hash, it should be computationally difficult to decipher the original data. Remember, hashes are one way functions. There is no "dehash" method.
* Given a hash, it should be hard to find a different input with the same resulting hash. Hashing is unique in the fact that adding or subtracting even one 1 byte of data from the input can dramatically change the entire hash string output.

This is drastically different from encryption algortihms, which are **two-way functions** (encrypt and decrypt). Having encryption literally means the data can be decrypted. We don't want this for our password. We want to keep the password in a form that cannot be reversed in any way.

Encryption of our string:
![encrypting]({{ site.url }}{{ site.baseurl }}/assets/images/e1.jpg)

Decryption of our string:
![decrypting]({{ site.url }}{{ site.baseurl }}/assets/images/d1.jpg)

### Password: BruteForce/DictionaryAttack45@@@

The first property of hashing seems pretty straightforward. Nobody should be able to dicipher the orginal data given a hash string. However, while hackers can't reverse engineer a hashed password, they can be clever and **compare known hash values to their corresponding plaintext strings**. 

Let's say a hacker was able to obtain hundreds of hashed passwords from a database. The goal is to try to crack these passwords in order to obtain their plaintext form for a given username. Typical hacker business. The hacker sees the hash for 'password' (5f4dcc3b5aa765d61d8327deb882cf99) in the database. How do they crack it? 

One thing they could do is generate hashes in real time of every possible combination of letters/symbols. They then could compare the hash that they're trying to crack with one of the hashes they just generated. When it matches, they can see which plaintext combination caused the hit. This is called a **brute force** attack. It's exactly how it sounds. You're going to try EVERY single combination in order to eventually get the result you want. 

In our example, a hacker could generate hashes for all 8 character lower case strings without numebrs or symbols. Eventually, they will generate the hash for "password". This assumes a lot of prior knowledge of the password beforehand, most notably length, which is definitely not something hackers always have. Can you imagine if the hacker didn't have this knowledge? They would have to start with 1 character and work their way up to 8 characters of ALL possible permutations of lower case letters.

This might take more time than we anticipated. As we can see, we trade off time for plausibility of cracking the password. Let's be clear, brute force will always work, but there's no guarantee it will be in a reasonable amount of time. Hence, this is why difficulty of deciphering the original data from a hash is an important property. There has to be a potentially faster way of doing this...

Let's go back to our example. So brute forcing is out of the picture because it takes too long. What if the hacker already has a list of thousands of different plaintext passwords? They could just run through the list, compute the hashes for every entry and then compare it to the hash they're trying to crack. Seems easy right? This is called a **dictionary attack**. The hacker is using a list or dictionary of values that they believe may be one of the passwords. This can be much faster than generating our own string combinations in real time. 

The downside to this attack is that it assumes the password is in the list the hacker has. If it's not in the list, then this attack is not going to work. This is why when perfomring a dictionary attack it's important to have a good password list. Believe it or not, there are lists you can find on the internet of common passwords that people use. The attack can only be as good as the dictionary used. Though this can be faster than brute force attacks, the hacker could also find it very difficult and time intensive to use many different dictionaries to find the password.

Wow that was just the first property. All this hacking and failing may leave the hacker a bit salty...

### Password: CanYouPassTheSalt?156*&

The second property is where things get tricky. Imagine you were a system administrator and had a password database full of hashes. It wouldn't be unfair to say that at least two people more than likely share the same password. How could you tell? If you trust the second property holds, you know that if you happen to see at least two of the same hash, it probably means it's the same exact input. This means a hacker could see this as well if they compromised the database. Obviosuly, this is bad. Let's look at the case where Alice and Bob share the same password:

* Alice's password: (password) 5f4dcc3b5aa765d61d8327deb882cf99
* Bob's password: (password) 5f4dcc3b5aa765d61d8327deb882cf99

In defenses against hackers, our goal is to frustrate them as much as possible and not give obvious hints where and how to attack. How can we address this issue of two people sharing the same password? Trusting the second property again, what we could do to change the hashes is add a bit of **salt** (some pre-computed value) to the original password in order to change the hash entirely. Remember, adding just 1 byte of data to the input will change the hash. We can prepend or append this value to the original password.

* Salt value: dontcrackme3456
* Alice's password with salt: (dontcrackme3456password) 760271f1349c0484a4ecaf53e161253e
* Bob's password with salt: (passworddontcrackme3456) 94ccd5cc971a0ffd0745cd5672452d84

As we can now see, we've effectively solved the problem of two people sharing the same password. In Alice's case, the salt value was prepended to her password, whereas Bob's was appended. Both hashes are different, and there's no way a hacker would be able to know these two people shared the same password, let alone use a dictionary or brute force attack in any reasonable time to crack the password. Take that statement with a grain of salt...haha ;) 

This example was pretty simple and defintely not indicative of any real industry security standard for actual salt + hashing techniques for safely storing passwords. However, this is what should happen to your password on the backend when you create one for a website. When you input your password on a login screen, it is converted to a hash with its given salt and compared to the one stored on the database with your username. If they match, then you will be authenticated. If they don't match, you can't login! 

### Ok...but is my password safe?

Whew ok now we get to the actual point of the article. The answer is, it depends...

In the attacks for cracking passwords, I mentioned that length of your password was something that hackers don't always have prior knowledge of. This is very important. As computing power becomes more powerful and accessible to the public, password cracking is becoming faster. It's not a coincidence that websites have requirements for passwords such as 8 characters upper and lower case + numbers + symbols for example. What they're really trying to do is increase the computational complexity that a computer has to go through if it's attempting to generate a hash to crack the passwords. 

The shorter and less complex your password is, the easier it is to potentially crack. In our example with 'password' as an 8 character all lower case string, it actually won't take that long to bruteforce its hash. It would take about 4 hours or less with an average GPU. However, if you use a 20 character alphanumeric + symbols password, you've exponentially increased the time it would take to crack that hash. And for malicious hackers...

![aint]({{ site.url }}{{ site.baseurl }}/assets/images/aint.jpeg)

Length is the key factor in keeping passwords secure, but remember, dictionary attacks might still be a viable way to crack your password. Even if you have a long password, if it somehow has been added to a list of common passwords on the internet, it is subject to cracking. Case and point, the password 'aaaaaaaaaaaaaaaZZZZ' is technically a secure password even though it looks very simple. It will take a very long time to bruteforce it starting from scratch, but could only take a few seconds to use a dictionary attack to crack it. Hence, this is why we need a little more complexity to reduce the likelihood of it being on a list somewhere.

It can be really hard to come up with a long and complex password that you'll A) remember and B) know is secure. This is why it is highly recommened that you get a **password manager** in order to increase your own security posture. Not only will a password manager make your life easier in storing all of your passwords, they also can be used to generate secure passwords based on any criteria needed by the user. Consider getting one! 

Some popular password managers:

* [KeepassX](https://keepass.info/)
* [LastPass](https://www.lastpass.com/)
* [1Password](https://1password.com/)

I recommened looking these up and see which one works for you. Most have free versions and are compatible with most OS's. There may even be a mobile app version as well. 

Thanks for reading! 










