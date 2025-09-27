**📊 Schéma ERD – bolo.com**

**1. Entités principales**

***👤 Candidat***
id_candidat (PK)
nom
prénom
email (unique)
mot_de_passe (hashé)
cv_url
date_inscription

***🏢 Entreprise***
id_entreprise (PK)
nom
email_contact
site_web
date_creation_compte

***👥 UtilisateurEntreprise***
id_utilisateur (PK)
id_entreprise (FK)
nom
prénom
email (unique)
rôle (administrateur, recruteur)
mot_de_passe

***💼 OffreEmploi***
id_offre (PK)
id_entreprise (FK)
titre
description
localisation
type_contrat (CDI, CDD, Freelance, Stage…)
date_publication
statut (active, expirée, archivée)

***📑 Candidature***
id_candidature (PK)
id_candidat (FK)
id_offre (FK)
date_candidature
statut (en cours, accepté, rejeté, entretien…)

***🔔 AlerteEmploi***
id_alerte (PK)
id_candidat (FK)
mots_cles
localisation
type_contrat
frequence (quotidienne, hebdomadaire)
canal (email, push)

***📝 TestCompetence***
id_test (PK)
type_test (langue, technique, logique…)
titre
description
niveau

***📊 ResultatTest***
id_resultat (PK)
id_candidat (FK)
id_test (FK)
score
date_passage

2. Relations

- Une Entreprise possède plusieurs UtilisateursEntreprise.
- Une Entreprise publie plusieurs OffresEmploi.
- Un Candidat postule à plusieurs OffresEmploi via Candidature.
- Un Candidat peut configurer plusieurs AlertesEmploi.
- Un Candidat passe plusieurs TestsCompetence, stockés dans ResultatTest.
- Un TestCompetence peut être passé par plusieurs candidats.
- Un candidat peut repasser un test de competence
