# Wireshark Packet Analysis & SOC Network Labs

Welcome to my network analysis documentation repository!

This repository documents my hands-on investigative labs completed during my cybersecurity internship with **NetworkWalks**. Using **Wireshark** as the primary network analysis tool, these labs focus on protocol dissection, traffic filtering, and investigating security scenarios commonly handled in SOC environments.

---

## Lab 1: Identify the Attacker's IP

### 📌 Scenario & Objective
Analyze a raw network traffic capture to identify malicious scanning activity, isolate threat signatures, and map attacker-victim behaviors.

### Investigation Steps

1. **Capture the traffic:** Loaded the raw PCAP file into Wireshark to begin analyzing the network session.
2. **Show only SYN packets:** Filtered the traffic to display only TCP SYN packets:
   ```
   tcp.flags.syn == 1 and tcp.flags.ack == 0
   ```
3. **Find the attacker:** Examined source IP addresses initiating connection requests to spot the scanning node.
4. **Find the target:** Identified the destination IP address receiving the high volume of probe requests.
5. **Source port:** Inspected the ephemeral source ports used during the attack initiation.
6. **Ports probed:** Analyzed the range or specific destination ports targeted by the scan.
7. **Open ports:** Determined which target ports responded with SYN-ACK:
   ```
   tcp.flags.syn == 1 and tcp.flags.ack == 1
   ```
8. **Reply for closed ports:** Inspected the TCP RST/ACK or ICMP unreachable responses sent back by the target for closed ports.
9. **Closed ports:** Identified the specific port numbers that rejected the connection attempts.
10. **Attack type:** Classified the behavior as a **Stealth TCP SYN Port Scan**.

---

## Lab 2: Follow TCP Stream (Part 1)

### 📌 Scenario & Objective
Deconstruct transport layer sessions by reassembling TCP conversations to read full session content and payload data.

### Investigation Steps

1. **Capture the traffic:** Opened the target PCAP file containing conversational data streams.
2. **Find the chat port:** Identified the application/service port used for the messaging session.
3. **Who started the chat?** Analyzed initial packet directions (source IP) to find the chat initiator.
4. **Stream number:** Located the specific TCP stream index in Wireshark using `tcp.stream`.
5. **Size of the chat:** Calculated the total payload bytes transferred during the conversation stream.
6. **Who needs help?** Extracted text payloads to determine which user requested support.
7. **The problem drive:** Identified drive letters or network resource paths discussed in the session.
8. **Temporary password:** Extracted plaintext temporary credentials from the stream payload.
9. **Guest Wi-Fi password:** Located the shared network access key revealed in the conversation.
10. **Text colour:** Analyzed configuration strings or text styling mentions within the extracted text.

---

## Lab 3: Follow TCP Stream (Part 2)

### 📌 Scenario & Objective
Advanced TCP stream tracking: locating hidden files, analyzing backup server interactions, and inspecting HTTP response codes.

### Investigation Steps

1. **Capture the traffic:** Loaded the second part of the TCP stream capture into Wireshark.
2. **Find the pages:** Inspected web requests or directory listings retrieved during the session.
3. **Where is it hidden?** Located the specific directory path or hidden folder structure from the stream payload.
4. **Find the stream:** Identified the exact TCP stream index containing the target file transfer.
5. **The code word:** Extracted specific keywords or verification tokens embedded in the communication.
6. **The backup server:** Identified the IP address or hostname acting as the secondary backup target.
7. **Which direction?** Determined whether the data flow was an upload or download relative to client and server.
8. **Status code:** Inspected the HTTP response codes (e.g., `200 OK`, `404 Not Found`) returned in the stream.
9. **The downloaded file:** Extracted the file name and extension transferred during the session.
10. **Who made the report?** Identified user identifiers, author tags, or metadata embedded within the document stream.

---

## Lab 4: HTTP Plain Text Credentials

### 📌 Scenario & Objective
Inspect unencrypted HTTP traffic to locate login pages, extract plaintext credentials, and evaluate remediation steps.

### Investigation Steps

1. **Capture the traffic:** Loaded the web traffic capture containing cleartext protocol exchanges.
2. **Which protocol?** Identified **HTTP** as the unencrypted protocol used for transmission (`http`).
3. **The method:** Determined the HTTP request method used for submitting credentials (`POST`).
4. **The login page:** Located the URI path or endpoint used for user authentication.
5. **The PC that signed in:** Identified the source IP or host machine performing the login.
6. **How many attempts?** Counted the total login requests to track brute-forcing or trial-and-error behavior.
7. **The username:** Extracted the account username from the HTTP form parameters (`urlencoded-form`).
8. **The password:** Retrieved the exposed plaintext password transmitted across the wire.
9. **Packet details line:** Pinpointed the exact packet details line displaying the submitted credentials.
10. **The fix:** Recommended migrating to **HTTPS (TLS encryption)** to secure credentials in transit.

---

## Lab 5: Brute Force Catch

### 📌 Scenario & Objective
Track repetitive authentication failures, analyze error response codes, and detect brute-force attack patterns in network captures.

### Investigation Steps

1. **Capture the traffic:** Opened the security event capture file containing authentication traffic.
2. **The protocol:** Identified the application-layer protocol targeted by the brute-force attempts.
3. **The target:** Located the victim server IP address hosting the authentication service.
4. **The attacker:** Pinpointed the source IP generating high-frequency login requests.
5. **The username:** Identified the user account targeted during the password guessing.
6. **Failure code:** Analyzed server response codes indicating failed logins (e.g., `530 Not logged in`).
7. **Failed logins:** Counted the total number of rejected authentication attempts.
8. **Success code:** Identified the response code indicating a successful login (e.g., `230 Logged in`).
9. **The password that worked:** Isolated the final correct password that passed the authentication check.
10. **The attack type:** Classified the incident as a **Credential Brute-Force Attack**.

---

## Lab 6: VoIP Call Replay

### 📌 Scenario & Objective
Investigate VoIP security by analyzing SIP call setup signaling and reconstructing RTP media streams.

### Investigation Steps

1. **Capture the traffic:** Loaded the telephony and VoIP traffic capture into Wireshark.
2. **Call setup protocol:** Identified **SIP** (Session Initiation Protocol) as the signaling protocol (`sip`).
3. **Voice protocol:** Identified **RTP** (Real-time Transport Protocol) carrying the actual audio stream.
4. **Caller extension:** Extracted the extension number of the entity initiating the call.
5. **Caller name:** Retrieved caller ID details from the SIP INVITE headers using:
   ```
   sip.Method == "INVITE"
   ```
6. **The codec:** Determined the audio codec negotiated for the media stream (e.g., G.711, G.729).
7. **Who answered?** Identified the extension or user agent that accepted the call (`200 OK`).
8. **Card number:** Extracted sensitive financial data (credit card details) spoken or transmitted during the call scam.
9. **Expiry date:** Located the validity date associated with the compromised card details.
10. **The fix:** Recommended session encryption (**SRTP/TLS**) and anomaly detection on VoIP gateways.

---

## Key Takeaways Across All Labs

- **Practical Tooling & Advanced Filtering:** Gained hands-on experience using Wireshark and applying display filters (such as `sip.Method == "INVITE"` or `tcp.flags.syn == 1`) to isolate specific protocol behaviors and investigate security-related activities, including port scanning and brute-force attempts.
- **Protocol Mechanics & Frame Structure:** Developed a realistic understanding of how Wireshark operates at Layer 2, dissecting frame structures, protocol headers, and specific status codes (e.g., FTP `530`, SIP `200 OK`).
- **Moving to Hands-on Analysis:** Successfully bridged theory with practice, building a clearer understanding of how network traffic analysis fits into daily SOC operations.

---

## Author

**Furat Omar**

*This repository reflects my ongoing learning journey and practical development in cybersecurity and network traffic analysis.*

