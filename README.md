# TP4 — Communication Réseau par Datagrammes UDP en Java
> **Module :** Systèmes Répartis / Applications Réparties  
> **Technologies :** Java SE (Sockets UDP : `DatagramSocket`, `DatagramPacket`, Threads)

Ce projet illustre la mise en œuvre progressive d'une communication réseau en mode non connecté (**UDP**) en Java, allant d'un échange unidirectionnel simple jusqu'à une application de chat multi-utilisateurs avec diffusion (*broadcast*).

---

## 📁 Structure du Projet

```text
tp4/
├── bin/                          # Fichiers .class compilés
├── src/
│   ├── App.java                  # Classe d'exemple / point d'entrée par défaut
│   ├── clientPackage/
│   │   ├── Client_4_1.java       # Client UDP simple (envoi uniquement)
│   │   ├── Client_4_12.java      # Client UDP bidirectionnel (Thread de réception)
│   │   └── Client_4_2.java       # Client UDP pour salon multi-utilisateurs
│   └── serverPackage/
│       ├── Server_4_1.java       # Serveur UDP simple (écoute et affichage)
│       ├── Server_4_12.java      # Serveur UDP bidirectionnel (réception + réponse console)
│       └── Server_4_2.java       # Serveur UDP multi-clients (relais / broadcast)
└── README.md
```

---

## ⚙️ Prérequis

- **Java Development Kit (JDK) 8** ou version ultérieure (JDK 11, 17 ou 21 recommandés).
- Un terminal (PowerShell, Bash, CMD) ou un IDE (VS Code, IntelliJ IDEA, Eclipse).

---

## 🚀 Compilation

Depuis la racine du projet (`tp4/meow`), compilez l'ensemble des sources dans le dossier `bin` :

```bash
# Création du dossier de sortie (si nécessaire)
mkdir bin

# Compilation de tous les fichiers Java
javac -encoding UTF-8 -d bin src/clientPackage/*.java src/serverPackage/*.java src/App.java
```

---

## 📖 Guide d'Exécution & Scénarios

Le port d'écoute utilisé par défaut pour tous les serveurs est le port **`1234`** sur `localhost` (ou `0.0.0.0`).

### 1️⃣ Étape 4.1 : Communication Unidirectionnelle (Client ➔ Serveur)

- **Principe :** Le client saisit un pseudo puis envoie des messages. Le serveur les reçoit et les affiche dans sa console avec l'adresse IP et le port source.
- **Exécution :**
  1. Ouvrez un premier terminal et lancez le serveur :
     ```bash
     java -cp bin serverPackage.Server_4_1
     ```
  2. Ouvrez un deuxième terminal et lancez le client :
     ```bash
     java -cp bin clientPackage.Client_4_1
     ```
  3. Saisissez votre pseudo, puis envoyez vos messages. Tapez `exit` pour fermer le client.

---

### 2️⃣ Étape 4.1.2 : Dialogue Bidirectionnel 1-à-1 (Full-Duplex)

- **Principe :** Le client et le serveur peuvent envoyer et recevoir des messages simultanément grâce à l'utilisation de **Threads** séparés pour l'écoute (évite le blocage lors de la saisie clavier).
- **Exécution :**
  1. Lancez le serveur bidirectionnel :
     ```bash
     java -cp bin serverPackage.Server_4_12
     ```
  2. Lancez le client bidirectionnel :
     ```bash
     java -cp bin clientPackage.Client_4_12
     ```
  3. Envoyez un premier message depuis le client (le serveur mémorise l'adresse du client).
  4. Le serveur et le client peuvent désormais s'échanger des messages en temps réel.

---

### 3️⃣ Étape 4.2 : Chat Multi-utilisateurs avec Diffusion (*Broadcast*)

- **Principe :** Un serveur central conserve la liste dynamique des adresses des clients connectés (`Set<SocketAddress>`). Dès qu'un client émet un message, le serveur le relaie à **tous les autres clients** connectés sans lui renvoyer son propre message.
- **Exécution :**
  1. Démarrez le serveur de broadcast :
     ```bash
     java -cp bin serverPackage.Server_4_2
     ```
  2. Ouvrez plusieurs terminaux distincts et démarrez un client dans chacun :
     ```bash
     # Terminal Client 1 (ex: Alice)
     java -cp bin clientPackage.Client_4_2

     # Terminal Client 2 (ex: Bob)
     java -cp bin clientPackage.Client_4_2

     # Terminal Client 3 (ex: Charlie)
     java -cp bin clientPackage.Client_4_2
     ```
  3. Chaque message envoyé par l'un des participants est automatiquement diffusé et affiché en direct chez tous les autres.

---

## 🧠 Notions Clés Abordées

- **Protocole UDP (`User Datagram Protocol`) :** Mode non connecté (aucun `handshake` préalable), chaque paquet (`DatagramPacket`) transporte de manière autonome ses données, sa taille, l'adresse IP et le port du destinataire.
- **`DatagramSocket` :**
  - Côté serveur : lié à un port fixe via `bind(new InetSocketAddress(PORT))`.
  - Côté client : port dynamique attribué automatiquement par le système d'exploitation (`new DatagramSocket()`).
- **Encodage & Décodage :** Conversion explicite des chaînes en octets (`getBytes(StandardCharsets.UTF_8)`) et inversement (`new String(..., StandardCharsets.UTF_8)`).
- **Concurrence & Multi-threading :** Utilisation d'un thread dédié pour la méthode bloquante `socket.receive(...)` afin de maintenir l'interface de saisie utilisateur (`Scanner(System.in)`) réactive en continu.
- **Routage applicatif :** Mémorisation d'adresses d'expéditeurs sous forme de `SocketAddress` pour permettre la réexpédition sélective.
"# tp4_dar-" 
