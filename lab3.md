# Lab 3 — Packet Filtering and Analysis using Wireshark

## 1. Installation Setup

Download and install:

- Oracle VirtualBox for Windows
- Kali Linux Virtual Machine

Kali Linux download:

[Kali Linux Downloads](https://www.kali.org/get-kali/?utm_source=chatgpt.com#kali-platforms)

After downloading the Kali VirtualBox image:

```text
Extract the downloaded file
Open Oracle VirtualBox
Import/Open the Kali Linux VM
```

## 2. Configure VirtualBox Network

Open:

```text
VirtualBox
→ Kali VM
→ Settings
→ Network
→ Adapter 1
```

Set:

```text
Attached to:      Bridged Adapter
Promiscuous Mode: Allow All
Cable Connected:  ✔
```

Save the settings.

## 3. Start Kali Linux

Start the Kali Linux VM.

Open a terminal:

```bash
```

## 4. Start Wireshark

```bash
sudo wireshark
```

Select:

```text
eth0
```

Start packet capture.

## 5. Generate Network Traffic

Open websites or use:

```bash
ping google.com
```

```bash
curl https://example.com
```

## 6. Wireshark Display Filters

### DNS

```text
dns
```

### TLS / HTTPS

```text
tls
```

### Specific IP

```text
ip.addr == 18.161.216.37
```

### TCP Port 443

```text
tcp.port == 443
```

### HTTP POST Requests

```text
http.request.method == "POST"
```

## 7. Analyze Packets

Select a packet and expand:

```text
Ethernet
IP
TCP / UDP
Application Layer
```

Check:

```text
Source MAC
Destination MAC

Source IP
Destination IP

Source Port
Destination Port

TCP Flags
SYN
ACK

Protocol Details
```

## 8. Save Packet Capture

Stop the capture using the red square button.

Save as:

```text
lab3-capture.pcap
```

---

# Part B — Capturing Login Credentials over HTTP

## 9. Start Apache

Open a terminal in Kali:

```bash
sudo service apache2 start
```

Check status:

```bash
sudo service apache2 status
```

## 10. Create Login Directory

```bash
sudo mkdir -p /var/www/html/testlogin
cd /var/www/html/testlogin
```

## 11. Create Login HTML Page

```bash
sudo nano login.html
```

Paste:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Login</title>
</head>
<body>

    <h2>Login Form</h2>

    <form action="login.php" method="POST">

        <label>Username:</label>
        <input type="text" name="username">

        <br><br>

        <label>Password:</label>
        <input type="password" name="password">

        <br><br>

        <input type="submit" value="Login">

    </form>

</body>
</html>
```

Save and exit:

```text
Ctrl+O
Enter
Ctrl+X
```

## 12. Create PHP Script

```bash
sudo nano login.php
```

Paste:

```php
<?php

$username = $_POST['username'];
$password = $_POST['password'];

echo "<h2>Login Attempt</h2>";

echo "Username: " . $username . "<br>";
echo "Password: " . $password . "<br>";

?>
```

Save and exit:

```text
Ctrl+O
Enter
Ctrl+X
```

## 13. Check Apache

```bash
sudo service apache2 status
```

If Apache is not running:

```bash
sudo service apache2 start
```

## 14. Test Login Page

Using Firefox, open:

```text
http://127.0.0.1/testlogin/login.html
```

Or using terminal:

```bash
curl http://127.0.0.1/testlogin/login.html
```

## 15. Start Wireshark

```bash
sudo wireshark
```

Select:

```text
lo
```

Start the capture.

Apply:

```text
http.request.method == "POST"
```

## 16. Perform Login

Open:

```text
http://127.0.0.1/testlogin/login.html
```

Enter:

```text
Username: admin
Password: test123
```

Click:

```text
Login
```

## 17. Analyze POST Packet

In Wireshark:

```text
Locate POST packet
→ Right-click
→ Follow
→ TCP Stream
```

Check the transmitted HTTP data.

## 18. Save Capture

Stop the capture.

Save as:

```text
lab3-http-login.pcap
```

## 19. Verify Apache Files

```bash
ls -l /var/www/html/testlogin
```

Expected:

```text
login.html
login.php
```

## 20. Shutdown

Stop Apache:

```bash
sudo service apache2 stop
```

Close Wireshark.

Power off Kali Linux:

```text
Kali Linux
→ Power Off
```

Close Oracle VirtualBox.