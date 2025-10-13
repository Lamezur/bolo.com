...

### Modèle de Données Complet pour bolo.com

#### 1️⃣ Utilisateur
| Champ              | Type                | Description                              |
|--------------------|---------------------|------------------------------------------|
| id_utilisateur     | PK (Auto-increment) | Identifiant unique                       |
| nom                | VARCHAR            | Nom de l’utilisateur                     |
| prenom             | VARCHAR            | Prénom de l’utilisateur                  |
| email              | VARCHAR (unique)   | Email de l’utilisateur                   |
| mot_de_passe       | VARCHAR            | Mot de passe hashé (via Strapi)          |
| type_utilisateur   | ENUM(candidat, gestionnaire, administrateur) | Type d’utilisateur            |
| langue_preferee    | ENUM(fr, en)       | Langue préférée de l’utilisateur         |
| date_creation      | DATETIME           | Date de création du compte               |

**Notes pour Strapi** :
- Utilisation du plugin **Users & Permissions** pour gérer cette table (champs natifs : `email`, `password`).
- Ajout `nom`, `prenom`, `type_utilisateur` (Enumeration), `langue_preferee` (Enumeration), `date_creation` (Date, auto-filled), etc. comme champs personnalisés.
- Le mot de passe est automatiquement hashé par Strapi (bcrypt).

---

#### 2️⃣ Candidat
| Champ              | Type                | Description                              |
|--------------------|---------------------|------------------------------------------|
| id_candidat        | PK, FK → Utilisateur(id_utilisateur) | Identifiant (lié à Utilisateur) |
| cv                 | VARCHAR (Media)     | Lien vers le fichier CV (PDF)            |
| lettre_motivation  | VARCHAR (Media, nullable) | Lien vers lettre de motivation (optionnel) |
| profil_complet     | BOOLEAN            | Indique si le profil est complété        |

**Notes pour Strapi** :
- Création d'une collection **Candidat** avec relation 1:1 vers **Utilisateur**.
- Champs `cv` et `lettre_motivation` utilisent le type Media (plugin Upload, limitez à PDF, 5MB max).
- `profil_complet` est un champ Boolean (défaut : false).

---

#### 3️⃣ Entreprise
| Champ              | Type                | Description                              |
|--------------------|---------------------|------------------------------------------|
| id_entreprise      | PK (Auto-increment) | Identifiant unique                       |
| nom_entreprise     | VARCHAR            | Nom de l’entreprise                      |
| secteur            | VARCHAR            | Secteur d’activité (ex. : Tech, Finance) |
| site_web           | VARCHAR (nullable) | Site web de l’entreprise                 |
| abonnement         | ENUM(Starter, Pro, Premium) | Type d’abonnement                |
| date_creation      | DATETIME           | Date de création du compte               |

**Notes pour Strapi** :
- Création d'une collection **Entreprise**.
- `abonnement` est un champ Enumeration.
- `site_web` peut être marqué comme nullable (optionnel).
- `date_creation` est un champ Date (auto-filled).

---

#### 4️⃣ Gestionnaire
| Champ              | Type                | Description                              |
|--------------------|---------------------|------------------------------------------|
| id_gestionnaire    | PK, FK → Utilisateur(id_utilisateur) | Identifiant (lié à Utilisateur) |
| id_entreprise      | FK → Entreprise(id_entreprise) | Entreprise associée                |
| role               | ENUM(éditeur_offres, recruteur) | Rôle du gestionnaire                |

**Notes pour Strapi** :
- Création d'une collection **Gestionnaire** avec relation 1:1 vers **Utilisateur** et 1:N vers **Entreprise**.
- `role` est un champ Enumeration.

---

#### 5️⃣ Offre d’emploi
| Champ              | Type                | Description                              |
|--------------------|---------------------|------------------------------------------|
| id_offre           | PK (Auto-increment) | Identifiant unique                       |
| titre              | VARCHAR            | Titre de l’offre (lié à Traduction)      |
| description        | TEXT               | Description détaillée (lié à Traduction) |
| localisation       | VARCHAR            | Lieu de travail                          |
| type_contrat       | ENUM(CDI, CDD, Stage, Freelance) | Type de contrat                   |
| date_publication   | DATETIME           | Date de publication                      |
| date_expiration    | DATETIME           | Date d’expiration                        |
| id_entreprise      | FK → Entreprise(id_entreprise) | Entreprise qui publie            |
| id_gestionnaire    | FK → Gestionnaire(id_gestionnaire) | Gestionnaire qui publie       |
| statut             | ENUM(active, archivee, expiree) | Statut de l’offre                  |

**Notes pour Strapi** :
- Création d'une collection **Offre** avec relations vers **Entreprise** (1:N) et **Gestionnaire** (1:N).
- Activation du plugin **i18n** pour `titre` et `description` (ou lien à table Traduction).
- `type_contrat` et `statut` sont des champs Enumeration.
- `date_publication` et `date_expiration` sont des champs Date.

---

#### 6️⃣ Candidature
| Champ              | Type                | Description                              |
|--------------------|---------------------|------------------------------------------|
| id_candidature     | PK (Auto-increment) | Identifiant unique                       |
| id_offre           | FK → Offre(id_offre) | Offre à laquelle le candidat postule   |
| id_candidat        | FK → Candidat(id_candidat) | Candidat ayant postulé              |
| date_postulation   | DATETIME           | Date de postulation                      |
| etat               | ENUM(Postule, CV_vu, En_process, Retenue, Rejetee) | État de la candidature |
| notes_recruteur    | TEXT (nullable)    | Notes internes du recruteur              |
| score_recommandation | FLOAT (nullable)  | Score IA pour tri automatique            |

**Notes pour Strapi** :
- Création d'une collection **Candidature** avec relations N:N entre **Offre** et **Candidat**.
- `etat` est un champ Enumeration.
- `notes_recruteur` et `score_recommandation` sont optionnels (Text et Float, nullable).

---

#### 7️⃣ IA_Assistance
| Champ              | Type                | Description                              |
|--------------------|---------------------|------------------------------------------|
| id_assistance      | PK (Auto-increment) | Identifiant unique                       |
| id_candidature     | FK → Candidature(id_candidature) | Candidature liée                |
| type               | ENUM(lettre_motivation, tri_candidatures) | Type d’assistance IA       |
| contenu            | TEXT               | Contenu généré par l’IA                  |
| date_creation      | DATETIME           | Date de génération                       |

**Notes pour Strapi** :
- Création d'une collection **IA_Assistance** avec relation 1:N vers **Candidature**.
- `type` est un champ Enumeration.
- `contenu` est un champ Text.

---

#### 8️⃣ Abonnement
| Champ              | Type                | Description                              |
|--------------------|---------------------|------------------------------------------|
| id_abonnement      | PK (Auto-increment) | Identifiant unique                       |
| id_entreprise      | FK → Entreprise(id_entreprise) | Entreprise souscrivant            |
| type               | ENUM(Starter, Pro, Premium) | Type d’abonnement                    |
| date_debut         | DATETIME           | Date de début de l’abonnement            |
| date_fin           | DATETIME           | Date de fin de l’abonnement              |
| statut             | ENUM(actif, expire) | Statut de l’abonnement                   |

**Notes pour Strapi** :
- Création d'une collection **Abonnement** avec relation 1:N vers **Entreprise**.
- `type` et `statut` sont des champs Enumeration.
- `date_debut` et `date_fin` sont des champs Date.

---

#### 9️⃣ Alertes
| Champ              | Type                | Description                              |
|--------------------|---------------------|------------------------------------------|
| id_alerte          | PK (Auto-increment) | Identifiant unique                       |
| id_candidat        | FK → Candidat(id_candidat) | Candidat recevant l’alerte       |
| mots_cles          | VARCHAR            | Mots-clés pour filtrer offres            |
| localisation       | VARCHAR (nullable) | Localisation souhaitée (optionnel)       |
| type_contrat       | ENUM(CDI, CDD, Stage, Freelance) | Type de contrat souhaité           |
| frequence          | ENUM(quotidien, hebdo) | Fréquence des notifications         |
| statut             | ENUM(actif, inactif) | Statut de l’alerte                   |
| date_creation      | DATETIME           | Date de création                         |

**Notes pour Strapi** :
- Création d'une collection **Alertes** avec relation 1:N vers **Candidat**.
- `type_contrat`, `frequence`, `statut` sont des champs Enumeration.
- `localisation` est nullable.

---

#### 10️⃣ Traduction (pour bilinguisme)
| Champ              | Type                | Description                              |
|--------------------|---------------------|------------------------------------------|
| id_traduction      | PK (Auto-increment) | Identifiant unique                       |
| content_type       | VARCHAR            | Type de contenu (ex. : "offre")          |
| field              | VARCHAR            | Champ traduit (ex. : "titre", "description") |
| id_content         | BIGINT             | ID du contenu lié (ex. : id_offre)       |
| locale             | ENUM(fr, en)       | Langue (français, anglais)               |
| valeur             | TEXT               | Contenu traduit                          |

**Notes pour Strapi** :
- Création d'une collection **Traduction** (ou utilisez plugin i18n si champs traduits limités à `titre`, `description`).
- `content_type` et `field` sont des champs Text pour identifier le contenu (ex. : `content_type: "offre", field: "titre"`).
- `locale` est un champ Enumeration.

---

### Relations Importantes
- **Utilisateur → Candidat ou Gestionnaire** : 1:1 (un Utilisateur est soit Candidat, soit Gestionnaire, soit Administrateur via `type_utilisateur`).
- **Entreprise → Gestionnaire** : 1:N (une Entreprise peut avoir plusieurs Gestionnaires).
- **Gestionnaire → Offre** : 1:N (un Gestionnaire peut publier plusieurs Offres).
- **Offre → Candidature → Candidat** : N:N via table Candidature (une Offre peut avoir plusieurs Candidatures, un Candidat peut postuler à plusieurs Offres).
- **Candidature → IA_Assistance** : 0:N (une Candidature peut avoir plusieurs assistances IA, mais ce n’est pas obligatoire).
- **Entreprise → Abonnement** : 1:N (une Entreprise peut avoir un historique d’Abonnements).
- **Candidat → Alertes** : 1:N (un Candidat peut configurer plusieurs Alertes).

---

### Optimisations et Notes
- **Bilinguisme** :
  - La table **Traduction** permet de gérer les champs textuels (ex. : `titre`, `description`) sans dupliquer les champs dans chaque table. Alternativement, utilisez le plugin i18n de Strapi pour `Offre` (`titre`, `description`) si vous préférez une solution native.
  - Exemple pour **Traduction** : Pour une offre avec `id_offre=1`, stockez :
    ```markdown:disable-run
    | id_traduction | content_type | field       | id_content | locale | valeur                |
    |---------------|--------------|-------------|------------|--------|-----------------------|
    | 1             | offre        | titre       | 1          | fr     | Développeur Full Stack |
    | 2             | offre        | titre       | 1          | en     | Full Stack Developer  |
    ```
- **IA** :
  - Le champ `score_recommandation` dans **Candidature** permet un tri automatique (ex. : basé sur correspondance mots-clés CV/offre). Implémentation d'une logique simple dans Strapi (via script personnalisé) pour MVP, reportez IA avancée à v2.
- **Alertes** :
  - Implémentation d'un cron job (via Strapi extensions) pour vérifier offres correspondant à `mots_cles`, `localisation`, `type_contrat` des **Alertes**. Envoi des emails via SendGrid/Nodemailer.
- **Strapi Implémentation** :
  - Création de chaque table comme une **Collection Type** dans Strapi (sauf **Utilisateur**, géré par Users & Permissions).
  - Configuration permissions (Admin > Roles) pour limiter accès (ex. : `notes_recruteur` visible uniquement par Gestionnaires).
  - Utilisation du plugin Upload pour CV/lettre (intégration Cloudinary pour stockage cloud).
- **Sécurité** :
  - Assurer conformité RGPD : stockez consentement utilisateur, permettez suppression données.
  - Limite uploads à 5MB, format PDF.
  - Sécurisation endpoints avec JWT (Strapi gère automatiquement).

---

### Prochaines Étapes
Modèle prêt à être implémenté dans Strapi. Pour avancer :
1. **Création des Content Types** dans Strapi (En suivant les notes ci-dessus).
2. **Configuration i18n** (plugin ou table Traduction) pour bilinguisme.
3. **Adaption du template React** pour appeler les endpoints Strapi (ex. : `GET /api/offres?locale=fr`).
4. **Tests localement** chaque table/relation (ex. : créer utilisateur → candidat → postuler offre).

...
