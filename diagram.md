# Diagramme de Classes - Système de Gestion des Absences ISM

```mermaid
classDiagram
    %% Classe abstraite Utilisateur
    class Utilisateur {
        <<abstract>>
        -String id
        -String email
        -String password
        -String nom
        -String prenom
        -String telephone
        -TypeUtilisateur typeUtilisateur
        -DateTime dateCreation
        -Boolean actif
        +seConnecter() void
        +seDeconnecter() void
        +modifierProfil() void
    }
    
    %% Classes concrètes héritant d'Utilisateur
    class Etudiant {
        -String matricule
        -Date dateNaissance
        -String adresse
        -String photo
        -String qrCode
        +consulterAbsences() List~Presence~
        +consulterRetards() List~Presence~
        +justifierAbsence(justification: Justification) void
        +consulterEmploiDuTemps() List~Seance~
        +genererQRCode() String
        +getStatistiques() StatistiqueAbsence
    }
    
    class Vigile {
        -String numeroAgent
        -String poste
        +pointerEtudiant(etudiant: Etudiant, seance: Seance) Presence
        +scannerQRCode(qrCode: String) Etudiant
        +saisirMatricule(matricule: String) Etudiant
        +consulterPresencesJour(date: Date) List~Presence~
        +getSeanceActuelle() Seance
    }
    
    class Admin {
        -String role
        +validerJustification(justification: Justification) void
        +rejeterJustification(justification: Justification, motif: String) void
        +genererRapport(type: String, periode: Periode) Rapport
        +gererInscriptions() void
        +gererPlanning() void
        +consulterStatistiquesGlobales() Statistiques
        +exporterDonnees(format: String) File
    }
    
    %% Classe Classe (pour les groupes d'étudiants)
    class Classe {
        -String id
        -String nom
        -String niveau
        -String filiere
        -String anneeAcademique
        -Integer effectif
        +ajouterEtudiant(etudiant: Etudiant) void
        +retirerEtudiant(etudiant: Etudiant) void
        +obtenirListeEtudiants() List~Etudiant~
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
        +getEtudiantsAbsents() List~Etudiant~
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
    
    %% Relations d'héritage
    Utilisateur <|-- Etudiant : hérite
    Utilisateur <|-- Vigile : hérite
    Utilisateur <|-- Admin : hérite
    
    %% Relations d'association et de composition
    Etudiant "1" --o "*" Presence : participe à
    Etudiant "*" --* "1" Classe : appartient à
    Etudiant "1" --o "*" Justification : soumet
    
    Vigile "1" --o "*" Presence : enregistre
    
    Admin "1" --o "*" Justification : traite
    Admin "1" --o "*" Rapport : génère
    
    Classe "*" <--> "*" Cours : suit
    Classe "1" --o "*" Seance : assiste à
    
    Enseignant "1" --o "*" Cours : enseigne
    Enseignant "1" --o "*" Seance : anime
    
    Cours "1" *-- "*" Seance : comprend
    
    Seance "1" *-- "*" Presence : contient
    
    Presence "1" -- "0..1" Justification : peut avoir
    
    %% Relations avec les énumérations
    Utilisateur ..> TypeUtilisateur : utilise
    Presence ..> StatutPresence : utilise
    Justification ..> StatutJustification : utilise
    Justification ..> TypeJustification : utilise
    Seance ..> TypeSeance : utilise
    
    %% Dépendances
    Etudiant ..> StatistiqueAbsence : crée
    Admin ..> Classe : gère
    Admin ..> Enseignant : gère
    Admin ..> Cours : gère
    Admin ..> Seance : gère
    
    %% Notes et contraintes
    note for Etudiant "- Le matricule est unique\n- Le QR code est généré automatiquement\n- Un étudiant appartient à une seule classe"
    
    note for Presence "- Une présence est unique par étudiant et séance\n- Le statut est calculé automatiquement\n- PRESENT si pointage <= heureDebut + 15min\n- RETARD si pointage > heureDebut + 15min"
    
    note for Justification "- Une justification par absence\n- Document obligatoire pour type MEDICAL\n- Validation par admin uniquement"
```

## Légende des Relations

- **`<|--`** : Héritage (extends)
- **`*--`** : Composition (l'objet possédé ne peut exister sans le possesseur)
- **`--o`** : Agrégation (l'objet possédé peut exister indépendamment)
- **`--`** : Association simple
- **`..>`** : Dépendance (uses)
- **`<-->`** : Association bidirectionnelle

## Cardinalités

- **`1`** : Un et un seul
- **`*`** : Zéro ou plusieurs
- **`0..1`** : Zéro ou un
- **`1..*`** : Un ou plusieurs

## Contraintes Métier Respectées

1. **Authentification** : Classe abstraite Utilisateur avec 3 types concrets
2. **Unicité** : Matricule étudiant et numéro agent vigile uniques
3. **Pointage** : Un seul pointage par étudiant et par séance
4. **Statut automatique** : PRESENT/RETARD calculé selon l'heure
5. **Justification** : Une seule par absence, validation par admin
6. **Hiérarchie** : Étudiant → Classe → Cours → Séances
7. **Traçabilité** : Dates de création et modification sur toutes les entités
