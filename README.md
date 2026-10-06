# DropCLI 🚀

> A secure, high-speed, terminal-based peer-to-peer file sharing system for transferring large files directly between computers.

DropCLI is a command-line application designed to make file sharing between two computers as simple as:

```bash
drop send movie.mp4
```

and:

```bash
drop receive X7K9-P2QA
```

The goal is to build an **AirDrop-like experience for the terminal**, while allowing the system to work across local networks and eventually across the Internet.

The file itself should be transferred **directly between peers whenever possible**, rather than being uploaded to a central storage server.

---

## ✨ Features

### Core Features

- 📁 Send individual files
- 📦 Transfer very large files
- ⚡ Chunk-based streaming
- 📊 Real-time transfer progress
- 🚀 High-speed local network transfers
- 🔐 End-to-end encryption
- 🔑 Secure share codes
- ✅ SHA-256 file integrity verification
- 🔄 Resume interrupted transfers
- 🖥️ Terminal/CLI interface
- 🌐 Internet-based peer-to-peer transfer
- 🔥 No permanent server-side file storage
- ⏳ Expiring and one-time share codes

### Planned Features

- Folder transfer
- Multiple simultaneous transfers
- Transfer history
- Bandwidth limiting
- Automatic peer discovery on LAN
- QR-code sharing
- Desktop GUI
- Mobile client
- Web client
- Transfer pause/resume
- Automatic compression
- Selective file sharing

---

# 🧠 Project Idea

Traditional file sharing usually follows this model:

```text
Sender
   │
   │ Upload 2 GB
   ▼
Cloud Server
   │
   │ Download 2 GB
   ▼
Receiver
```

This has several disadvantages:

- The sender uploads the entire file.
- The receiver downloads it again.
- Server bandwidth is consumed twice.
- Temporary server storage may be required.
- Privacy depends on the server.

DropCLI aims for:

```text
Sender
   │
   │ Direct P2P transfer
   ▼
Receiver
```

The backend is mainly responsible for **authentication, session management, and connection negotiation**, rather than permanently storing the user's files.

---

# 🏗️ High-Level Architecture

```text
                         ┌────────────────────────┐
                         │      Signaling Server  │
                         │                        │
                         │  • Share Codes        │
                         │  • Peer Discovery      │
                         │  • Signaling           │
                         │  • Authentication      │
                         │  • Session Management  │
                         └───────────┬────────────┘
                                     │
                          Connection Negotiation
                             /               \
                            /                 \
                           ▼                   ▼
                 ┌─────────────────┐   ┌─────────────────┐
                 │   Computer A    │   │   Computer B    │
                 │                 │   │                 │
                 │   DropCLI       │   │   DropCLI       │
                 │                 │   │                 │
                 │   File Reader   │   │   File Writer   │
                 │   Encryption    │   │   Decryption    │
                 │   Hashing       │   │   Hashing       │
                 └────────┬────────┘   └────────▲────────┘
                          │                     │
                          │    P2P Connection  │
                          └─────────────────────┘
                              File Transfer
```

---

# 🔄 Transfer Flow

A typical transfer works like this:

```text
1. Sender selects a file
          ↓
2. DropCLI calculates metadata
          ↓
3. DropCLI creates a random share code
          ↓
4. Receiver enters the share code
          ↓
5. Signaling server introduces the peers
          ↓
6. Peers negotiate a connection
          ↓
7. Secure connection is established
          ↓
8. File is divided into chunks
          ↓
9. Chunks are encrypted
          ↓
10. Chunks are transferred
          ↓
11. Receiver writes chunks to disk
          ↓
12. Receiver calculates SHA-256
          ↓
13. Hashes are compared
          ↓
14. Transfer is marked successful
```

---

# 🖥️ CLI Interface

The final CLI is intended to feel simple.

## Send a file

```bash
drop send ./movie.mp4
```

Example:

```text
DropCLI v1.0

File: movie.mp4
Size: 2.14 GB

Share Code: X7K9-P2QA

Waiting for receiver...
```

---

## Receive a file

```bash
drop receive X7K9-P2QA
```

Example:

```text
DropCLI v1.0

Connecting...
Peer found ✓
Secure connection established ✓

File: movie.mp4
Size: 2.14 GB

[██████████████████░░░░░░] 78%

Transferred: 1.67 GB / 2.14 GB
Speed: 94.2 MB/s
ETA: 5 seconds
```

After completion:

```text
Transfer completed ✓

SHA-256:
Original : 4e9c...8a21
Received : 4e9c...8a21

Integrity verification: PASSED ✓
```

---

# 🛠️ Technology Stack

## CLI Application

### Go

Go is the primary language for DropCLI.

Reasons:

- Excellent networking support
- Built-in concurrency
- Efficient memory usage
- Strong standard library
- Easy file I/O
- Easy binary distribution
- Cross-platform support

---

## Networking

### Initial Version

```text
TCP
```

TCP is ideal for the first version because it provides:

- Reliable delivery
- Ordered data
- Automatic retransmission
- Connection management

### Advanced Version

```text
UDP / QUIC / WebRTC
```

These can be introduced later for more advanced peer-to-peer Internet communication.

---

## Signaling

```text
WebSocket
```

The signaling server does not need to transfer the actual file.

Its job is to exchange connection information between peers.

For example:

```text
Sender → Server

"Create session"

Server → Sender

"Share code = X7K9-P2QA"

Receiver → Server

"Join X7K9-P2QA"

Server

"Sender and receiver matched"
```

---

## Security

Recommended technologies:

```text
SHA-256
AES-256-GCM
ChaCha20-Poly1305
TLS
Secure random token generation
```

Encryption prevents an intermediary from easily reading transferred file contents.

SHA-256 is used to verify that the received file matches the original.

---

# 📂 Project Structure

A scalable project structure:

```text
dropcli/
│
├── cmd/
│   ├── send.go
│   ├── receive.go
│   └── root.go
│
├── internal/
│   │
│   ├── network/
│   │   ├── tcp.go
│   │   ├── udp.go
│   │   └── protocol.go
│   │
│   ├── transfer/
│   │   ├── sender.go
│   │   ├── receiver.go
│   │   ├── chunk.go
│   │   └── resume.go
│   │
│   ├── security/
│   │   ├── hash.go
│   │   ├── encryption.go
│   │   └── token.go
│   │
│   ├── signaling/
│   │   └── client.go
│   │
│   └── discovery/
│       └── lan.go
│
├── server/
│   ├── main.go
│   ├── websocket.go
│   ├── session.go
│   └── codes.go
│
├── configs/
│   └── config.yaml
│
├── tests/
│   ├── network_test.go
│   ├── transfer_test.go
│   └── security_test.go
│
├── go.mod
├── go.sum
├── README.md
└── LICENSE
```

For the first prototype, however, the structure can be much smaller:

```text
dropcli/
├── main.go
├── sender.go
├── receiver.go
└── protocol.go
```

---

# 📡 Version 1: LAN File Transfer

The first goal is to transfer files between two computers connected to the same local network.

Architecture:

```text
             Wi-Fi Router
                 │
        ┌────────┴────────┐
        │                 │
   Computer A         Computer B
   192.168.1.5        192.168.1.10
        │                 │
        └────── TCP ──────┘
```

### Sender

```bash
drop send file.zip 192.168.1.10:9000
```

### Receiver

```bash
drop receive --port 9000
```

The receiver listens for a TCP connection.

The sender connects to the receiver.

---

# 📦 File Transfer Protocol

DropCLI will use a simple application-level protocol.

Example:

```text
┌───────────────────────┐
│ Protocol Version      │
├───────────────────────┤
│ File Name             │
├───────────────────────┤
│ File Size             │
├───────────────────────┤
│ Chunk Size            │
├───────────────────────┤
│ File Hash             │
├───────────────────────┤
│ Encryption Metadata   │
├───────────────────────┤
│ File Chunks           │
│                       │
│ Chunk 1               │
│ Chunk 2               │
│ Chunk 3               │
│ ...                   │
├───────────────────────┤
│ Transfer Complete     │
└───────────────────────┘
```

---

# 🧩 Chunked File Transfer

Large files must not be loaded entirely into RAM.

For a 2 GB file:

```text
2 GB
│
├── Chunk 1   4 MB
├── Chunk 2   4 MB
├── Chunk 3   4 MB
├── Chunk 4   4 MB
├── ...
└── Chunk N   4 MB
```

Sender:

```text
Read chunk
   ↓
Encrypt
   ↓
Send
   ↓
Read next chunk
```

Receiver:

```text
Receive chunk
   ↓
Decrypt
   ↓
Write to disk
   ↓
Receive next chunk
```

This keeps memory usage relatively small.

---

# 🧮 Memory Usage

Suppose:

```text
File = 2 GB
Chunk = 4 MB
```

The application does not need to allocate 2 GB of RAM just to transfer the file. It can process the file as a stream of chunks. Exact memory usage depends on buffering and implementation details.

---

# 🔐 File Integrity

The sender calculates:

```text
SHA-256(original_file)
```

The receiver calculates:

```text
SHA-256(received_file)
```

Then:

```text
Original hash == Received hash
```

Result:

```text
Integrity verified ✓
```

If the hashes do not match:

```text
Integrity verification failed ✗
```

---

# 🔐 Encryption

The transfer should eventually be encrypted.

Conceptually:

```text
Original File
      ↓
Split into chunks
      ↓
Encrypt each chunk
      ↓
Network
      ↓
Decrypt each chunk
      ↓
Write file
```

Recommended authenticated encryption:

```text
AES-256-GCM
```

or:

```text
ChaCha20-Poly1305
```

A secure random key should be generated for each sharing session.

---

# 🔑 Share Code System

The user should not need to exchange IP addresses manually in the Internet version.

Example:

```text
Share Code:
X7K9-P2QA
```

The code maps to a temporary session.

Conceptually:

```text
X7K9-P2QA
      ↓
Session ID
      ↓
Sender
      ↓
Connection information
```

The share code should:

- Be generated using cryptographically secure randomness
- Expire automatically
- Be single-use when appropriate
- Be rate limited
- Never be predictable
- Not contain sensitive information

---

# 🌐 Internet P2P Architecture

LAN communication is straightforward.

The Internet version is more difficult because computers are often behind NATs and firewalls.

Architecture:

```text
                    Internet
                       │
             ┌─────────┴─────────┐
             │                   │
        Computer A          Computer B
             │                   │
            NAT                 NAT
             │                   │
             └────── Internet ────┘
```

A direct connection cannot always be established simply by knowing someone's public IP.

Therefore the advanced architecture can use:

```text
WebRTC
ICE
STUN
TURN
Signaling Server
```

---

# 🔄 Internet Connection Process

```text
Computer A
    │
    │ Create share
    ▼
Signaling Server
    │
    │ share code
    ▼
Computer B
    │
    │ Join share
    ▼
Signaling Server
    │
    │ exchange connection information
    ▼
Peer Negotiation
    │
    ├───────────────┐
    │               │
    ▼               ▼
Direct P2P       TURN Relay
    │               │
    └───────┬───────┘
            ▼
        File Transfer
```

The preferred path is direct P2P.

A relay can be used when a direct connection cannot be established.

---

# 🧠 Why the Server Exists

The server is **not necessarily a file-storage server**.

Its primary responsibilities are:

```text
Authentication
       +
Share codes
       +
Peer discovery
       +
Signaling
       +
Session management
```

It should not permanently store the user's files.

The intended architecture is:

```text
Server
  │
  ├── Who is sharing?
  ├── Who wants to receive?
  ├── How do they connect?
  └── Is this share code valid?
```

while:

```text
Peer A ─────────────── Peer B
          File data
```

handles the actual transfer whenever direct connectivity is available.

---

# 🔄 Resume Support

Large files can take a long time to transfer.

If a 2 GB transfer stops at:

```text
1.7 GB / 2 GB
```

the user should not have to start from zero.

The receiver can track completed chunks:

```text
received_chunks = 425
```

When reconnecting:

```text
Receiver:
"I already have chunks 1-425."

Sender:
"Okay, start from chunk 426."
```

Example:

```text
File
│
├── Chunk 1 ✓
├── Chunk 2 ✓
├── Chunk 3 ✓
├── ...
├── Chunk 425 ✓
├── Chunk 426 ✗
└── Chunk 427 ✗
```

Then resume from chunk 426.

---

# 📊 Transfer Performance

The CLI should display:

```text
File: Ubuntu.iso
Size: 4.2 GB

Transferred: 2.6 GB
Progress: 62%

Speed: 112 MB/s
Average: 108 MB/s

ETA: 15 sec
```

Useful metrics:

- Current speed
- Average speed
- Bytes transferred
- Total size
- Percentage
- ETA
- Connection status

Actual transfer speed depends on network, disk, CPU, OS, and whether a direct or relayed connection is used.

---

# 🔍 Peer Discovery

For LAN mode, automatic discovery can eventually be implemented.

Instead of:

```bash
drop send file.zip 192.168.1.10
```

the application could show:

```text
Available devices:

1. Pankaj-Laptop
2. Desktop-PC
3. MacBook-Pro

Select device:
```

Potential technologies:

```text
mDNS
UDP broadcast
Multicast DNS
```

---

# 🛡️ Security Model

Security is an important part of the project.

The system should protect against:

### Unauthorized access

Only a user with the valid session credentials/share secret should be able to join.

### Data interception

Use authenticated encryption.

### Tampered files

Use cryptographic hashes and authenticated encryption.

### Code guessing

Use sufficiently random, short-lived codes and rate limiting.

### Replay attacks

Sessions should expire and share codes should ideally become invalid after use.

### Malicious file names

Never blindly trust a remote file name. Sanitize paths to prevent path traversal such as:

```text
../../important-file
```

---

# ⚠️ Threat Model

DropCLI assumes the Internet may contain malicious parties.

Potential attackers may try to:

```text
Attacker
   │
   ├── guess share code
   ├── intercept traffic
   ├── modify packets
   ├── send malicious metadata
   ├── attempt path traversal
   ├── flood the signaling server
   └── replay sessions
```

The application should therefore use:

```text
Random session IDs
+
Cryptographic authentication
+
Encryption
+
Integrity verification
+
Rate limiting
+
Input validation
+
Session expiration
```

---

# 🧪 Development Roadmap

## Phase 1 — Terminal Communication

Goal:

```text
Computer A ←→ Computer B
```

Tasks:

- Create TCP server
- Create TCP client
- Send text
- Receive text
- Handle disconnects

Difficulty: **4/10**

---

## Phase 2 — Basic File Transfer

Tasks:

- Open file
- Send metadata
- Stream file
- Save received file
- Handle binary data

Example:

```bash
drop send image.jpg
```

Difficulty: **4–5/10**

---

## Phase 3 — Large File Support

Tasks:

- Chunk files
- Stream data
- Avoid excessive memory usage
- Handle large files
- Add configurable chunk size

Difficulty: **5/10**

---

## Phase 4 — Progress + Integrity

Tasks:

- Progress bar
- Transfer speed
- ETA
- SHA-256
- Error reporting

Difficulty: **5–6/10**

---

## Phase 5 — Resume

Tasks:

- Track completed chunks
- Restart interrupted transfers
- Verify chunk integrity
- Resume from the correct offset

Difficulty: **6–7/10**

---

## Phase 6 — Security

Tasks:

- Session authentication
- Encryption
- Secure random codes
- Key management
- Replay protection

Difficulty: **7/10**

---

## Phase 7 — Signaling Server

Tasks:

- WebSocket server
- Session creation
- Share codes
- Peer matching
- Session expiration

Difficulty: **7/10**

---

## Phase 8 — Internet P2P

Tasks:

- NAT traversal
- ICE
- STUN
- TURN
- WebRTC or QUIC-based transport

Difficulty: **9/10**

---

## Phase 9 — Production Hardening

Tasks:

- Unit tests
- Integration tests
- Security testing
- Load testing
- Logging
- Monitoring
- Cross-platform builds
- Automatic updates
- Documentation

Difficulty: **9–10/10**

---

# 🚀 MVP Definition

The first usable MVP should support:

```text
✓ Terminal interface
✓ Two computers
✓ Same Wi-Fi/LAN
✓ TCP connection
✓ File transfer
✓ Large files
✓ Chunked streaming
✓ Progress display
✓ SHA-256 verification
```

Example:

### Computer B

```bash
drop receive --port 9000
```

### Computer A

```bash
drop send ./movie.mp4 192.168.1.20:9000
```

Successful result:

```text
Transfer completed ✓
Integrity verified ✓
```

---

# 🏁 Final Product

The final CLI should look like:

### Sender

```bash
drop send ./movie.mp4
```

Output:

```text
DropCLI

File: movie.mp4
Size: 2.14 GB

Share Code:
X7K9-P2QA

Waiting for receiver...
```

### Receiver

```bash
drop receive X7K9-P2QA
```

Output:

```text
Connecting...
Peer found ✓
Secure connection ✓

movie.mp4
[████████████████████████] 100%

2.14 GB transferred
Average speed: 96 MB/s

SHA-256 verification ✓
Transfer complete ✓
```

---

# 📈 Scalability

The system should eventually support:

```text
1 file
     ↓
multiple files
     ↓
folder transfer
     ↓
multiple peers
     ↓
parallel transfers
```

A future architecture could evolve toward multiple signaling instances and many independent peer-to-peer sessions.

---

# 💻 Cross-Platform Support

Target platforms:

```text
Windows
Linux
macOS
```

Potential future support:

```text
Android
iOS
```

Go makes cross-platform compilation straightforward.

Example:

```bash
GOOS=windows GOARCH=amd64 go build
```

```bash
GOOS=linux GOARCH=amd64 go build
```

```bash
GOOS=darwin GOARCH=arm64 go build
```

---

# 🧰 Example Commands

## Start receiver

```bash
drop receive
```

## Send a file

```bash
drop send ./video.mp4
```

## Send to a known address in LAN mode

```bash
drop send ./video.mp4 192.168.1.20:9000
```

## Specify output directory

```bash
drop receive X7K9-P2QA --output ./downloads
```

## Limit bandwidth

```bash
drop send ./video.mp4 --limit 20MB
```

## Resume transfer

```bash
drop resume X7K9-P2QA
```

---

# 📦 Installation

## Requirements

- Go 1.XX+
- Git
- Two computers for testing

Clone the repository:

```bash
git clone https://github.com/yourusername/dropcli.git
cd dropcli
```

Install dependencies:

```bash
go mod download
```

Build:

```bash
go build -o drop
```

Run:

```bash
./drop
```

On Windows:

```powershell
go build -o drop.exe
```

Then:

```powershell
.\drop.exe
```

---

# 🧪 Testing Strategy

## Unit Tests

Test:

- Hash generation
- Encryption
- Chunk creation
- Chunk reconstruction
- Protocol parsing
- Share-code generation

## Integration Tests

Test sender → receiver with:

- 1 KB file
- 1 MB file
- 100 MB file
- 1 GB file
- 2 GB file

## Failure Tests

Simulate:

- Network disconnect
- Receiver shutdown
- Sender shutdown
- Corrupted chunk
- Invalid code
- Expired code
- Insufficient disk space

---

# 📊 Performance Goals

The application should aim for:

```text
Memory:
Small relative to file size

File handling:
Streaming

Transfer:
Limited primarily by network and disk performance

Storage:
No permanent server-side file storage

Integrity:
Cryptographic verification

Security:
Authenticated encrypted transfer
```

Actual transfer speed depends on:

- Wi-Fi/Ethernet speed
- Internet connection
- Network congestion
- CPU encryption performance
- Disk read/write speed
- NAT/relay path
- Operating system
- Hardware

---

# 🧱 Important Design Principle

> **The file should move directly between peers whenever the network allows it.**

The server should coordinate the peers rather than becoming the permanent home of the user's data.

Ideal:

```text
                 Signaling Server
                      │
               connection setup
                  /        \
                 /          \
                ▼            ▼
             Sender ======> Receiver
                   P2P
```

Fallback:

```text
Sender ───────► TURN/Relay ───────► Receiver
```

---

# 🎯 Learning Objectives

Building DropCLI teaches:

### Networking

- TCP
- UDP
- Sockets
- Ports
- IP addressing
- NAT
- NAT traversal
- WebRTC
- STUN/TURN
- WebSockets

### Systems Programming

- File I/O
- Buffers
- Streaming
- Concurrency
- Memory management
- Process communication

### Security

- Hashing
- Encryption
- Authentication
- Key derivation
- Random tokens
- Session security

### Distributed Systems

- Peer discovery
- Session coordination
- Reliability
- Fault recovery
- Resume mechanisms

### Software Engineering

- CLI design
- Protocol design
- Modular architecture
- Unit testing
- Integration testing
- Cross-platform builds

---

# 📅 Recommended Development Order

Do **not** start by implementing WebRTC.

Build in this order:

```text
TCP chat
   ↓
TCP file transfer
   ↓
Large-file streaming
   ↓
Chunk protocol
   ↓
Progress bar
   ↓
SHA-256 verification
   ↓
Resume support
   ↓
Encryption
   ↓
Share codes
   ↓
WebSocket signaling
   ↓
LAN discovery
   ↓
Internet connectivity
   ↓
STUN
   ↓
TURN fallback
   ↓
Production hardening
```

This progression makes the project progressively harder instead of trying to solve every networking problem at once.

---

# ⭐ Project Difficulty

| Version | Difficulty |
|---|---:|
| TCP terminal chat | 2/10 |
| Basic file transfer | 4/10 |
| Large-file transfer | 5/10 |
| Hash + progress | 5/10 |
| Resume | 6/10 |
| Encryption | 7/10 |
| Share-code system | 7/10 |
| Signaling server | 7/10 |
| Internet P2P | 9/10 |
| Production-grade application | 9–10/10 |

---

# 📌 Current Project Status

```text
[ ] CLI skeleton
[ ] TCP communication
[ ] Basic text messaging
[ ] File transfer
[ ] Chunked streaming
[ ] Progress bar
[ ] SHA-256 verification
[ ] Resume support
[ ] Encryption
[ ] Share codes
[ ] Signaling server
[ ] LAN discovery
[ ] Internet P2P
[ ] STUN
[ ] TURN fallback
[ ] Security hardening
[ ] Cross-platform releases
```

---

# 🌟 Long-Term Vision

DropCLI aims to become a simple command-line tool for securely transferring files between devices without requiring users to manually upload their files to a traditional cloud-storage service.

The ideal user experience should be:

```bash
drop send presentation.pdf
```

Share:

```text
X7K9-P2QA
```

The receiver enters:

```bash
drop receive X7K9-P2QA
```

And the file moves directly between the two devices whenever possible.

---

# 📜 License

Choose a license appropriate for your project, for example:

```text
MIT License
```

---

# 🤝 Contributing

Contributions are welcome.

Suggested contribution areas:

- Networking
- Security
- CLI design
- Performance optimization
- Testing
- Cross-platform support
- Documentation

---

# 👨‍💻 Author

Built as a learning and systems-engineering project focused on:

```text
Networking
+
Security
+
Distributed Systems
+
High-Speed File Transfer
```

---

## 🚀 Final Goal

**DropCLI = "AirDrop-like file sharing from your terminal."**

```text
Simple command
       ↓
Secure session
       ↓
Peer discovery
       ↓
Direct connection
       ↓
Chunked transfer
       ↓
Integrity verification
       ↓
Done ✓
```
