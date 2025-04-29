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

