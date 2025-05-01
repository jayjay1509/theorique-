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

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# 🧪 Fiche Technique — Proton (Valve)

## 🎮 Qu’est-ce que Proton ?

**Proton** est une **couche de compatibilité développée par Valve** (basée sur Wine) permettant aux jeux Windows de **fonctionner sous Linux**, notamment via Steam.

> 🎯 Objectif : permettre à un joueur Linux d’exécuter un jeu Windows sans modification du jeu d’origine.

---

## 🔧 Composants techniques

Proton combine plusieurs technologies open-source :

| Composant     | Rôle |
|---------------|------|
| **Wine**      | Émulation des API Windows |
| **DXVK**      | Traduction DirectX 9/10/11 → Vulkan |
| **vkd3d-proton** | Traduction DirectX 12 → Vulkan |
| **FAudio**    | Implémentation des API audio de XAudio |
| **D9VK**      | Ancienne branche DirectX 9 vers Vulkan (intégré à DXVK) |
| **esync/fsync** | Optimisation des threads (performances multi-thread) |

---

## ✅ Avantages

- 💻 Lance **des milliers de jeux Windows** sur Linux.
- 🚀 Performances parfois **meilleures que sur Windows**.
- 📦 Intégration transparente dans Steam.
- 🔁 Mise à jour fréquente avec les dernières versions de Wine/DXVK.
- 🌍 Open source → modifiable / extensible.

---

## ❌ Inconvénients

| Limite | Détail |
|--------|--------|
| Pas 100 % compatible | Certains jeux ne se lancent pas ou ont des bugs |
| Anti-cheats | Beaucoup de systèmes anti-triche bloquent Proton |
| Dépendances Linux | Nécessite des pilotes graphiques récents et Vulkan |
| Debugging difficile | Messages d’erreurs peu clairs (logs Wine) |

---

## 🚀 Fonctionnement simplifié




Utilisation	Définir .proto, générer code, sérialiser
Avantage	Performance + taille réduite
Inconvénient	Pas lisible, dépendance à protoc




---

## ⚙️ Utilisation avec Steam

### Activer Proton :
1. Aller dans **Paramètres > Steam Play**
2. Cocher **"Activer Steam Play pour tous les titres"**
3. Choisir une version de Proton (ex: Proton 8.0)

### Lancer un jeu :
- Cliquer sur "Installer" puis "Jouer" comme avec un jeu Linux natif.
- Steam utilise Proton automatiquement.

---

## 🧠 ProtonDB

> [https://www.protondb.com](https://www.protondb.com)

- Base de données communautaire sur la **compatibilité des jeux avec Proton**.
- Classement par note : **Platinum, Gold, Silver, Bronze, Borked**.
- Contient des astuces spécifiques pour chaque jeu (lignes de commande, correctifs).

---

## 📂 Exemple de structure


---

## 🔧 Commandes utiles

| Commande | Description |
|----------|-------------|
| `PROTON_LOG=1 %command%` | Génére un fichier `steam-<appid>.log` pour le debug |
| `protontricks <appid>` | Installe des dépendances manquantes via Winetricks |
| `STEAM_COMPAT_DATA_PATH=...` | Redirige le dossier d’installation simulé |

---

## 📚 Ressources

- Proton GitHub : [https://github.com/ValveSoftware/Proton](https://github.com/ValveSoftware/Proton)
- ProtonDB : [https://www.protondb.com](https://www.protondb.com)
- Lutris (alternative non-Steam) : [https://lutris.net](https://lutris.net)
- Wine HQ : [https://www.winehq.org](https://www.winehq.org)

---

## 📝 À retenir

| Élément | Détail |
|--------|--------|
| Proton = Wine + DXVK + FAudio + tweaks |
| Supporte DirectX 9/10/11/12 via Vulkan |
| Très bon pour les joueurs Linux |
| Intégré à Steam, facile d’utilisation |
| Tous les jeux ne sont pas 100 % compatibles |

--------------------------------------------------------------------------------------------------------------------------------------------------------------------
# 🧩 Fiche Technique — Input Prediction

## 🎮 Qu’est-ce que l'Input Prediction ?

L'**Input Prediction** est une technique utilisée pour **réduire l'impact de la latence** en simulant les actions du joueur localement avant d'avoir la confirmation du serveur. Cela donne l'impression que le jeu réagit instantanément aux actions du joueur, même s'il y a un retard dû au réseau.

---

## 🔧 Fonctionnement

1. **Le joueur effectue une action** : se déplacer, tirer, etc.
2. **Simulation locale** : l'action est immédiatement appliquée sur le client local.
3. **Envoi de l’action au serveur** : le client envoie l'information au serveur.
4. **Réception de la réponse du serveur** : le serveur valide l'action et renvoie l'état mis à jour.
5. **Correction** : si l'état du serveur diffère de la simulation locale, une **réconciliation** a lieu pour ajuster l'état du client en fonction.

---

## ✅ Avantages

- **Réduction de la latence perçue** : les actions semblent instantanées.
- **Fluidité** : les joueurs n’ont pas l’impression de subir des délais dus au réseau.
- **Amélioration de l’expérience de jeu** : le jeu semble plus réactif, même sur des connexions avec une latence élevée.

---

## ❌ Inconvénients

- **Prévisions incorrectes** : si le serveur renvoie un résultat différent de la prédiction, l'état doit être corrigé, ce qui peut provoquer des "jumps" visuels.
- **Complexité** : implémentation plus complexe, nécessite une gestion précise des erreurs.
- **Rewind & Correction** : les corrections peuvent être difficiles à gérer dans des environnements très dynamiques.

---

## 🎮 Cas d'utilisation

- **Jeux de tir à la première personne (FPS)** : les joueurs se déplacent et tirent en temps réel.
- **Jeux de course** : les véhicules sont contrôlés instantanément.
- **Jeux de plateforme** : les actions sont simulées en temps réel avant confirmation serveur.

---

## 🔧 Exemple d'implémentation

1. **Prédiction des positions** : lorsqu'un joueur se déplace, le client calcule localement où il devrait se retrouver à la prochaine image.
2. **Correction d'input** : si le serveur détecte une différence (par exemple, une position différente), le client ajuste la position du joueur pour se réaligner.

---

## 📚 Ressources

- [Netcode for Games: Input Prediction](https://www.gamasutra.com/view/feature/131624/understanding_input_prediction_and_.php)
- [Game Networking: Input Prediction and Reconciliation](https://gafferongames.com/post/reliable_udp/)

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# 🧩 Fiche Technique — Lag Compensation

## 🎮 Qu’est-ce que la Lag Compensation ?

La **Lag Compensation** est une technique utilisée pour **corriger les effets de latence** dans les jeux en ligne, en particulier dans les jeux de tir où les actions du joueur, comme les tirs, doivent être corrigées si elles sont affectées par un retard réseau.

---

## 🔧 Fonctionnement

1. **Le joueur tire** : l'action est envoyée au serveur avec un délai dû à la latence.
2. **Serveur enregistre le tir** : le serveur enregistre l'instant du tir, mais doit compenser le décalage.
3. **Rewind du serveur** : le serveur "remonte dans le temps" pour simuler l'état du jeu au moment du tir, comme si le joueur avait tiré avec moins de latence.
4. **Calcul de la collision** : la position du tir est recalculée en fonction du lag compensé et de la position du joueur au moment du tir.
5. **Correction sur le client** : le client applique la correction si nécessaire.

---

## ✅ Avantages

- **Justesse des actions** : un joueur ne sera pas pénalisé par la latence quand il tire.
- **Gameplay équitable** : le système assure que même avec une latence, les tirs seront traités comme s’ils avaient été effectués dans les bonnes conditions.
- **Réduction de l'impact des connexions lentes** : les joueurs avec un ping élevé ne sont pas désavantagés.

---

## ❌ Inconvénients

- **Latence accrue pour les autres joueurs** : un serveur avec compensation de latence peut entraîner un retard dans le calcul des actions des autres joueurs.
- **Complexité d’implémentation** : nécessite un serveur robuste et des ajustements sur le client pour garantir la cohérence de l'état du jeu.
- **Problèmes avec des comportements extrêmes** : sur des latences très élevées, la compensation peut ne pas être suffisamment efficace, créant des problèmes visuels ou de gameplay.

---

## 🎮 Cas d'utilisation

- **Jeux de tir compétitifs** : les FPS multijoueurs (ex : Counter-Strike, Call of Duty).
- **Jeux de bataille royale** : chaque tir doit être calculé précisément, même avec des déconnexions.
- **Jeux de sport en ligne** : la précision est essentielle pour les passes, tirs, etc.

---

## 🔧 Exemple d'implémentation

1. **Enregistrement des tirs** : chaque tir est enregistré avec un timestamp sur le serveur.
2. **Rewind du serveur** : le serveur prend en compte la latence du joueur et ajuste l'état du jeu à l'instant du tir.
3. **Calcul des collisions** : le serveur utilise la position du joueur et la trajectoire du tir à ce moment-là.
4. **Synchronisation** : le client ajuste la position du tir et la synchronise avec l'état actuel du serveur.

---

## 📚 Ressources

- [Game Networking: Lag Compensation](https://gafferongames.com/post/lag_compensation/)
- [Lag Compensation Techniques](https://www.gamasutra.com/view/feature/130765/implementing_lag_compensation_in_.php)

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# 🧩 Fiche Technique — Client-Server vs Peer-to-Peer

## 🎮 Qu’est-ce que Client-Server vs Peer-to-Peer ?

Les modèles **Client-Server** et **Peer-to-Peer** sont deux architectures utilisées pour gérer la communication réseau dans les jeux multijoueurs. Chaque modèle a ses propres avantages et inconvénients selon le type de jeu et les objectifs du réseau.

---

## 🔧 **Client-Server** : Le modèle traditionnel

### 🏛️ Fonctionnement
Dans un modèle **Client-Server**, un **serveur central** est responsable de la gestion du jeu, des états des joueurs, de la logique, et de la communication entre les clients. Les clients (joueurs) se connectent au serveur pour envoyer et recevoir des informations.

### ✅ Avantages
- **Contrôle centralisé** : Le serveur contrôle l’état du jeu et la logique, ce qui simplifie la gestion des tricheurs et assure la cohérence du jeu.
- **Sécurité** : Le serveur est responsable de toutes les actions, ce qui rend la triche plus difficile, car le client ne peut pas manipuler les données.
- **Scalabilité** : Le serveur peut gérer un grand nombre de clients simultanément (avec des ressources matérielles suffisantes).
- **Latence contrôlée** : Le serveur peut optimiser le réseau et limiter la latence.

### ❌ Inconvénients
- **Dépendance au serveur** : Si le serveur tombe, tout le jeu s’arrête. Les joueurs dépendent du serveur pour toutes les actions.
- **Coût élevé** : Les serveurs dédiés peuvent être coûteux en termes d’infrastructure et de maintenance.
- **Latence serveur** : La latence entre le client et le serveur peut affecter l’expérience du joueur, notamment pour les joueurs éloignés géographiquement.

### 🎮 Cas d'utilisation
- **Jeux massivement multijoueurs en ligne (MMO)** : World of Warcraft, Final Fantasy XIV.
- **Jeux de stratégie en ligne** : Starcraft, Age of Empires.
- **Jeux de rôle** : MMORPGs (ex: Elder Scrolls Online).

---

## 🔧 **Peer-to-Peer** : Le modèle décentralisé

### 🏛️ Fonctionnement
Dans un modèle **Peer-to-Peer (P2P)**, chaque joueur (ou client) agit à la fois comme **serveur et client**. Les joueurs communiquent directement entre eux sans passer par un serveur central.

### ✅ Avantages
- **Pas de serveur dédié** : Cela réduit les coûts d’infrastructure, car chaque joueur fournit une partie de la bande passante et des ressources.
- **Latence réduite** : Les joueurs peuvent se connecter directement entre eux, ce qui peut réduire la latence pour les joueurs proches.
- **Scalabilité** : Le modèle P2P peut facilement gérer un grand nombre de joueurs sans nécessiter un serveur central coûteux.
- **Moins de dépendance** : Le jeu continue de fonctionner même si un joueur se déconnecte, tant qu'il y a suffisamment de joueurs restants pour gérer la session.

### ❌ Inconvénients
- **Sécurité et triche** : La gestion des tricheurs est plus complexe, car les données peuvent être modifiées localement sur les clients sans validation serveur.
- **Problèmes de synchronisation** : Les joueurs peuvent avoir des états de jeu différents, ce qui peut entraîner des erreurs de synchronisation (par exemple, un joueur voit un autre joueur à un endroit différent de la réalité).
- **Latence variable** : La latence peut varier considérablement en fonction de la connexion du joueur, ce qui peut nuire à l’expérience de jeu.
- **Connexion instable** : Le modèle P2P peut être moins stable, surtout dans des situations où les connexions réseau des joueurs sont de mauvaise qualité.

### 🎮 Cas d'utilisation
- **Jeux de tir à la première personne (FPS)** : Call of Duty (certains modes), Fortnite (anciennement).
- **Jeux de combat** : Street Fighter V, Mortal Kombat 11.
- **Jeux mobiles** : Jeux multijoueurs sur mobile qui n’ont pas d'infrastructure serveur dédiée.

---

## 🆚 **Comparaison rapide : Client-Server vs Peer-to-Peer**

| Caractéristique              | **Client-Server**                | **Peer-to-Peer (P2P)**          |
|------------------------------|----------------------------------|---------------------------------|
| **Contrôle**                  | Serveur centralisé               | Décentralisé, chaque joueur gère |
| **Sécurité**                  | Haute, difficile à tricher       | Plus faible, triche plus facile |
| **Latence**                   | Dépend de la distance au serveur | Dépend de la qualité de la connexion des joueurs |
| **Scalabilité**               | Peut supporter de nombreux joueurs | Limité par les ressources des joueurs |
| **Coût**                      | Coût d'infrastructure élevé      | Moins coûteux, aucun serveur dédié |
| **Fiabilité**                 | Plus fiable                      | Moins fiable, peut souffrir de déconnexions |
| **Exemples**                  | MMO, jeux de stratégie, RPG      | FPS, jeux de combat, jeux mobiles |

---

## 💡 À retenir pour l'examen
- **Client-Server** est plus sécurisé et fiable, mais coûteux et sujet à des problèmes de latence pour les joueurs distants.
- **Peer-to-Peer** est plus économique, mais peut souffrir de problèmes de latence et de sécurité, et n'est pas aussi stable que le modèle Client-Server.

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# 🧩 Fiche Technique — Packet Structure

## 🎮 Qu'est-ce que la Structure de Paquet ?

La structure d'un paquet réseau définit comment les données sont organisées lorsqu'elles sont envoyées entre le client et le serveur dans un jeu en ligne. Une structure bien définie permet une communication efficace, réduisant les erreurs et la latence.

---

## 🔧 **Composants de la Structure de Paquet**

### 1. **En-tête (Header)**
L'en-tête d'un paquet contient des informations nécessaires à son acheminement et à son traitement.

- **Identifiant de paquet** : Un identifiant unique pour chaque paquet pour permettre au récepteur de suivre la séquence des paquets.
- **Adresse source et destination** : Identifie les points de communication (adresse IP et port).
- **Longueur du paquet** : La taille totale du paquet (données + en-tête).
- **Numéro de séquence** : Indique la position du paquet dans la séquence d'envoi.
- **Checksum** : Un code de vérification pour s'assurer que le paquet n’a pas été corrompu en transit.

### 2. **Corps du paquet (Payload)**
Le corps contient les données réelles qui sont envoyées. Cela peut inclure :

- **Données de jeu** : Par exemple, la position d'un joueur, les mouvements, les actions dans le jeu.
- **Commandes de jeu** : Les informations envoyées du client au serveur pour mettre à jour l'état du jeu.
- **Données de contrôle** : Des informations pour aider à la synchronisation ou à la gestion du jeu, comme les scores, l'état des ressources.

### 3. **Numéro de confirmation / Ack (dans TCP)**
Si le protocole utilisé est **TCP**, un champ supplémentaire pour l'accusé de réception est ajouté. Cela permet de confirmer la réception d'un paquet et de déclencher une nouvelle tentative en cas de perte de paquet.

---

## ✅ **Avantages d'une Bonne Structure de Paquet**

- **Efficient** : Les données sont envoyées dans un format standardisé, facilitant l'encodage/décodage.
- **Fiabilité** : Un bon paquet garantit que les informations arrivent intactes (surtout avec TCP).
- **Sécurité** : Les informations de validation comme le checksum empêchent l'intégrité des données d’être corrompue.

---

## 🆚 **Exemple de Structure de Paquet**

Voici un exemple simple de structure de paquet pour un jeu multijoueur utilisant UDP :

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# 🧩 Fiche Technique — NAT Traversal & Matchmaking

## 🎮 Qu'est-ce que NAT Traversal et Matchmaking ?

**NAT Traversal** est la technique permettant aux joueurs derrière des routeurs ou pare-feu (NAT) de se connecter et de communiquer entre eux dans un jeu en ligne. Le **Matchmaking** fait référence au processus de mise en relation des joueurs afin de les placer dans une session de jeu adaptée à leur niveau et préférences.

---

## 🔧 **NAT Traversal** : Traversée de NAT

### 🏛️ Fonctionnement
Le **NAT** (Network Address Translation) est une méthode utilisée par de nombreux routeurs pour modifier les adresses IP privées des utilisateurs en une adresse publique. Cela peut poser un problème pour les jeux en ligne, car les joueurs derrière un NAT ne sont pas directement accessibles.

Les techniques de **NAT Traversal** permettent de résoudre ces problèmes en facilitant la communication directe entre les clients derrière différents NAT.

### ✅ Techniques principales
- **UPnP (Universal Plug and Play)** : Permet à un client de configurer automatiquement le routeur pour ouvrir des ports nécessaires pour la communication.
- **STUN (Session Traversal Utilities for NAT)** : Permet à un client de déterminer son adresse publique et de s’assurer que le port est accessible via le NAT.
- **TURN (Traversal Using Relays around NAT)** : Si STUN échoue, TURN permet de relayer les données via un serveur intermédiaire pour assurer la communication.
- **ICE (Interactive Connectivity Establishment)** : Combine STUN et TURN pour déterminer la meilleure façon de communiquer.

### ✅ Avantages
- Permet des connexions directes entre joueurs, même si l'un des joueurs est derrière un NAT.
- Réduit la latence et améliore l'expérience de jeu en permettant des connexions P2P.

### ❌ Inconvénients
- Peut être complexe à implémenter dans des environnements NAT très stricts.
- Nécessite des serveurs relais TURN, ce qui peut augmenter les coûts.

---

## 🔧 **Matchmaking** : Mise en relation des joueurs

### 🏛️ Fonctionnement
Le matchmaking est le processus qui permet de trouver un adversaire ou une équipe adaptée à un joueur dans un jeu multijoueur. Il prend en compte plusieurs facteurs :

- **Niveau de compétence** : Associer des joueurs avec des compétences similaires.
- **Latence** : Essayer de minimiser la distance géographique et la latence entre les joueurs.
- **Préférences** : Par exemple, préférences de serveur ou mode de jeu.
- **Disponibilité** : Les joueurs en ligne et leurs horaires.

### ✅ Techniques principales
- **ELO Rating** : Utilisé dans les jeux compétitifs (comme les échecs ou League of Legends) pour évaluer la compétence d’un joueur et l’associer à d’autres joueurs du même niveau.
- **Serveurs régionaux** : Utiliser des serveurs proches de la localisation géographique des joueurs pour réduire la latence.
- **Systèmes de parties aléatoires** : Utilisation d'algorithmes qui prennent en compte la latence et le niveau des joueurs pour faire des matchs équilibrés.

### ✅ Avantages
- Permet aux joueurs de trouver rapidement des parties.
- Assure des jeux équilibrés en termes de niveau de compétence et de latence.

### ❌ Inconvénients
- Le matchmaking peut ne pas être parfait, surtout dans des régions avec peu de joueurs.
- Peut entraîner des délais d'attente plus longs si les joueurs ont des préférences strictes.

---

## 🆚 **Comparaison : NAT Traversal vs Matchmaking**

| Caractéristique            | **NAT Traversal**                 | **Matchmaking**                     |
|----------------------------|------------------------------------|-------------------------------------|
| **Objectif**                | Permet aux joueurs derrière un NAT de se connecter entre eux. | Met en relation les joueurs pour créer des parties équilibrées. |
| **Technique utilisée**      | UPnP, STUN, TURN, ICE              | ELO, Serveurs régionaux, Systèmes aléatoires |
| **Problème résolu**         | Connexion P2P entre joueurs sous NAT | Trouver un adversaire ou une équipe de niveau similaire |
| **Impact sur le jeu**       | Réduit la latence et améliore l'expérience de jeu en permettant des connexions directes. | Améliore l'équilibre des parties et réduit les frustrations des joueurs. |

---

## 💡 À Retenir pour l'Examen

- **NAT Traversal** est essentiel pour permettre aux joueurs de se connecter directement, même s'ils sont derrière des routeurs ou des pare-feu.
- **Matchmaking** assure une expérience de jeu fluide en plaçant les joueurs dans des parties équilibrées et adaptées à leurs préférences.
- Les deux techniques améliorent l'expérience de jeu, mais elles abordent des problèmes différents : la connexion et l’équilibre des parties.




--------------------------------------------------------------------------------------------------------------------------------------------------------------------------
📄 Fiche Technique : Les 7 Couches du Modèle OSI
N°	Couche	Rôle Principal	Exemples de Protocoles / Matériel
7	Application	Interface avec l'utilisateur / les logiciels	HTTP, FTP, SMTP, DNS
6	Présentation	Format des données, chiffrement, compression	SSL/TLS, JPEG, MPEG, ASCII, JSON
5	Session	Gestion des connexions, ouverture/fermeture de sessions	NetBIOS, RPC, PPTP
4	Transport	Fiabilité, contrôle d’erreurs, segmentation	TCP (fiable), UDP (rapide)
3	Réseau	Routage des paquets entre réseaux	IP, ICMP, IGMP
2	Liaison de données	Transmission des trames sur un même réseau physique	Ethernet, Wi-Fi (802.11), ARP, PPP
1	Physique	Transmission des bits sur le support physique	Câble RJ45, fibre optique, ondes radio

🧠 À retenir :
Chaque couche a une fonction spécifique et communique avec ses couches voisines.

Les données sont encapsulées en descendant (Application → Physique) et désencapsulées en montant (Physique → Application).

Les modèles TCP/IP (utilisé dans l'Internet) sont plus simplifiés (4 couches seulement).


-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


📘 Modèle OSI – Les 7 couches
Couche	Nom	Rôle principal	Exemples
7	Application	Interface entre l’utilisateur et le réseau	HTTP, FTP, SMTP, DNS
6	Présentation	Traduction, chiffrement, compression des données	SSL/TLS, JPEG, PNG, XML, ASCII
5	Session	Ouverture, gestion et fermeture des sessions	NetBIOS, RPC
4	Transport	Contrôle de flux, fiabilité, gestion des erreurs	TCP, UDP
3	Réseau	Routage et adressage IP	IP, ICMP, IGMP
2	Liaison de données	Encadrement des données, détection d’erreurs sur le support local	Ethernet, PPP, ARP
1	Physique	Transmission des bits sur le support matériel	Câble, fibre optique, Wi-Fi, Bluetooth

🌐 Modèle TCP/IP – Les 4 couches
Couche	Nom	Correspondance OSI	Rôle principal	Exemples
4	Application	Couches 5-6-7 OSI	Services réseau aux applications	HTTP, FTP, DNS, SMTP
3	Transport	Couche 4 OSI	Transport fiable ou rapide des données	TCP, UDP
2	Internet	Couche 3 OSI	Adressage et routage inter-réseaux	IP, ICMP
1	Accès réseau	Couches 1-2 OSI	Transmission des données sur le réseau physique	Ethernet, Wi-Fi, ARP

🔁 Comparaison visuelle OSI vs TCP/IP
markdown
Copier
Modifier
Modèle OSI           Modèle TCP/IP
------------         ----------------
7. Application    →  4. Application
6. Présentation   →     "
5. Session        →     "
4. Transport      →  3. Transport
3. Réseau         →  2. Internet
2. Liaison        →  1. Accès réseau
1. Physique       →     "
🧠 Astuce pour retenir les 7 couches OSI (du haut vers le bas) :
"All People Seem To Need Data Processing"
→ Application, Presentation, Session, Transport, Network, Data Link, Physical

Ou en français :

"Au Peu Sur Terre, Nous Livrons Physiquement"
→ Application, Présentation, Session, Transport, Réseau, Liaison, Physique


---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


📦 Taille des paquets TCP vs UDP
Élément	UDP	TCP
Taille d’en-tête	8 octets	20 octets (minimum), jusqu'à 60
Taille max des données	65 507 octets (en théorie)	Dépend du MTU, mais peut être fragmenté
Taille totale max (IPV4)	65 535 octets (header IP inclus)	65 535 octets (idem)
Fragmentation	Gérée par IP	Gérée par TCP et IP
Transmission typique	< 512 octets (DNS, VoIP...)	Variable (de quelques octets à plusieurs Ko)

📌 Détails importants :
📘 UDP (User Datagram Protocol)
En-tête fixe : 8 octets

Aucun contrôle de flux, de congestion ni de réémission.

Taille max utile (données) : 65 507 octets = 65 535 - 8 (UDP) - 20 (IP)

Utilisé pour des données rapides : VoIP, DNS, vidéos, jeux...

📗 TCP (Transmission Control Protocol)
En-tête minimum : 20 octets, extensible avec options (jusqu'à 60)

Taille utile variable, mais souvent limitée à la taille du MTU (~1460 octets) sans fragmentation.

Gère la fiabilité : accusés de réception, retransmissions, etc.

Utilisé pour : web (HTTP/HTTPS), email, FTP, etc.

📐 MTU : Maximum Transmission Unit
En général : 1 500 octets (Ethernet)

TCP doit se conformer à cette limite (en-tête IP + TCP ≤ 40, donc payload ≈ 1 460 octets sans fragmentation)


--------------------------------------------------------------------------------------------------------------------------------------------------------------------




