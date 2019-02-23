---
layout: post
title: "DNSSEC Keys and Signing Explained"
categories: [DNS, DNSSEC]
---
This article describes what happens when a zone is signed with DNSSEC. 
This document helps to understand the concept of zone signing and does not detail the actual steps for signing a zone.
                                                                                                                           
															   
**Note**: *I have taken some liberties with this article in the interest of simplicity. For full and accurate information, refer DNSSEC      RFCs.
NXDOMAIN validation is not included in this article.*
                                                        
							
							
Let’s consider an unsigned zone test.com. 
Below is a representation of the zone file with sample records with only the relevant fields.

|Records                                                                               |
|--------------------------------------------------------------------------------------|
|test.com IN SOA ns1.test.com. hostmaster.test.com. (2012022301 1800 1800 1814400 3600)|
|test.com IN NS ns1.test.com.                                                          |
|ns1.test.com IN A 1.2.3.4                                                             |


Now, when we sign the zone, two key pairs are generated. A key pair consists of a private key and a public key. Such key pairs usually used in encryption or signing.

**Encryption** is used to protect data. When some data is encrypted with the public key, only the private key (which is to be kept secret) can decrypt the data.

**Signing** is used to verify the authenticity of data. Here the data is signed with the private key. This just means that the data to be signed and the private key go through a signing algorithm and form some new data. If the new data and the public key go through the signing algorithm the old data is formed. The new data formed is called a hash of the old data. The signing algorithm is just a set of instructions, like a mathematical formula.
                                          
Here is an example.


## Signing

|Data (a)|Private Key (b)|Signing Algorithm                                    |Hash|
|--------|---------------|-----------------------------------------------------|----|
|abcd    |3              |add ‘b’ number or characters to each character in ‘a’|efgh|


## Decoding or Verifying

|Hash (a)|Public Key (b)|Signing Algorithm                                    |Data|
|--------|--------------|-----------------------------------------------------|----|
|efgh    |-3            |add ‘b’ number or characters to each character in ‘a’|abcd|
                        
If you want to go deeper, here is a practical example. 
(*on a linux box**)
                         
Generating a private key.

	$ openssl genrsa -out privkey.pem 2048
	Generating RSA private key, 2048 bit long modulus
	..........................................+++
	..............................................................................................................+++
	e is 65537 (0x10001)

Generating the public key from the private key

	$ openssl rsa -in privkey.pem -pubout -out pubkey.pem
	writing RSA key

These two files are created . 

	$ ls
	privkey.pem  pubkey.pem

Private key

	$ cat privkey.pem
	-----BEGIN RSA PRIVATE KEY-----
	MIIEowIBAAKCAQEA4Kqfn3osK33k/M8lPciHdlSmGN/lmhksds0/ebiF6IAJRXsx
	sQZuFPwHlunnC7ihcIiLE0ySOjpUgLuoLuFWJ5+qwgEyYLwTN3ea1vxKR/2jsxun
	mu4dRH/SpGWhji9H5hDJ+TsAIGnB7FL+9nceFjna+m3GaTVw9Pi3xbMUyLGsIHMH
	4CfngtthT0Nq2Sti2gFF9kCusDuzTfVAOFRNfm1J5J2TgQlmA6XHNpQe+TyNFr5v
	E/dMLHTeMegvEAZQ+mRqCKtBOGrPFoyJdZjyWLCiH3tNFpt+4KxHXFvOqbqPQjqy
	lUyWHE3K89pV7+L3K6RDMmS6EMySFweWjRY3TwIDAQABAoIBAHQN75L0C2kUCXvG
	jZhSxBcONxbWYcauhleAQu/fr9ygdymbL9ogVjEk187PWPinEU4OWrlHbqoBg7FU
	PtaotFaXlh/NenaZ8NtQP34aqUxy62MUQAo6Qogl92vQzBmktuFTfuHt5mzX9MLd
	RLOQaMxWapW+qyWh443IBTZtAamBloGRUVkHARDjXtbq10Y8KPBVFHlMQigC3hVs
	nE3EEMJxUPmPF77V59qWzQNH5/W1jfGQhgavvmE+Bqo8dwFMh4rhVn2x+Yz2JgB6
	JZ1njRkM8k/W5dTfUodzLFpdUaK0oq2LjEPgqtwNqeESneKouqAguReXqEB9pqkw
	q1B6FOECgYEA8DAib5uayJHZzFtDzbYTqk9SIwTQZec5aJF55P126n9rfr2ff8S9
	OGPk5x3tfx/E1FN8cxB1RkRyFzge7Beh4TmkknuyI7vNMk6sXboPbMRLOXp6XTh4
	pwl5q7UgM+/XAY40H+1y0eDKXUv7QkrzkVEatWevUANN/JRm6clW96cCgYEA73Tn
	/e1YTtbWuSCCmWxyfB5aOb81ANbihCTUok8NXtVPacOF4O9y480ayge0jyQAUnzD
	KNyUwNvdB25lochwOgmrZkAvrByoW67vebLwJdtXZNPRVHFIGPUs3Mt4y4+CkckH
	57sv2G6wMfjHoK3LA/dWgH7jNdBpCgCVW8SouBkCgYEArSzHZ1j13K7sLd+Pn34r
	55uRSRZre02forlg/a2SU7jTNGpb2a9sDoBXxhtZ5VJug/g9vmibZbJr4Dnica8I
	VG9PLR5qbkE1zZPTyzAfdviAlEyudRAGTckTJK5PLaM7ji+NfYeiRZihz2q9GisY
	OioT6796M2JulDIbkWxNe/kCgYBAWK76umvvi6XZy5Wsusqs9c8TE4Gfvx7RmcAV
	+Z5DLJkRd7wjLNU3x+b6AUYQ7QC1KdebxGKozKxBkfX3mpAl2HFZocftvSm0sXai
	wmXsFlwOuSjYQzS3mDK9BmRodyEEIfxg1hlOVLg+RXcHg4w5fZ6eGvrdfCqtyGha
	Z6dbCQKBgGR/d5+xtzcdNmRYpqV2dd8lc0ye8kloGZRE3mCByqjAcI2nIXyFqhSi
	Hm7Hs8Pcw/bq2wPMOb9+5gsGHIE9k6yjPhTPBufgr2UtIBO7w6N+eyawiDj7Wlhn
	/4tZqDDNV9QP+YSybcyZI/GsTRCYdeF8rw/hBkXMcG/3gOqsiKOE
	-----END RSA PRIVATE KEY-----


Public key

	$ cat pubkey.pem
	-----BEGIN PUBLIC KEY-----
	MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA4Kqfn3osK33k/M8lPciH
	dlSmGN/lmhksds0/ebiF6IAJRXsxsQZuFPwHlunnC7ihcIiLE0ySOjpUgLuoLuFW
	J5+qwgEyYLwTN3ea1vxKR/2jsxunmu4dRH/SpGWhji9H5hDJ+TsAIGnB7FL+9nce
	Fjna+m3GaTVw9Pi3xbMUyLGsIHMH4CfngtthT0Nq2Sti2gFF9kCusDuzTfVAOFRN
	fm1J5J2TgQlmA6XHNpQe+TyNFr5vE/dMLHTeMegvEAZQ+mRqCKtBOGrPFoyJdZjy
	WLCiH3tNFpt+4KxHXFvOqbqPQjqylUyWHE3K89pV7+L3K6RDMmS6EMySFweWjRY3
	TwIDAQAB
	-----END PUBLIC KEY-----

These will be the keys for our zone. 
                 
## Signing records

Records in the zone are signed with the private key. 

Lets take for example the a record a.example.com

Putting the record into a file.

	$echo "ns1.test.com IN A 1.2.3.4" >arecord.txt


Signing the file with the A record.

	$ openssl dgst -sha256 -sign privkey.pem -out arecord.txt.sha256 arecord.txt

A file named arecord.txt.sha256 with the signature is created.

	$ ls | grep sha256
	arecord.txt.sha256


This signature can verify the authenticity of the original file using the public key. 

	$ openssl dgst -sha256 -verify pubkey.pem -signature arecord.txt.sha256 arecord.txt
	Verified OK


If the original file is changed, or if a different file is used, the verification will fail. 

Here is an example. 

	$ echo test >> arecord.txt
	$ cat arecord.txt
	ns1.test.com IN A 1.2.3.4
	test


Now the original file is changed. 

Let’s try the verification again. 

	$ openssl dgst -sha256 -verify pubkey.pem -signature arecord.txt.sha256 arecord.txt
	Verification Failure

Lets try the verification with a different file. 


	$ echo "ns1.test.com IN A 4.5.6.7" >fakerecord.txt
	$ openssl dgst -sha256 -verify pubkey.pem -signature arecord.txt.sha256 fakerecord.txt
	Verification Failure

                  
As in the above example, all the records in the zone are signed with a private key. The signature is then encoded(same as hashing) with base64 and an RRSIG record is created with the encoding for each record. 

Please see below example.


	$ echo "test.com IN SOA ns1.test.com. hostmaster.test.com. (2012022301 1800 1800 1814400 3600) " > soarecord.txt

	$ echo "test.com IN NS ns1.test.com." >nsrecord.txt

	$ echo "ns1.test.com IN A 1.2.3.4" >arecord.txt

	$ ls
	arecord.txt  nsrecord.txt  privkey.pem  pubkey.pem  soarecord.txt

	$ openssl dgst -sha256 -sign privkey.pem -out soarecord.txt.sha256 soarecord.txt

	$ openssl dgst -sha256 -sign privkey.pem -out nsrecord.txt.sha256 nsrecord.txt

	$ openssl dgst -sha256 -sign privkey.pem -out arecord.txt.sha256 arecord.txt

	$ ls
	arecord.txt  arecord.txt.sha256  nsrecord.txt  nsrecord.txt.sha256  privkey.pem  pubkey.pem  soarecord.txt  soarecord.txt.sha256

	$ base64 soarecord.txt.sha256
	MMoccLV19wocCHo2hdLSjKy5Irp2imxwXRone1eRpmSMpr33E3o1bcMORwyHutTtl0BKPPnlJiMK
	mODMydZmRaM6n3UEDcOXUKTwWwDYihsM4/bic53zhBuS+fIn9EowKmEKJmgP5DCLxt5qmdK5xfZS
	F20OKi/SHOJDy4YrGJrfOiI3Lp6FEVgFcNWZy+CVpXAItDsn5S2mkFv87oTsID01XTExRWvJRQSF
	sDvOBYF7eEWmgXi7PnST0xN1fu3kwonmCOsXU2r7xJA1pmdyt7qQZGOLPTqmNftOALRySbrtiH27
	AO4yct+54/sFEzxwpZ80FFZiE6sOS8FVHuLNTg==

	$ base64 nsrecord.txt.sha256
	b5iHVdENqbPoOt7kx6uXw+tdR5pfITohuwoXjSMvFbVgigAyiWky0UqlR1G+K4YrVXWka3m+vERs
	H6QRndMrHVzvJgM4X9PTxgtgRC8skgg6HSyO/YH5lP7YXRNGPjWXnxOW0ry6x7+ij5WgwHB7TWkq
	dU/HcVNupoYZ/5R0aGaDe5tyFgKtMW7dS3EieR08Vg0lGNUP8eEB9Xf0n+nhADj0S8pFnJtn2AUh
	+gWNdLDkdNpmX9xFDZOS3JrlZem5FsGf+VvcgDySxVRabDsfioAzwxHbSgbmwsbiUM7no1ZX8xJU
	62TCI3uNe6FHOyyJELsiUHEnzneLANqpU/irkg==

	$ base64 arecord.txt.sha256
	rOORLSorofC5s8DeT4n2HvnW5EV7rrR+ZpbOk5rnNAwJh1iNx24J687YgLrpotxzbZUGPUIlU3nN
	tn50LzNZ6AicCFB/wppTq1a2jswmeF6YiKWXF/cjhvSG3Q46Vdlfe2Mn4hrNEaS+OEk6TjdTT7py
	ajwUDuzIrH9FU/Z2hYles3GIWBaffD9HrC+NuOvG4mMB8Z72ABin7oURKowZSQ2WsaQu5XlxWMaE
	TLx9pKQJD+r8naCD1XCCvJhwWLA78rnreRVtTv7gRYuyiCQIWhkBkxs0cSGcrKxy+fiJ/83vWNMl
	U3GRLY80wvDLHU4M84L+u0d++WcfPV5KKtudbQ==



                        
Now with the base64 encoded strings obtained, RRSIG records are created like below. 


|Records                                                                      |
|:-----------------------------------------------------------------------------|
|test.com  IN RRSIG SOA MMoccLV19wocCHo2hdLSjKy5Irp2imxwXRone1eRpmSMpr33E3o1bcMORwyHutTtl0BKPPnlJiMKmODMydZmRaM6n3UEDcOXUKTwWwDYihsM4/bic53zhBuS+fIn9EowKmEKJmgP5DCLxt5qmdK5xfZSF20OKi/SHOJDy4YrGJrfOiI3Lp6FEVgFcNWZy+CVpXAItDsn5S2mkFv87oTsID01XTExRWvJRQSFsDvOBYF7eEWmgXi7PnST0xN1fu3kwonmCOsXU2r7xJA1pmdyt7qQZGOLPTqmNftOALRySbrtiH27AO4yct+54/sFEzxwpZ80FFZiE6sOS8FVHuLNTg==  			      |
|test.com IN RRSIG NS b5iHVdENqbPoOt7kx6uXw+tdR5pfITohuwoXjSMvFbVgigAyiWky0UqlR1G+K4YrVXWka3m+vERsH6QRndMrHVzvJgM4X9PTxgtgRC8skgg6HSyO/YH5lP7YXRNGPjWXnxOW0ry6x7+ij5WgwHB7TWkqdU/HcVNupoY/5R0aGaDe5tyFgKtMW7dS3EieR08Vg0lGNUP8eEB9Xf0n+nhADj0S8pFnJtn2AUh+gWNdLDkdNpmX9xFDZOS3JrlZem5FsGf+VvcgDySxVRabDsfioAzwxHbSgbmwsbiUM7no1ZX8xJU62TCI3uNe6FHOyyJELsiUHEnzneLANqpU/irkg==                              |
|Ns1.test.com IN RRSIG A rOORLSorofC5s8DeT4n2HvnW5EV7rrR+ZpbOk5rnNAwJh1iNx24J687YgLrpotxzbZUGPUIlU3nNtn50LzNZ6AicCFB/wppTq1a2jswmeF6YiKWXF/cjhvSG3Q46Vdlfe2Mn4hrNEaS+OEk6TjdTT7pyajwUDuzIrH9FU/Z2hYles3GIWBaffD9HrC+NuOvG4mMB8Z72ABin7oURKowZSQ2WsaQu5XlxWMaETLx9pKQJD+r8naCD1XCCvJhwWLA78rnreRVtTv7gRYuyiCQIWhkBkxs0cSGcrKxy+fiJ/83vWNMlU3GRLY80wvDLHU4M84L+u0d++WcfPV5KKtudbQ==                         |


**Note**: *The above records are not in the true structure of these records. This is only a representation to help understand the concept. 
For the real records structure and understanding the different fields in the records, refer <a href=https://tools.ietf.org/html/rfc4034>RFC 4034</a>.*


                                   


These records can be decoded with base64 to get the signatures and the signatures can be verified with the public key. 

For such verification, the public key for the zone needs to be distributed to the clients. 

The public key is encoded with base64 and the encoding is used to create a DNSKEY record for the zone. This DNSKEY record is known as a Zone Signing Key. 
                            
As mentioned earlier in the article, when a zone is signed, two key pairs are generated. 

The second key pair is used to sign and verify the public key for the zone(*Zone Signing Key*). 

The public key is signed using the second private key, the signature is encoded in base64 and the encoding is used to create an RRSIG for the Zone Signing Key. The second public key is encoded with base64  another DNSKEY record known as the Key Signing Key. This record is used to verify the authenticity of the Zone Signing Key. 


                               
Now the public key of the Key Signing Key is encoded/hashed with SHA-1 or SHA-256 and this encoding/hash is used to create another record called the DS record. 

The DS record is placed at the parent for the zone. In this case, the parent for ‘test.com.’ is ‘com.’.
The DS record can be used to verify the authenticity of the Key Signing Key. 
                 
## Diagram

### Zone Signing Process

```

+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|                                                                                                                                                                         |
|                                                                                                                                                                         |
|                                                                                                                                                                         |
|                                                                                                                                                                         |
|                                                                                                                                                                         |
|                                                                                                                                                                         |
|                     +----------------+                               +------------------------------------------------+                                                 |
|                     | Unsigned Zone  |                               |  +-------------------------+                   |                                                 |
|                     +----------------+                               |  |Record 1 | Private Key 1 |                   |                                                 |
|                     |  Record 1      |                               |  +-------------------------+                   |                                                 |
|                     |                |                               |  |Record 2 | Private Key 1 |                   |                                                 |
|                     |  Record 2      |                               |  +-------------------------+                   |                                                 |
|                     |                |                               |  |Record 3 | Private Key 1 |                   |                                                 |
|                     |  Record 3      |                               |  +-------------------------+                   +------------+                                    |
|                     +-------+--------+          +------------------->+            +                                   |            |                                    |
|                             |                   |                    |      Signing Algorithm                         |            |                                    |
|                             +                   +                    |            +                                   |     Public Key 1 is                             |
|                       Two sets of Keys     Records are signed with   |            |                +---------------+  |    encoded with Base64                          |
|                       are created          the first private key     |            +                |Record 1 RRSIG |  |    to create the ZSK DNSKEY record              |
|                             +              and the signature is      |      Base64 Encoding        +---------------+  |            |                                    |
|                             |              encoded with Base64 to    |            +                |Record 2 RRSIG |  |            |                                    |
|                             |              create RRSIG records      |            +--------------->----------------+  |            |                                    |
|                             |                   +                    |                             |Record 3 RRSIG |  |            |                                    |
|                             |                   |                    |                             +---------------+  |            |                                    |
|                             |                   |                    |                                                |            |                                    |
|                      +-----------------------------------------+     +------------------------------------------------+            |                                    |
|                      | +-------------+          +------------+ |    +----------------------------------------------------------+   |                                    |
|                      | |Private Key 1|          |Public Key 1| |    | +-------------+                   +--------------------+ +<--+                                    |
|                      | +-------------+          +------------+ |    | |Public Key 1 +--Base64 Encoding->+DNSKEY Record (ZSK) | |                                        |
|                      | +-------------+          +------------+ |    | +-------------+                   +--------------------+ +-------------+                          |
|                      | |Private Key 2|          |Public Key 2| |    +----------------------------------------------------------+             |                          |
|                      | +-------------+          +------------+ |    +----------------------------------------------------------+   ZSK DNSKEY record is                 |
|                      +-----------------------------------------+    | +----------------------------+    +-------------------+  |   signed with Private Key 2 and        |
|                                                                     | |DNSKEY(ZSK) | Private Key 2 |    |DNSKEY (ZSK) RRSIG |  |   Base64 encoded to create ZSK RRSIG   |
|            +-----------------------------------------------------+  | +----------------------------+    +----------+--------+  |   record    |                          |
|            |--------------+                +-------------------+ |  |              |                               v           |             |                          |
|  +----------|Public Key 2 +-Base64 Encoding|DNSKEY Record (KSK)| |  |      Signing Algorithm                Base64 Encoding    +<------------+                          |
|  |         |--------------+                +-------------------+ |  |              |                               |           |                                        |
|  |         +---------------------+-------------------------------+  |              +-------------------------------+           |                                        |
|  |                               ^                                  |                                                          |                                        |
|  |                               |                                  +--------------------------+-------------------------------+                                        |
|  |                               |                                                             |                                                                        |
|  |                               |                                                             |                                                                        |
|  |                               |                                                             |                                                                        |
|  |                               +-------------------------------------------------------------+                                                                        |
|  |                                                                                                                                                                      |
|  |                                      +--------------------------------------------------+                                                                            |
|  |                                      | +------------+                      +----------+ |                                                                            |
|  +------------------------------------->+ |DNSKEY (KSK)+----SHA256 Hash------>+DS Record | |                                                                            |
|                                         | +------------+                      +----------+ |                                                                            |
|                                         +--------------------------------------------------+                                                                            |
|                                                                                                                                                                         |
+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

```
