# Fiche Réseau - TCP vs UDP dans les Jeux Vidéo

## ✅ TCP (Transmission Control Protocol)

### 🧩 Définition
- Protocole orienté **connexion**.
- Assure une **livraison fiable** des données.
- Les paquets arrivent dans le bon ordre et sans perte (ou sont retransmis).

### 🔧 Fonctionnalités clés
- **Contrôle de flux** : évite de saturer le récepteur.
- **Contrôle de congestion** : réduit la vitesse si le réseau est chargé.
- **Vérification d'intégrité** : grâce à des checksums.
- **Accusés de réception (ACK)** : pour chaque paquet reçu.
- **Établissement de connexion en 3 étapes** (handshake).

### ✅ Avantages pour les jeux
- Idéal pour les données **critiques** qui ne doivent **jamais être perdues**.
  - Exemples : messages de chat, chargement initial de carte, inventaire.
- Permet une communication **sécurisée** et **fiable**.

### ❌ Inconvénients
- **Lent** à cause des retransmissions.
- **Latence élevée**.
- Mauvais pour les données temps réel : si un paquet est perdu, les suivants attendent.

### 🎮 Cas d'utilisation typiques
- Jeux au **tour par tour**.
- Données persistantes : scores, profils, inventaire.

---

## ⚡ UDP (User Datagram Protocol)

### 🧩 Définition
- Protocole **sans connexion**.
- Aucune garantie que les paquets arrivent ni qu’ils arrivent dans l’ordre.

### 🔧 Fonctionnalités clés
- **Très rapide** : pas de vérification de réception.
- **Léger** : pas d'établissement de connexion.
- Permet un envoi continu de données.

### ✅ Avantages pour les jeux
- Idéal pour les données **temps réel** : déplacements, tirs, position, etc.
- **Faible latence** → meilleure réactivité.
- Pas de blocage si un paquet est perdu.

### ❌ Inconvénients
- **Pas fiable** : les données peuvent être perdues.
- Nécessite des systèmes **sur mesure** pour corriger les pertes si besoin.
- Moins adapté aux connexions instables.

### 🎮 Cas d'utilisation typiques
- FPS (Counter-Strike, Fortnite).
- Jeux multijoueur temps réel (Rocket League, MOBA).
- Synchronisation d’état de jeu.

---

## 🆚 Comparaison rapide

| Caractéristique       | TCP                          | UDP                           |
|-----------------------|-------------------------------|-------------------------------|
| Connexion             | Oui (établie)                 | Non (sans état)               |
| Fiabilité             | ✅ Élevée                      | ❌ Faible                     |
| Vitesse               | ❌ Plus lent                   | ✅ Très rapide                |
| Latence               | ❌ Haute                       | ✅ Faible                     |
| Usage idéal           | Données critiques             | Données temps réel            |
| Ordre des paquets     | ✅ Garanti                     | ❌ Non garanti                |
| Surcharge             | ❌ Élevée                      | ✅ Faible                     |

---

## 📝 À retenir pour l'examen
- **UDP** = vitesse + fluidité (mais perte possible).
- **TCP** = sécurité + fiabilité (mais plus lent).
- De nombreux jeux utilisent **UDP** + ajoutent leurs propres mécanismes de fiabilité (ack, re-envoi, timestamp).

- 📦 Taille des paquets
TCP
Taille d'en-tête : 20 à 60 octets.

Taille maximale d'un segment TCP (MTU typique - Maximum Transmission Unit) :

En général : 1500 octets (Ethernet standard).

Après en-tête IP (20 octets) et TCP (20 octets), il reste 1460 octets pour les données.

Fragmentation possible si dépasse la MTU (ralentit les performances).

UDP
Taille d'en-tête : 8 octets (très léger).

Taille maximale d'un datagramme UDP :

Théorique : jusqu’à 65 535 octets (limite protocole IP).

Pratique (sans fragmentation) : souvent limité à 512 à 1472 octets selon le réseau.

Souvent recommandé de rester sous 1200 octets pour éviter la fragmentation et les pertes.


--------------------------------------------------------------------------------------------------------------
# 🕹️ Rollback Networking — Fiche Technique

## 🔍 Définition
Le **rollback networking** est une technique de synchronisation réseau utilisée dans les jeux multijoueur (souvent compétitifs) pour **masquer la latence** perçue par le joueur, en **prévoyant les entrées** et **corrigeant l'état du jeu** en cas de divergence.

---

## ⚙️ Fonctionnement Général

### Étapes principales :
1. **Simulation locale immédiate** :
   - Le jeu simule **instantanément** les actions du joueur sans attendre l’autre joueur.
   
2. **Prédiction des entrées adverses** :
   - Le moteur **devine** les entrées de l’autre joueur (ex : répète la dernière connue).

3. **Réception réseau tardive** :
   - Une fois les vraies entrées reçues, le jeu **compare** avec la prédiction.

4. **Rollback** :
   - Si les vraies entrées sont différentes → le moteur **reconstruit l’état** du jeu dans le passé.
   - Il **re-simule rapidement** jusqu’au présent avec les bonnes données.

---

## 🧠 Exigences Techniques

### ⏳ Mémoire d’état
- Le moteur doit pouvoir **sauvegarder et restaurer** l’état complet du jeu à un instant T.
- Utilisation de **snapshots** ou **copies de sauvegarde** pour pouvoir revenir en arrière.

### 🕹️ Simulation déterministe
- La simulation du jeu doit être **100 % déterministe** :
  - Même entrée + même état = même résultat.
  - Nécessite souvent de désactiver les sources d’aléatoire non contrôlées (`rand()`, flottants imprécis, etc).

### ⏱️ Input Delay
- Souvent utilisé **en combinaison avec un léger délai d’entrée** (ex. 2 frames) pour équilibrer rollback & prédiction.

---

## 📦 Intégration dans un moteur

### 🔁 Architecture typique


### 🧪 Techniques utilisées
- **Ring buffer** ou **deque** pour stocker les états passés (sur X frames).
- **Lockstep partiel** : synchronisation partielle des inputs, mais exécution locale immédiate.
- Vérification **hash d'état** pour détecter les divergences de simulation.

---

## 📉 Avantages

- **Expérience fluide** : le joueur **ne ressent pas la latence**.
- **Réactivité immédiate** (contrairement au client-server classique avec input delay).
- Très apprécié pour les jeux de **combat**, **plateforme compétitifs**, **match 1v1**.

---

## ⚠️ Inconvénients

| Limite                          | Détail |
|----------------------------------|--------|
| Complexité d’implémentation     | Sauvegarde/restauration d’état complexe |
| Simulation déterministe requise | Pas toujours triviale, surtout avec la physique |
| Coût CPU élevé                  | Re-simulation sur plusieurs frames |
| Rollback visible                | Si les prédictions sont trop différentes |

---

## 🧩 Cas d’usage typiques

- **Jeux de combat** : Street Fighter V, Skullgirls, Guilty Gear.
- **Jeux rétro multijoueur** (via GGPO).
- **Jeux 1v1 compétitifs** : stratégie en temps réel, jeux d’échecs animés.
- Moins adapté aux jeux avec **beaucoup d’entités physiques** (type Battle Royale).

---

## 🧠 Notes supplémentaires

- ⚠️ Le rollback ne fonctionne **qu’en P2P** ou architecture client synchronisé, pas bien avec un serveur arbitre centralisé (à moins de rollback côté client uniquement).
- Certains moteurs comme **RollbackNetcode** ou **GGPO SDK** peuvent être utilisés comme base.

---

## 📚 Ressources utiles

- [GGPO SDK GitHub](https://github.com/pond3r/ggpo)
- [Rollback Netcode Explained](https://www.youtube.com/watch?v=7jb0FOcImdg) – très bon résumé en vidéo
- [Article de Gaffer on Games sur la latence](https://gafferongames.com/post/what_every_programmer_needs_to_know_about
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# 📦 Fiche Technique — Protocol Buffers (Protobuf)

## 🔍 Qu’est-ce que Protocol Buffers ?

Protocol Buffers (ou **Protobuf**) est un format de **sérialisation de données binaire** développé par **Google**. Il est utilisé pour **échanger des données efficacement** entre différents systèmes, souvent dans les applications réseau ou distribuées.

> ✅ Alternative plus rapide et plus légère que JSON ou XML.

---

## 🎯 Pourquoi l’utiliser dans un jeu vidéo ?

- **Efficacité réseau** (taille réduite des messages).
- **Performance** (parsing binaire rapide).
- **Interopérabilité** (langages multiples supportés).
- **Structure bien définie** → facilite le développement client/serveur.

---

## 🧩 Fonctionnement

### 1. 📜 Définition des messages
Les structures de données sont définies dans un fichier `.proto`.

```proto
syntax = "proto3";

message PlayerPosition {
  int32 id = 1;
  float x = 2;
  float y = 3;
  float z = 4;
}
  | Format        | Avantage                          | Taille (approximative)        |
|---------------|-----------------------------------|-------------------------------|
| JSON          | Lisible humain, mais verbeux      | ~120 octets pour un message   |
| XML           | Très verbeux                      | ~200 octets                   |
| Protobuf      | Compact, rapide à parser          | ~20-30 octets                 |


🛠️ Avantages
⚡ Rapide à encoder/décoder

📉 Réduction de bande passante

🔄 Multiplateforme et multilangage

💪 Supporte versioning (ajout/suppression de champs sans casser le format)

🔐 Binaire → plus difficile à manipuler / trafiquer côté client

❗ Inconvénients

Limite	Détail
Non lisible humain	Debug plus compliqué sans outils
Requiert protoc	Génération automatique nécessaire
Pas aussi flexible que JSON	Moins pratique pour configs
🧠 Conseils d’utilisation dans un jeu
Utiliser Protobuf pour :

Synchronisation joueur/serveur

Événements réseau

État de l’environnement

Pré-générer les classes pendant la phase de build.

Utiliser SerializeToArray() ou SerializeToString() pour les sockets.

Éviter d’en envoyer trop souvent → regrouper les messages.

🧪 Exemples de messages utiles
proto
Copier
Modifier
message PlayerInput {
  int32 id = 1;
  bool jump = 2;
  bool fire = 3;
  float angle = 4;
}

message ChatMessage {
  int32 senderId = 1;
  string text = 2;
}
📚 Ressources utiles
Documentation officielle Protobuf

Outil protoc GitHub

Outils GUI : Protobuf Viewer

✅ À retenir pour l’examen ou un projet réseau

Point clé	Détail
Format	Binaire, compact, structuré
Utilisation	Définir .proto, générer code, sérialiser
Avantage	Performance + taille réduite
Inconvénient	Pas lisible, dépendance à protoc

