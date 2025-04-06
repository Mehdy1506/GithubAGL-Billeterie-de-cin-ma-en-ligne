# Document de Spécifications

**Projet** : *Billetterie en ligne cinéma à plusieurs salles*  

**Date** : **05/04/2025**

## 1. Sujet du projet

**Nom du projet :** Billetterie en ligne cinéma à plusieurs salles  

**Nom et prénom des auteurs :**
- BenMohamed YASMINE  
- Abdelmoula RAHMA  
- Aouini MEHDI  

**Date :** 05/04/2025  

---

## 2. Introduction au projet

Le projet CinemaVF est une application de billetterie en ligne développée en Java, avec interface
graphique (GUI) sous Swing. Le but est de permettre à un utilisateur (client ou administrateur)
d’interagir avec un système de gestion de films projetés dans un cinéma composé de plusieurs salles.

Ce projet entre dans le cadre du cours d’AGL (Approche Génie Logiciel) et de Programmation
Orientée Objet. Il représente un exemple concret de développement logiciel en respectant les bonnes
pratiques de modularité, séparation des responsabilités, et d’interface utilisateur.

Nous avons choisi ce projet car il est à la fois réaliste, motivant et challengeant. Il permet de
travailler sur une problématique courante dans le domaine du digital, tout en renforçant nos com-
pétences en POO, architecture logicielle et développement GUI en Java. Il était parmi nos premiers
choix, notamment pour ses aspects concrets et ses possibilités d’évolution.

---

## 3. Spécification du projet

### 3.1 Notions de base et contraintes

- **Technologie :** Java SE avec GUI Swing (aucune base de données, persistance simulée)
- **Structure MVC :** Séparation claire entre modèles, interfaces et gestion de données
- **Gestion des erreurs :** Utilisation de packages exceptions pour modularité
- **Partage de données :** Géré via `CinemaDataService.java`

### 3.2 Acteurs du système

#### Administrateur

- Connexion sécurisée via `AdminLogIn.java`  
- Accès à un tableau de bord (`Dashboard.java`) contenant :  
  - **Statistiques dynamiques :**  
    - Nombre total de billets vendus  
    - Nombre de films actuellement en diffusion  
    - Revenu total généré (calculé à partir des ventes de billets)  
- Ajout / modification / suppression de films via `AddMovies.java`  
- Gestion des projections (date, salle, capacité) via `EditScreening.java`

#### Client

- Connexion (`ClientLogIn.java`) ou inscription si le client n’a pas de compte (`ClientSignUp.java`)
- Consultation de films disponibles (`AvailMovies.java`)
- Achat de billets (`BuyTicket.java`) :  
  - Sélection d’un film et de la quantité de billets  
  - Affichage d’un reçu (`Receipt.java`)

### 3.3 Interfaces graphiques (GUI)

- **`WelcomeInterface.java`** : Interface d’entrée du programme, redirige vers les modules admin / client
# Admin Interfaces
- **AdminLogin.java** : Interface de connexion  
- **Dashboard.java** : Affichage de quelques statistiques  
- **AddMovies.java** : CRUD sur films (via `JTable` et `DefaultTableModel`)  
- **EditScreening.java** : Liaison film → séance (salle, date, capacity)

# Client Interfaces
- **ClientLogIn.java** et **ClientSignUp.java** : Authentification client
- **AvailMovies.java** : Visualisation des films (lecture seule)
- **BuyTicket.java** : Achat de billets + réception automatique (Receipt.java)

## 3.4 Modèles de données (models)
- **Admin.java** : Attributs et méthodes liées à l’authentification admin
- **User.java** : Données utilisateur client (nom, mot de passe, ID...)
- **Personne.java** : Classe mère (héritage) commune à Admin et User
- **Movie.java** : Attributs (titre, genre, durée, ID...)
- **CinemaHall.java** : Salles de cinéma avec capacité
- **Screening.java** : Représente une séance (film + salle + date)
- **Booking.java** : Réservation client (film, utilisateur, quantité, ID)
- **Ticket.java** : Billet généré (affiché sous forme de reçu)

## 3.5 Gestion des données partagées
**CinemaDataService.java** : Gère la mémoire partagée entre interfaces :
- Liste des utilisateurs, films, projections, réservations, etc.
- Méthodes d’ajout, suppression, récupération
- Sert d’API interne entre les composants

## 3.6 Gestion des erreurs (exceptions)
Structure modulaire des erreurs pour validation et robustesse :
- **BookingTicketsExceptions** : Erreurs liées aux réservations (quantité, disponibilité)
- **CinemaHallExceptions** : Capacité de salle invalide ou salle inexistante
- **InputExceptions** : Champs vides, mauvaise saisie
- **MovieExceptions** : Doublons de films, erreur d’ID
- **ScreeningsExceptions** : Conflits de date ou incohérences de séances
- **UsersExceptions** : Erreurs d’inscription / connexion utilisateur

Chaque exception est personnalisée pour afficher des messages explicites à l’utilisateur via des boîtes de dialogue Swing (JOptionPane).
## 4. Diagrammes de cas d’utilisation
## 5 Priorités des cas d’utilisation

### 5.1 Table de Décision pour « Acheter un billet de cinéma »

**Préconditions :**
- Client inscrit  
- Film existant et programmé  
- Siège disponible  
- Paiement autorisé  
- Place disponible pour le film  

**Postcondition :** Achat du billet accepté (résultat final)

**Table de Décision :**
| Conditions / Règles               | Règle 1 | Règle 2 | Règle 3 | Règle 4 | Règle 5 | Règle 6              |
|-----------------------------------|---------|---------|---------|---------|---------|----------------------|
| **Client inscrit**                | F       | T       | T       | T       | T       | T                    |
| **Film existant et programmé**    | –       | F       | T       | T       | T       | T                    |
| **Siège disponible**              | –       | –       | F       | T       | T       | T                    |
| **Paiement autorisé**             | –       | –       | –       | F       | T       | T                    |
| **Place disponible pour le film** | –       | –       | –       | –       | F       | T                    |
| **Achat du billet accepté**       | F       | F       | F       | F       | F       | T                    |
| **Jeux de tests / Combinaisons**  | 1       | 1       | 1       | 1       | 1       | 3^2(ou selon le besoin)|

**Explications des règles :**  
- **Règle 1** : Le client n’est pas inscrit (F). Dans ce cas, quelle que soit la validité des autres conditions, l’achat est refusé.  
- **Règle 2** : Le client est inscrit (T), mais le film n’existe pas ou n’est pas programmé (F). L’achat est refusé.  
— Règle 3 : Le client est inscrit et le film est programmé, mais aucun siège n’est disponible.
L’achat est refusé.

— Règle 4 : Toutes les conditions précédentes sont valides, mais le paiement est refusé. L’achat
est donc refusé.

— Règle 5 : Même avec un paiement autorisé, il n’y a plus de place disponible pour le film.
L’achat est refusé.

— Règle 6 : Toutes les préconditions sont remplies. L’achat est accepté.

## 5.2 Table de Décision pour « Ajouter un film » (Acteur : Admin)

**Préconditions :**
— Admin authentifié  
— Titre du film fourni  
— Vérification de l’existence du film  
— Données complètes et valides

**Postcondition :** Ajout du film accepté (résultat final)

**Table de Décision :**
| Conditions / Règles           | Règle 1 | Règle 2 | Règle 3 | Règle 4 | Règle 5                |
|-------------------------------|---------|---------|---------|---------|------------------------|
| Admin authentifié             | F       | T       | T       | T       | T                      |
| Titre du film fourni          | –       | F       | T       | T       | T                      |
| Film déjà existant            | –       | –       | T       | F       | F                      |
| Données complètes et valides  | –       | –       | –       | F       | T                      |
| **Ajout du film accepté**     | F       | F       | F       | F       | T                      |
| Jeux de tests / Combinaisons  | 1       | 1       | 1       | 1       | 3² (ou selon le besoin)|

Explications des règles :  
— Règle 1 : L’Admin n’est pas authentifié. Quelle que soit la suite, l’ajout du film est refusé.  
— Règle 2 : L’Admin est authentifié, mais le titre du film n’est pas fourni. L’ajout est refusé.  
— Règle 3 : Le film existe déjà dans le système. L’ajout est refusé pour éviter un doublon.  
— Règle 4 : Les données du film sont incomplètes ou invalides. L’ajout est refusé.  
— Règle 5 : Toutes les conditions sont remplies. L’ajout est accepté  
