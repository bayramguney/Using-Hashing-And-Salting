# Using-Hashing-And-Salting

# Assisted Live Lab: Using Hashing and Salting

## Project Overview

In this lab, I practiced using cryptographic solutions including **hashing and salting** to improve data integrity and password security.

The activities included:

- Verifying file integrity using cryptographic hashes.
- Using hash values to identify suspicious files with MetaDefender.
- Understanding how salting improves password hash security.
- Comparing password cracking difficulty between salted and unsalted hashes.

---

# Scenario

You are a security team member at **Structureality Inc.** Your organization wants to improve its security posture by implementing stronger cryptographic protections.

## Tasks

1. Verify downloaded files have not been modified.
2. Analyze suspicious files using hash lookup services.
3. Understand how password salts protect against brute-force attacks.

---

# Lab Environment

## Virtual Machine

- Kali Linux

## User Credentials

```
Username:
root

Password:
Pa$$w0rd
```

---

# Tools Used

- Kali Linux Terminal
- md5sum
- sha1sum
- OpenSSL
- John the Ripper
- MetaDefender Cloud
- Linux `/etc/shadow`

---

# CompTIA Security+ Objectives

## Objective 1.4

Explain the importance of using appropriate cryptographic solutions.

## Objective 3.3

Compare and contrast concepts and strategies to protect data.

---

# Part 1: Using Hashing to Confirm File Integrity

## Goal

Verify that a downloaded file has not been modified by comparing its hash value with the original hash value provided by the website.

---

# Step 1: Access the Forensic Image Repository

Website:

```
http://dftt.sourceforge.net/
```

Steps:

1. Open a browser.
2. Navigate to the Digital Forensics Tool Testing website.
3. Select:

```
EXT3FS Keyword Search Test ## 1
```

4. Locate the MD5 hash value under the Downloads section.

Example:

```
30e7f792cc853e34e17335b243605d3a
```

---

# Step 2: Access Kali Linux VM

Login:

```
Username:
root

Password:
Pa$$w0rd
```

Mount the DVD:

```
Student-Resources-L06.ISO
```

---

# Step 3: Copy Lab Files

Open Terminal.

View DVD contents:

```bash
ls /media/cdrom0/
```

Copy files:

```bash
cp /media/cdrom0/* /root/Downloads/
```

Navigate:

```bash
cd /root/Downloads
```

Verify files:

```bash
ls -l
```

Expected file:

```
4-kwsrch-ext3.zip
```

---

# Step 4: Extract the Forensic Image

Extract:

```bash
unzip 4-kwsrch-ext3.zip
```

View extracted files:

```bash
ls -l 4-kwsrch-ext3
```

Expected file:

```
ext3-img-kw-1.dd
```

Expected size:

```
5242880 bytes
```

---

# Step 5: Verify File Hash

View original hash:

```bash
cat 4-kwsrch-ext3-hash.txt
```

Generate MD5 hash:

```bash
md5sum 4-kwsrch-ext3/ext3-img-kw-1.dd >> 4-kwsrch-ext3-hash.txt
```

Display hashes:

```bash
cat 4-kwsrch-ext3-hash.txt
```

Compare both values.

## Result

If the hashes match:

- The file integrity is verified.
- The downloaded file is identical to the original file.

---

# Hash Length Reference

| Algorithm | Bit Length | Hex Length |
|---|---|---|
| MD5 | 128-bit | 32 characters |
| SHA-1 | 160-bit | 40 characters |
| SHA-256 | 256-bit | 64 characters |
| SHA-512 | 512-bit | 128 characters |

---

# Security Note

MD5 is deprecated because it is vulnerable to collision attacks.

Recommended hashing algorithms:

- SHA-256
- SHA-384
- SHA-512

---

# Part 2: Using MetaDefender to Analyze Files

## Goal

Identify suspicious files by searching their hash values using an online hash analysis service.

---

# Step 1: Locate Suspicious File

Navigate:

```bash
cd /usr/share/windows-resources/binaries
```

List files:

```bash
ls -l
```

Suspicious file:

```
nc.exe
```

---

# Step 2: Generate SHA-1 Hash

Command:

```bash
sha1sum nc.exe
```

Expected result:

```
57F0839433234285CC9DF96198A6CA58248A4707
```

---

# Step 3: Search Hash Using MetaDefender

Website:

```
metadefender.com
```

Search hash:

```
57F0839433234285CC9DF96198A6CA58248A4707
```

Select:

```
Process
```

---

# Result Analysis

MetaDefender shows whether security engines detect suspicious activity.

A detection does not always mean the file is malware.

The file could be:

- Malicious software
- Potentially unwanted program (PUP)
- Administrative/security tool

---

# Part 3: Exploring Password Salting

## Goal

Understand how adding salt values improves password security against brute-force attacks.

---

# Step 1: View Linux Password Hashes

Navigate:

```bash
cd ~
```

Display password hashes:

```bash
grep '\$' /etc/shadow
```

Linux password hash format:

```
$algorithm$parameters$salt$hash
```

Components:

| Component | Description |
|---|---|
| Algorithm | Hashing method used |
| Parameters | Hashing settings |
| Salt | Random value added to password |
| Hash | Final password hash |

---

# Step 2: Create Unsalted Password Hash

Create MD5 password hash:

```bash
openssl passwd -salt "" pass1 > hash.txt
```

View:

```bash
cat hash.txt
```

Observation:

- Hash does not contain a salt value.

---

# Step 3: Crack Unsalted Password Hash

Run John the Ripper:

```bash
john -incremental hash.txt
```

Result:

- Password can be cracked quickly.

Time:

```
Less than 10 seconds
```

---

# Step 4: Create Salted Password Hash

Create salted hash:

```bash
openssl passwd -salt SALT pass1 > salted-hash.txt
```

View:

```bash
cat salted-hash.txt
```

Example:

```
$1$SALT$passwordhash
```

Observation:

- Salt value is stored with the hash.

---

# Step 5: Crack Salted Hash

Command:

```bash
john -incremental salted-hash.txt
```

Because the salt is included, John can still attempt cracking.

---

# Step 6: Remove Salt Value

Remove salt:

```bash
cat salted-hash.txt | sed "s/SALT//g" > salt-secret-hash.txt
```

Verify:

```bash
cat salt-secret-hash.txt
```

---

# Step 7: Crack Without Knowing Salt

Command:

```bash
john -incremental salt-secret-hash.txt
```

## Result

Cracking becomes extremely slow.

Estimated time:

```
814,506,250 seconds
```

Approximately:

```
25.8 years
```

---

# Why Salting Improves Security

## Without Salt

Password:

```
pass1
```

The same password always creates the same hash.

Attackers can use:

- Rainbow tables
- Precomputed hashes
- Dictionary attacks

---

## With Salt

Password + Random Salt:

Example:

```
pass1 + SALT
```

Creates a unique hash.

Benefits:

- Prevents rainbow table attacks.
- Makes identical passwords produce different hashes.
- Increases brute-force attack difficulty.

---

# Command Summary

## View DVD Files

```bash
ls /media/cdrom0/
```

## Copy Files

```bash
cp /media/cdrom0/* /root/Downloads/
```

## Generate MD5 Hash

```bash
md5sum filename
```

## Generate SHA-1 Hash

```bash
sha1sum filename
```

## View File Contents

```bash
cat filename
```

## Create Password Hash

```bash
openssl passwd
```

## Crack Password Hash

```bash
john -incremental filename
```

---

# Skills Demonstrated

- Cryptographic hashing
- File integrity verification
- MD5 and SHA algorithms
- Hash comparison
- Malware hash investigation
- Password security
- Password salting
- Brute-force attack analysis
- Linux security tools

---

# Security+ Concepts Learned

## Hashing

Hashing is a one-way mathematical function used for:

- File integrity verification
- Password storage
- Digital signatures

---

## Salting

Salting adds random data before hashing.

Purpose:

- Prevent rainbow table attacks.
- Make password hashes unique.
- Increase password cracking difficulty.

---

## Collision

A collision occurs when two different files produce the same hash value.

Older algorithms such as MD5 are more vulnerable to collisions.

---

# GitHub Portfolio Summary

This lab demonstrates practical cybersecurity skills in:

- File integrity validation
- Malware investigation
- Cryptographic hash analysis
- Password protection techniques
- Linux security administration

## Tools Used

- Kali Linux
- OpenSSL
- John the Ripper
- MetaDefender Cloud
