# Diagramme de Classes - Système de Gestion des Absences ISM

```mermaid
classDiagram
    %% Classe User unique avec rôle
    class User {
        -String id
        -String email
        -String password
        -String nom
        -String prenom
        -String telephone
        -TypeUtilisateur role
        -DateTime dateCreation
        -Boolean actif
        -String matricule [nullable]
        -Date dateNaissance [nullable]
        -String adresse [nullable]
        -String photo [nullable]
        -String qrCode [nullable]
        -String numeroAgent [nullable]
        -String poste [nullable]
        -String roleAdmin [nullable]
        +seConnecter() void
        +seDeconnecter() void
        +modifierProfil() void
        +consulterAbsences() List~Presence~
        +consulterRetards() List~Presence~
        +justifierAbsence(justification: Justification) void
        +consulterEmploiDuTemps() List~Seance~
        +genererQRCode() String
        +pointerEtudiant(etudiant: User, seance: Seance) Presence
        +scannerQRCode(qrCode: String) User
        +consulterPresencesJour(date: Date) List~Presence~
        +validerJustification(justification: Justification) void
        +rejeterJustification(justification: Justification, motif: String) void
        +genererRapport(type: String, periode: Periode) Rapport
    }
    
    %% Classe Classe (pour les groupes d'étudiants)
    class Classe {
        -String id
        -String nom
        -String niveau
        -String filiere
        -String anneeAcademique
        -Integer effectif
        +ajouterEtudiant(etudiant: User) void
        +retirerEtudiant(etudiant: User) void
        +obtenirListeEtudiants() List~User~
        +calculerTauxPresenceGlobal() Double
    }
    
    %% Classe Enseignant
    class Enseignant {
        -String id
        -String nom
        -String prenom
        -String email
        -String telephone
        -String specialite
        -String grade
        +obtenirCours() List~Cours~
        +obtenirSeances() List~Seance~
        +consulterPresencesCours(cours: Cours) List~Presence~
    }
    
    %% Classe Cours
    class Cours {
        -String id
        -String code
        -String nom
        -Integer credit
        -Integer volumeHoraire
        -String description
        +ajouterSeance(seance: Seance) void
        +obtenirSeances() List~Seance~
        +calculerTauxPresenceMoyen() Double
    }
    
    %% Classe Seance
    class Seance {
        -String id
        -Date date
        -Time heureDebut
        -Time heureFin
        -String salle
        -TypeSeance type
        +marquerPresence(presence: Presence) void
        +obtenirPresences() List~Presence~
        +calculerTauxPresence() Double
        +estTerminee() Boolean
        +getEtudiantsAbsents() List~User~
    }
    
    %% Classe Presence
    class Presence {
        -String id
        -DateTime heurePointage
        -StatutPresence statut
        -String commentaire
        -DateTime dateCreation
        +estEnRetard() Boolean
        +calculerDureeRetard() Integer
        +marquerJustifiee() void
    }
    
    %% Classe Justification
    class Justification {
        -String id
        -String motif
        -TypeJustification typeJustification
        -DateTime dateDepot
        -DateTime dateTraitement
        -StatutJustification statut
        -String documentJustificatif
        -String commentaireAdmin
        +valider() void
        +rejeter(motif: String) void
        +telechargerDocument() File
        +estEnAttente() Boolean
    }
    
    %% Classe Rapport
    class Rapport {
        -String id
        -String type
        -DateTime dateGeneration
        -String periode
        -String contenu
        -String format
        +generer() void
        +exporter(format: String) File
        +envoyerParEmail(destinataire: String) void
    }
    
    %% Classes pour les statistiques
    class StatistiqueAbsence {
        -Integer totalAbsences
        -Integer totalRetards
        -Integer totalPresences
        -Integer totalJustifiees
        -Double tauxPresence
        +calculerTauxPresence() Double
        +genererGraphique() Chart
    }
    
    %% Énumérations
    class TypeUtilisateur {
        <<enumeration>>
        ETUDIANT
        VIGILE
        ADMIN
    }
    
    class StatutPresence {
        <<enumeration>>
        PRESENT
        ABSENT
        RETARD
    }
    
    class StatutJustification {
        <<enumeration>>
        EN_ATTENTE
        VALIDEE
        REJETEE
    }
    
    class TypeJustification {
        <<enumeration>>
        MEDICAL
        FAMILIAL
        AUTRE
    }
    
    class TypeSeance {
        <<enumeration>>
        COURS
        TD
        TP
        EXAMEN
    }
    
    %% Relations d'association et de composition
    User "1" --o "*" Presence : participe/enregistre
    User "*" --* "1" Classe : appartient à [si role=ETUDIANT]
    User "1" --o "*" Justification : soumet/traite
    User "1" --o "*" Rapport : génère [si role=ADMIN]
    
    Classe "*" <--> "*" Cours : suit
    Classe "1" --o "*" Seance : assiste à
    
    Enseignant "1" --o "*" Cours : enseigne
    Enseignant "1" --o "*" Seance : anime
    
    Cours "1" *-- "*" Seance : comprend
    
    Seance "1" *-- "*" Presence : contient
    
    Presence "1" -- "0..1" Justification : peut avoir
    
    %% Relations avec les énumérations
    User ..> TypeUtilisateur : utilise
    Presence ..> StatutPresence : utilise
    Justification ..> StatutJustification : utilise
    Justification ..> TypeJustification : utilise
    Seance ..> TypeSeance : utilise
    
    %% Dépendances
    User ..> StatistiqueAbsence : crée [si role=ETUDIANT]
    User ..> Classe : gère [si role=ADMIN]
    User ..> Enseignant : gère [si role=ADMIN]
    User ..> Cours : gère [si role=ADMIN]
    User ..> Seance : gère [si role=ADMIN]
    
    %% Notes et contraintes
    note for User "Champs spécifiques par rôle:\n- ETUDIANT: matricule, dateNaissance, adresse, photo, qrCode\n- VIGILE: numeroAgent, poste\n- ADMIN: roleAdmin\n\nTous les champs spécifiques sont nullable"
    
    note for Presence "- Une présence est unique par user(étudiant) et séance\n- Le statut est calculé automatiquement\n- PRESENT si pointage <= heureDebut + 15min\n- RETARD si pointage > heureDebut + 15min\n- Créée par un User avec role=VIGILE"
    
    note for Justification "- Une justification par absence\n- Document obligatoire pour type MEDICAL\n- Soumise par User avec role=ETUDIANT\n- Validée par User avec role=ADMIN"
```

## Changements Effectués

### 1. **Entité User Unique**
- Plus d'héritage
- Un seul type `User` avec un attribut `role` (TypeUtilisateur)
- Tous les champs spécifiques sont **nullable** :
  - **Pour ETUDIANT** : matricule, dateNaissance, adresse, photo, qrCode
  - **Pour VIGILE** : numeroAgent, poste  
  - **Pour ADMIN** : roleAdmin

### 2. **Gestion par Rôle**
- Les méthodes sont disponibles sur User mais leur utilisation dépend du rôle
- Les relations conditionnelles sont annotées (ex: "si role=ETUDIANT")

### 3. **Avantages de cette Approche**
- Plus simple pour la base de données (une seule collection/table)
- Facilite la gestion des permissions
- Évite la complexité de l'héritage en MongoDB
- Permet facilement de changer de rôle si nécessaire

### 4. **Contraintes Maintenues**
- Unicité du matricule (pour les étudiants)
- Unicité du numeroAgent (pour les vigiles)
- Un étudiant appartient à une seule classe
- Les autres contraintes métier restent identiques
