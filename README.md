
# Esig'estion — Application de gestion des inscriptions aux dominantes

> Projet Java réalisé par **Ancelmo QUENUM** & **Iyabo AROGOUN**  
> Version 2.0 — ESIGELEC

---

## Présentation

Esig'estion est une application de bureau développée en Java permettant de gérer les inscriptions des étudiants aux sessions de dominantes de l'ESIGELEC. Elle distingue deux profils d'utilisateurs : **étudiant** et **administrateur**, avec des interfaces adaptées à chaque rôle.

---

## Prérequis

- Java JDK 8 ou supérieur
- Eclipse IDE (recommandé)
- Pilote JDBC Oracle : fichier `ojdbc7_g.jar` fourni dans le dossier `lib/`
- Accès réseau à la base de données Oracle de l'ESIGELEC

---

## Installation et configuration

### 1. Importer le projet dans Eclipse

1. Ouvrir Eclipse
2. `File` → `Import` → `Existing Projects into Workspace`
3. Sélectionner le dossier `project_java_QUENUM_AROGOUN`

### 2. Ajouter la librairie JDBC Oracle

1. Clic droit sur le projet → `Properties`
2. Aller dans `Java Build Path` → onglet `Libraries` → `Classpath`
3. Cliquer sur `Add External JARs...`
4. Sélectionner le fichier `lib/ojdbc7_g.jar`
5. Valider avec `Apply and Close`

>  Sans cette étape, la connexion à la base de données ne fonctionnera pas, même avec les bons identifiants.

### 3. Configurer la connexion à la base de données

Le fichier à modifier est : `src/dao/ConnectionDAO.java`

**Sur PC personnel (hors réseau école) :**
```java
final static String URL = "jdbc:oracle:thin:@oracle.esigelec.fr:1521:orcl";
```

**Sur PC de l'école (réseau intranet) :**
```java
// Décommenter cette ligne et commenter la précédente :
final static String URL = "jdbc:oracle:thin:@srvoracledb.intranet.int:1521:orcl";
```

Les identifiants de connexion sont :
```java
final static String LOGIN = "C##BDD3_3";
final static String PASS  = "BDD33";
```

---

## Lancement de l'application

Le point d'entrée se trouve dans :

```
src/tests/Project_java_QUENUM_AROGOUN.java
```

Lancer la classe `Project_java_QUENUM_AROGOUN` → `Run As` → `Java Application`.

L'application démarre sur l'écran de connexion. Entrez votre adresse e-mail et votre mot de passe : **l'interface s'adapte automatiquement** selon que vous êtes étudiant ou administrateur.

> Il est aussi possible de lancer directement un écran en décommentant les lignes correspondantes dans le `main` (utile pour les tests) :
> ```java
> // new AccueilEtudiant(new Utilisateur(4, "Ango", "Joel", "joel@esigelec.fr", "joel", false));
> // new AccueilAdmin(new Utilisateur(2, "Arogoun", "Iyabo", "iyabo@esigelec.fr", "iyabo", true));
> ```

---

## Structure du projet

```
project_java_QUENUM_AROGOUN/
├── src/
│   ├── dao/              # Accès aux données (base Oracle)
│   │   ├── ConnectionDAO.java
│   │   ├── CampagneDAO.java
│   │   ├── CampagneDominanteDAO.java
│   │   ├── DominanteDAO.java
│   │   ├── InscriptionDAO.java
│   │   ├── SessionDAO.java
│   │   └── UtilisateurDAO.java
│   ├── ihm/              # Interfaces graphiques (Swing)
│   │   ├── ConnexionUtilisateur.java
│   │   ├── AccueilAdmin.java
│   │   ├── AccueilEtudiant.java
│   │   ├── ArchivesSessionsAdmin.java
│   │   ├── DetailsCampagnes.java
│   │   ├── DetailsCycleDeVie.java
│   │   ├── DetailsDashboard.java
│   │   ├── DetailsDominantes.java
│   │   ├── DetailsSessionsAdmin.java
│   │   ├── DetailSessionEtudiant.java
│   │   ├── EcranDeChargement.java
│   │   └── InscriptionsEtudiants.java
│   ├── model/            # Modèles métier (POJOs)
│   │   ├── Campagne.java
│   │   ├── CampagneDominante.java
│   │   ├── Dominante.java
│   │   ├── Inscription.java
│   │   ├── Session.java
│   │   └── Utilisateur.java
│   ├── resources/        # Ressources graphiques
│   │   └── logo_esigelec_boite-1.png
│   └── tests/
│       └── Project_java_QUENUM_AROGOUN.java   ← point d'entrée (main)
├── lib/
│   └── ojdbc7_g.jar      # Pilote JDBC Oracle (obligatoire)
├── bin/                  # Fichiers compilés (.class)
└── doc/                  # Javadoc générée
```

---

## Fonctionnalités

### Interface Étudiant

- Connexion sécurisée par e-mail et mot de passe
- Consultation des sessions disponibles (filtrées par dominante et par horaire)
- Les sessions archivées sont masquées automatiquement
- Accès au détail d'une session (salle, présentateur, date, heure, capacité)
- Inscription et désinscription à une session
- Consultation de ses inscriptions personnelles

### Interface Administrateur

- Accès au tableau de bord (statistiques générales)
- Gestion des **dominantes** : consultation, ajout, modification
- Gestion des **sessions** : consultation, ajout, modification, validation
- Gestion des **campagnes** : création et suivi des campagnes d'inscription
- **Cycle de vie des inscriptions** :
  - Ouvrir les inscriptions (toutes les sessions non archivées passent à "ouverte")
  - Fermer les inscriptions
  - Traitement automatique
  - Archivage manuel de sessions sélectionnées
- Consultation des **archives** de sessions

---

## Cycle de vie d'une session

| Statut | Description |
|--------|-------------|
| `ouverte` | Les étudiants peuvent s'inscrire et se désinscrire |
| `fermée` | Les étudiants ne peuvent plus voir la session dans l'accueil, mais la voient encore dans leurs inscriptions (sans pouvoir se désinscrire) |
| `archivée` | Session validée — les étudiants ne peuvent plus interagir avec elle |

**Règles métier importantes :**
- Une session **fermée** n'est **pas** archivée ni validée.
- Quand la **capacité maximale** d'une session est atteinte, elle est **automatiquement archivée** (traitement automatique).
- Quand un **administrateur valide** une session, celle-ci est également archivée.
- Les campagnes se **ferment automatiquement** à l'expiration de leur date de fin.

---

## Javadoc

La documentation technique générée est disponible dans le dossier `doc/`. Ouvrir `doc/index.html` dans un navigateur pour la consulter.

---

## Auteurs

| Nom | Rôle |
|-----|------|
| Ancelmo QUENUM | Développeur |
| Iyabo AROGOUN | Développeuse |

---

## Remarques

- L'application utilise **Oracle JDBC** et nécessite un accès réseau à la base de données de l'ESIGELEC.
- En cas de problème de connexion malgré des identifiants corrects, vérifier que le fichier `ojdbc7_g.jar` est bien ajouté au classpath du projet.
- L'architecture suit le patron **DAO (Data Access Object)** pour séparer la logique métier de l'accès aux données.
