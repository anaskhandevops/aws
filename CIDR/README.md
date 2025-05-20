# Understanding CIDR Block: `10.0.0.0/19`

This document explains how to calculate the **IP address range** from a given CIDR block, using the example `10.0.0.0/19`.

---

## 🧠 What is a CIDR Block?

CIDR (Classless Inter-Domain Routing) notation is used to specify IP address ranges. It has the format: 

```
base IP address / prefix length 
```
---
## 🔢 Step-by-Step Explanation

✅ Step 1: What does /19 mean?

The `/19`tells us how many bits are used for the network.

IP addresses are `32 bits`. So: 19 bits are fixed for network.

Remaining `32 - 19 = 13` bits are available for host IPs (i.e. devices in that subnet).

✅ Step 2: How many IPs does 13 bits give us?

`2^13 = 8192` total IP addresses.

These start at the base IP: 10.0.0.0

✅ Step 3: Convert 8192 into actual range

We add `8192 - 1 = 8191` to the base IP (10.0.0.0) to get the last IP:

So we ask:
👉 What is `10.0.0.0 + 8191` in IP format?

## 🧠 How to Add 8191 to 10.0.0.0?
Let’s convert IPs to numbers.

Convert 10.0.0.0 to a 32-bit number:
```
10     . 0      . 0      . 0
(10)     (0)      (0)      (0)
= (10 * 256^3) + (0 * 256^2) + (0 * 256^1) + 0
= 167772160
```
Now add 8191:
```
167772160 + 8191 = 167780351
```
Convert `167780351` back to an IP address:

167780351 ÷ 256 = 655391

655391 ÷ 256 = 2552

2552 ÷ 256 = 9

So:
```
10.0.31.255
```


