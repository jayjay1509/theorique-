# theorique-
TCP (Transmission Control Protocol)

1. Définition

Protocole orienté connexion.

Assure une livraison fiable des données.

Les paquets arrivent dans le bon ordre et sans perte (ou sont retransmis).

2. Fonctionnalités clés

Contrôle de flux : évite de saturer le récepteur.

Contrôle de congestion : réduit la vitesse si le réseau est chargé.

Vérification d'intégrité : grâce à des checksums.

Accusés de réception (ACK) : pour chaque paquet reçu.

Trois étapes d'établissement de connexion (handshake).

3. Avantages pour les jeux

Idéal pour les données critiques qui ne doivent jamais être perdues (ex. : messages de chat, chargement initial de la map).

Permet une communication sécurisée et fiable.

4. Inconvénients

Lent à cause de la retransmission automatique en cas de perte.

Plus de latence (ping élevé).

Mauvais pour les données temps réel (si un paquet est perdu, les suivants attendent).

5. Cas d'utilisation dans les jeux

Jeux au tour par tour (comme les échecs).

Données non urgentes (inventaire, scores, etc.).

⚡ UDP (User Datagram Protocol)

1. Définition

Protocole sans connexion.

Aucune garantie que les paquets arrivent ni qu’ils arrivent dans l’ordre.

2. Fonctionnalités clés

Très rapide, car pas de vérification.

Pas de surcharge liée à la connexion.

Permet un envoi constant de données.

3. Avantages pour les jeux

Idéal pour les données temps réel : mouvements de joueur, tir, position, etc.

Pas de blocage si un paquet est perdu.

Faible latence = meilleure réactivité.

4. Inconvénients

Pas fiable : perte de données possible.

Nécessite des systèmes personnalisés pour vérifier ou corriger les erreurs.

Peut poser problème pour les réseaux instables.

5. Cas d'utilisation dans les jeux

FPS (ex. : Counter-Strike, Fortnite).

Jeux multijoueur temps réel (Rocket League, MOBA).

Systèmes de synchronisation de position.

🆚 Comparaison rapide

Caractéristique

TCP

UDP

Connexion

Oui (établie)

Non (sans état)

Fiabilité

Élevée (retransmission)

Faible (pas de garantie)

Vitesse

Plus lent

Très rapide

Latence

Haute

Faible

Usage idéal

Données critiques

Données temps réel

Ordre des paquets

Garanti

Non garanti

Surcharge

Élevée

Faible

📌 À retenir pour l'examen

UDP = vitesse + fluidité (mais perte possible).

TCP = sécurité + fiabilité (mais plus lent).

Beaucoup de jeux utilisent UDP, mais recréent certaines garanties de TCP manuellement (accusés de réception, correction de pertes, etc.).

