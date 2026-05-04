# DropSeed ( BIP39 Story Builder )

> Drag words. Build stories, let the checksum finish the sentence

# [DropSeed Playground](https://dropseed.vercel.app/)

## [TRY it live before using it](https://dropseed.vercel.app/)

## Don't generate seeds online ( only for testing ), install official [RELEASE](https://github.com/Raskollnikov/DropSeed/releases/edit/1.0.0)

## I am using Tails Os + No Bluetooth/SSD/HDD/WIFI/Ethernet. only single html file ( with valid hash and that's it ) 

```bash
sha256sum dropSeed.html ->  e4d4bf2a1ec5be9a720eaca8ea1493bdff45d12d03ed08dcfe020f76d4a9684f
```


**The offline BIP39 tool that turns broken English and ridiculous repetition into valid, memorable Bitcoin seeds**


---

## My motivation

When i was learning about Bitcoin private keys, i learned that it's simply a 256 bit number ( formatted in wallet import format, which is called WIF )

but [WIF](https://learnmeabitcoin.com/technical/keys/private-key/wif/) is simply a private key + version byte + checksum encoded using base58check

As bitcoin developed further developers introduced HD wallets instead of having individual address backed by individual WIF ( which is very hard to manage )

They introduced [HD WALLETS](https://learnmeabitcoin.com/technical/keys/hd-wallets/) known as a [BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki)

as i went down the rabbit hole, i realized that for a 24 word mnemonic, Bitcoin key material starts from a 256 bit block of entropy ( BIP39 supports multiple entropy sizes from 128 to 256 bits, mapping to 12 24 word mnemonics, but DropSeed is built specifically around the 24 word / 256 bit case ), which is then transformed into a human readable format using [BIP39](https://github.com/bitcoin/bips/blob/master/bip-0039/english.txt).<br>

instead of directly encoding this 256 bit value into formats like WIF ( wallet import format ), which represents a single private key in a compact base58check form, BIP39 takes a different approach: it converts the entropy into a mnemonic phrase. this is done by splitting the binary entropy into 11 bit chunks, since each chunk can represent a value from 0 to 2047 ( 2^11 possible values ), each of these values is then used as an index into the BIP39 wordlist, mapping raw binary data into words

if we multiply 11 × 24, we get 264 bits, but our entropy is only 256 bits. 23 × 11 = 253, which means 23 words account for 253 bits of entropy. the 24th word is not a full entropy word, it's built from the last 3 bits of the 256 bit entropy ( bits 254 256 ) plus an 8 bit checksum. those 3 bits are not floating leftovers, they are the tail end of the entropy block that simply don't fit into the first 23 words.

then i asked whether i could choose any bits i want in order to get 256 bits, why cant i choose any word i want from the official [BIP39](https://github.com/bitcoin/bips/blob/master/bip-0039/english.txt) wordlist ( 2048 words total ). and i was right, i could but with one restriction. we know that a seed has 24 words ( but we can't randomly choose all 24 words ) why? because 264 bits ( 24 × 11 ) are not all entropy, for 256 bit entropy specifically, the last 8 bits of the final word are a checksum, not entropy. the checksum length is always ENT/32, so for our 256 bit case that's exactly 8 bits. so only 23 words are pure free choice, and each of those 23 words must come from the official BIP39 wordlist freedom here means any combination from those 2048 words, not arbitrary English.<br>
<br>
in order to get 264 bits ( which is 24 × 11 ) we add the [checksum](https://learnmeabitcoin.com/technical/keys/checksum/) which is created by SHA256(entropy)[0:8], the first 8 bits of the SHA256 hash of the full 256 bit entropy appended to the end of those 256 bits, giving us 264 bits total.<br>
<br>
so my mental model was clear. i wanted to create a project where i would let users choose any combination of 23 words from the BIP39 wordlist ( same way they can choose any bits, each word is a certain index in decimal, and each word maps to an 11 bit index from the BIP39 wordlist, so they are still choosing the bits ) and then the program would derive the checksum automatically. you simply grab any combination of 23 words to get 253 bits, and the remaining 3 bits are the tail of the 256 bit entropy block, they are what shapes the 24th word. how?<br>
<br>
those 3 tail entropy bits have 2^3 = 8 possible values ( 000, 001, 010, 011, 100, 101, 110, 111 ). for each of these 8 candidates, the program assembles a unique 256 bit entropy ( your 253 bits + the 3 bit suffix ), computes SHA256 of that entropy, takes the first 8 bits as the checksum, and concatenates: 3 bit suffix + 8 bit checksum = 11 bits = one BIP39 word index. so each candidate produces 8 possible valid 24th words, and users can choose any one of them. it's mind blowing already ( i want to add things which goes beyond imagination )

BIP39 math: free_words × 11 = entropy_bits <br>
12-word: 11×11=121 bits free + 7 tail + 4 checksum = 132 = 12×11 <br>
24-word: 23×11=253 bits free + 3 tail + 8 checksum = 264 = 24×11 <br>

i implemented 12 seed option too, users also can switch to the 12 word seed, building sentences using that option might seem easy, but don't forget that brute forcing also becomes easier on basic words combinations

totally valid seeds i have already built look like :

---

## Examples

**"advance machine work inside satoshi farm during boy receive call about crazy company boss agree start offer among differ people between any year online"**

**"one frozen boy decide learn latin inside school among kangaroo end giraffe during enroll boy engage satoshi core about seven hour during day twist"**

**"abandon car still make noise because problem still exist inside engine laugh repair repair repair repair repair repair possible road"**

**"crazy city where people love digital gold appear amazing help satoshi smart"**

**"boy love girl end also satoshi because satoshi develop digital gold casino"**

all of these are **perfectly valid** 24 word BIP39 seeds haha

grammar is broken.  
repetition is heavy.  
memorability is high.

SCREENSHOTS

Personal favorite

satoshi master mind able develop digital gold change world toward better royal

<img width="1832" height="939" alt="image" src="https://github.com/user-attachments/assets/5e4100d9-1952-4286-af39-f9f27a955372" />

all the seeds are totally valid ( it's sparrow wallet )

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6d275873-2a44-487a-b6e9-a8cd89e86994" />

<img width="652" height="715" alt="image" src="https://github.com/user-attachments/assets/5017c9ce-44bb-4ef3-8500-91fcc9600772" />

<img width="652" height="715" alt="image" src="https://github.com/user-attachments/assets/c4842e74-58de-486f-86dc-da22de85a7d3" />

<br>
<img width="1796" height="922" alt="image" src="https://github.com/user-attachments/assets/67c67256-37a8-4a39-b5d8-dace8a950ccc" />
<br>
<img width="1796" height="922" alt="image" src="https://github.com/user-attachments/assets/8301388a-2338-473a-9aad-d409d6c8fc45" />
<br>
<img width="1796" height="922" alt="image" src="https://github.com/user-attachments/assets/8e3192bb-5668-4446-9768-8100b13df941" />
<br>
<img width="1796" height="922" alt="image" src="https://github.com/user-attachments/assets/0d2b509c-e8bf-43d5-8d7a-049fd4169349" />
<br>
<img width="1796" height="922" alt="image" src="https://github.com/user-attachments/assets/efd7fbf1-a175-477e-8f74-28c4c12b12c1" />


## in order to make building sentences easier, i inspected these words and split into different categories such as ( Noun, Verb, Adjectives, Adverbs, Specials, Months, etc... etc... )

## i did that using [Text Inspector](https://textinspector.com/parts-of-speech-identifier/)
## i pasted full 2048 words there, and these website automatically splits the words into the different parts of speech


<img width="1836" height="956" alt="image" src="https://github.com/user-attachments/assets/ec1e92f0-54a8-4c24-9300-91bd8c9366db" />
<img width="1836" height="956" alt="image" src="https://github.com/user-attachments/assets/b3a8ebf0-0d3f-4267-98c1-62c91f657c9a" />


---

## Philosophy

Bitcoin mnemonics shouldnt feel like punishment

They should feel like **something you created**

DropSeed lets you inject personality, repetition, rhythm, and even humor into your seed phrase ( or build surreal stories like mines ), while the checksum makes sure it remains technically perfect
