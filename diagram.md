## 5. Diagramme d'Activités - Processus Global de Gestion des Absences

```mermaid
flowchart TD
    Start([Début de séance]) --> Fork1{" "}
    
    Fork1 --> VLogin[Vigile se connecte]
    Fork1 --> EArrive[Étudiants arrivent]
    
    VLogin --> VAccess[Vigile accède à la séance]
    VAccess --> Fork1End{" "}
    EArrive --> Fork1End
    
    Fork1End --> StartPointing[Vigile commence le pointage]
    
    StartPointing --> CheckMore{Étudiants à pointer?}
    
    CheckMore -->|Oui| MethodChoice{Méthode de pointage?}
    CheckMore -->|Non| EndPointing[Fin du pointage]
    
    MethodChoice -->|QR Code| ScanQR[Scanner QR Code]
    MethodChoice -->|Matricule| EnterMat[Saisir matricule]
    
    ScanQR --> VerifyStudent[Vérifier identité étudiant]
    EnterMat --> VerifyStudent
    
    VerifyStudent --> CalcTime[Calculer heure d'arrivée]
    
    CalcTime --> CheckTime{Heure ≤ Début + 15min?}
    
    CheckTime -->|Oui| MarkPresent[Marquer PRESENT]
    CheckTime -->|Non| MarkLate[Marquer RETARD]
    
    MarkPresent --> SavePresence[Enregistrer présence]
    MarkLate --> SavePresence
    
    SavePresence --> CheckMore
    
    EndPointing --> Fork2{" "}
    
    Fork2 --> GenAbsent[Génération liste absents]
    Fork2 --> UpdateStats[Mise à jour statistiques]
    
    GenAbsent --> NotifyAbsent[Notification aux absents]
    UpdateStats --> Fork2End{" "}
    NotifyAbsent --> Fork2End
    
    Fork2End --> StudentProcess[Processus Étudiant]
    
    subgraph "Processus Étudiant"
        StudentProcess --> ConsultAbs[Étudiant consulte absences]
        ConsultAbs --> HasJustify{Absence à justifier?}
        
        HasJustify -->|Non| EndStudent[Fin processus étudiant]
        HasJustify -->|Oui| FillJustif[Remplir justification]
        
        FillJustif --> UploadDoc[Télécharger document]
        UploadDoc --> SubmitJustif[Soumettre justification]
        
        subgraph "Processus Admin"
            SubmitJustif --> AdminNotif[Admin reçoit notification]
            AdminNotif --> ExamineJustif[Admin examine justification]
            
            ExamineJustif --> ValidJustif{Justification valide?}
            
            ValidJustif -->|Oui| Validate[Valider justification]
            ValidJustif -->|Non| Reject[Rejeter justification]
            
            Validate --> UpdateStatus[Mettre à jour statut absence]
            Reject --> AddComment[Ajouter commentaire]
            
            UpdateStatus --> NotifyStudent[Notifier étudiant]
            AddComment --> NotifyStudent
        end
        
        NotifyStudent --> EndStudent
    end
    
    EndStudent --> EndProcess([Fin du processus])
    
    %% Styles pour améliorer la lisibilité
    classDef startEnd fill:#e1f5fe,stroke:#01579b,stroke-width:3px
    classDef process fill:#fff3e0,stroke:#e65100,stroke-width:2px
    classDef decision fill:#f3e5f5,stroke:#4a148c,stroke-width:2px
    classDef fork fill:#e8f5e9,stroke:#1b5e20,stroke-width:2px
    
    class Start,EndProcess startEnd
    class VLogin,VAccess,EArrive,StartPointing,ScanQR,EnterMat,VerifyStudent,CalcTime,MarkPresent,MarkLate,SavePresence,EndPointing,GenAbsent,NotifyAbsent,UpdateStats,StudentProcess,ConsultAbs,FillJustif,UploadDoc,SubmitJustif,AdminNotif,ExamineJustif,Validate,Reject,UpdateStatus,AddComment,NotifyStudent,EndStudent process
    class CheckMore,MethodChoice,CheckTime,HasJustify,ValidJustif decision
    class Fork1,Fork1End,Fork2,Fork2End fork
```# Modélisation UML - Système de Gestion des Absences ISM

## 1. Diagramme de Cas d'Utilisation (Use Case)

```mermaid
graph TB
    subgraph "Acteurs"
        Vigile[fa:fa-user-shield Vigile]
        Etudiant[fa:fa-graduation-cap Étudiant]
        Admin[fa:fa-user-cog Administrateur]
    end
    
    subgraph "Système de Gestion des Absences ISM"
        %% Cas d'utilisation communs
        Login[Se connecter]
        
        %% Cas d'utilisation Vigile
        ScanQR[Scanner QR Code étudiant]
        SaisirMat[Saisir matricule étudiant]
        Pointer[Pointer un étudiant]
        ConsulterPresJour[Consulter présences du jour]
        
        %% Cas d'utilisation Étudiant
        ConsulterAbs[Consulter ses absences]
        ConsulterRet[Consulter ses retards]
        Justifier[Justifier absence/retard]
        ConsulterEDT[Consulter emploi du temps]
        TelechargerJust[Télécharger justificatif]
        
        %% Cas d'utilisation Administrateur
        ConsulterToutesAbs[Consulter toutes absences]
        ConsulterTousRet[Consulter tous retards]
        ValiderJust[Valider justification]
        RejeterJust[Rejeter justification]
        GererInsc[Gérer inscriptions]
        GererClasses[Gérer classes]
        GererEns[Gérer enseignants]
        GererCours[Gérer cours]
        GererSeances[Gérer séances]
        GererPlanning[Gérer planning global]
        GenererRapports[Générer rapports]
    end
    
    %% Relations Vigile
    Vigile --> Login
    Vigile --> ScanQR
    Vigile --> SaisirMat
    Vigile --> Pointer
    Vigile --> ConsulterPresJour
    
    %% Relations Étudiant
    Etudiant --> Login
    Etudiant --> ConsulterAbs
    Etudiant --> ConsulterRet
    Etudiant --> Justifier
    Etudiant --> ConsulterEDT
    
    %% Relations Administrateur
    Admin --> Login
    Admin --> ConsulterToutesAbs
    Admin --> ConsulterTousRet
    Admin --> ValiderJust
    Admin --> RejeterJust
    Admin --> GererInsc
    Admin --> GererClasses
    Admin --> GererEns
    Admin --> GererCours
    Admin --> GererSeances
    Admin --> GererPlanning
    Admin --> GenererRapports
    
    %% Relations include/extend
    Pointer -.include.-> ScanQR
    Pointer -.include.-> SaisirMat
    Justifier -.extend.-> TelechargerJust
    
    %% Styles
    classDef actorStyle fill:#e1f5fe,stroke:#01579b,stroke-width:2px
    classDef usecaseStyle fill:#fff3e0,stroke:#e65100,stroke-width:2px
    
    class Vigile,Etudiant,Admin actorStyle
    class Login,ScanQR,SaisirMat,Pointer,ConsulterPresJour,ConsulterAbs,ConsulterRet,Justifier,ConsulterEDT,TelechargerJust,ConsulterToutesAbs,ConsulterTousRet,ValiderJust,RejeterJust,GererInsc,GererClasses,GererEns,GererCours,GererSeances,GererPlanning,GenererRapports usecaseStyle
```

## 2. Diagramme de Classes Détaillé

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
        -Date dateCreation
        -Boolean actif
        +seConnecter()
        +seDeconnecter()
        +modifierProfil()
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
        +justifierAbsence(Justification justification)
        +consulterEmploiDuTemps() List~Seance~
        +genererQRCode()
    }
    
    class Vigile {
        -String numeroAgent
        -String poste
        +pointerEtudiant(Etudiant etudiant, Seance seance)
        +scannerQRCode(String qrCode) Etudiant
        +saisirMatricule(String matricule) Etudiant
        +consulterPresencesJour() List~Presence~
    }
    
    class Admin {
        -String role
        +validerJustification(Justification justification)
        +rejeterJustification(Justification justification)
        +genererRapport(String type, Periode periode) Rapport
        +gererInscriptions()
        +gererPlanning()
    }
    
    %% Autres classes du domaine
    class Classe {
        -String id
        -String nom
        -String niveau
        -String filiere
        -String anneeAcademique
        -Integer effectif
        +ajouterEtudiant(Etudiant etudiant)
        +retirerEtudiant(Etudiant etudiant)
        +obtenirListeEtudiants() List~Etudiant~
    }
    
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
    }
    
    class Cours {
        -String id
        -String code
        -String nom
        -Integer credit
        -Integer volumeHoraire
        -String description
        +ajouterSeance(Seance seance)
        +obtenirSeances() List~Seance~
    }
    
    class Seance {
        -String id
        -Date date
        -Time heureDebut
        -Time heureFin
        -String salle
        -String type
        +marquerPresence(Presence presence)
        +obtenirPresences() List~Presence~
        +calculerTauxPresence() Double
    }
    
    class Presence {
        -String id
        -DateTime heurePointage
        -StatutPresence statut
        -String commentaire
        +estEnRetard() Boolean
        +calculerDureeRetard() Integer
    }
    
    class Justification {
        -String id
        -String motif
        -TypeJustification typeJustification
        -Date dateDepot
        -Date dateTraitement
        -StatutJustification statut
        -String documentJustificatif
        -String commentaireAdmin
        +valider()
        +rejeter()
        +telechargerDocument() File
    }
    
    class Rapport {
        -String id
        -String type
        -Date dateGeneration
        -String periode
        -String contenu
        +generer()
        +exporter(String format) File
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
    
    %% Relations d'héritage
    Utilisateur <|-- Etudiant
    Utilisateur <|-- Vigile
    Utilisateur <|-- Admin
    
    %% Relations d'association
    Etudiant "1" -- "*" Presence : participe
    Etudiant "*" -- "1" Classe : appartient
    Etudiant "1" -- "*" Justification : soumet
    
    Vigile "1" -- "*" Presence : enregistre
    
    Admin "1" -- "*" Justification : traite
    Admin "1" -- "*" Rapport : génère
    
    Classe "*" -- "*" Cours : suit
    Classe "1" -- "*" Seance : assiste
    
    Enseignant "1" -- "*" Cours : enseigne
    Enseignant "1" -- "*" Seance : anime
    
    Cours "1" -- "*" Seance : comprend
    
    Seance "1" -- "*" Presence : contient
    
    Presence "0..1" -- "0..1" Justification : justifie
```

## 3. Diagramme de Séquence - Processus de Pointage

```mermaid
sequenceDiagram
    participant V as Vigile
    participant AV as App Mobile<br/>Vigile
    participant API as API Gateway
    participant AS as Service<br/>Authentification
    participant PS as Service<br/>Presence
    participant ES as Service<br/>Etudiant
    participant DB as MongoDB

    Note over V,DB: Connexion du Vigile
    V->>AV: Saisir identifiants
    AV->>API: POST /auth/login
    API->>AS: authenticate(email, password)
    AS->>DB: findUserByEmail()
    DB-->>AS: User data
    AS->>AS: validatePassword()
    AS->>AS: generateJWT()
    AS-->>API: JWT token
    API-->>AV: { token, user }
    AV->>AV: Stocker token

    Note over V,DB: Scanner/Saisir Étudiant
    alt Scanner QR Code
        V->>AV: Scanner QR Code
        AV->>AV: Décoder QR Code
    else Saisir Matricule
        V->>AV: Saisir matricule
    end

    AV->>API: GET /etudiants/{matricule}
    API->>ES: findByMatricule()
    ES->>DB: query étudiant
    DB-->>ES: Etudiant data
    ES-->>API: Etudiant object
    API-->>AV: Etudiant info

    Note over V,DB: Pointer Présence
    AV->>AV: Afficher info étudiant
    V->>AV: Confirmer pointage
    AV->>API: POST /presences
    Note right of API: { etudiantId, seanceId, heurePointage }
    API->>PS: createPresence()
    PS->>PS: Calculer statut<br/>(PRESENT/RETARD)
    PS->>DB: save presence
    DB-->>PS: Presence saved
    PS-->>API: Presence created
    API-->>AV: Confirmation
    AV->>AV: Afficher succès
```

## 4. Diagramme de Séquence - Processus de Justification

```mermaid
sequenceDiagram
    participant E as Étudiant
    participant AE as App Mobile<br/>Étudiant
    participant API as API Gateway
    participant JS as Service<br/>Justification
    participant NS as Service<br/>Notification
    participant DB as MongoDB
    participant A as Admin
    participant AA as App Web<br/>Admin

    Note over E,AA: Soumission de Justification
    E->>AE: Sélectionner absence
    AE->>API: GET /absences/{id}
    API-->>AE: Détails absence

    E->>AE: Remplir formulaire<br/>justification
    E->>AE: Télécharger document
    AE->>API: POST /justifications
    Note right of API: { absenceId, motif, type, document }

    API->>JS: createJustification()
    JS->>JS: Valider données
    JS->>DB: save justification
    DB-->>JS: Justification saved
    JS->>NS: notifyAdmin()
    NS->>NS: Envoyer notification
    JS-->>API: Justification created
    API-->>AE: Confirmation

    Note over A,AA: Validation par Admin
    A->>AA: Consulter justifications
    AA->>API: GET /justifications/pending
    API->>JS: getPendingJustifications()
    JS->>DB: query justifications
    DB-->>JS: List<Justification>
    JS-->>API: Justifications list
    API-->>AA: Afficher liste

    A->>AA: Sélectionner justification
    AA->>API: GET /justifications/{id}
    API-->>AA: Détails + document

    A->>AA: Valider/Rejeter
    AA->>API: PUT /justifications/{id}/status
    Note right of API: { statut: VALIDEE/REJETEE, commentaire }

    API->>JS: updateStatus()
    JS->>DB: update justification
    JS->>NS: notifyStudent()
    NS->>NS: Envoyer notification
    JS-->>API: Status updated
    API-->>AA: Confirmation
```

## 5. Diagramme d'Activités - Processus Global de Gestion des Absences

```mermaid
@startuml
start

:Début de séance;

fork
    :Vigile se connecte;
    :Vigile accède à la séance;
fork again
    :Étudiants arrivent;
end fork

:Vigile commence le pointage;

while (Étudiants à pointer?) is (Oui)
    if (Méthode de pointage?) then (QR Code)
        :Scanner QR Code;
    else (Matricule)
        :Saisir matricule;
    endif
    
    :Vérifier identité étudiant;
    :Calculer heure d'arrivée;
    
    if (Heure <= Début + 15min?) then (Oui)
        :Marquer PRESENT;
    else (Non)
        :Marquer RETARD;
    endif
    
    :Enregistrer présence;
endwhile (Non)

:Fin du pointage;

fork
    :Génération liste absents;
    :Notification aux absents;
fork again
    :Mise à jour statistiques;
end fork

partition "Processus Étudiant" {
    :Étudiant consulte absences;
    
    if (Absence à justifier?) then (Oui)
        :Remplir justification;
        :Télécharger document;
        :Soumettre justification;
        
        partition "Processus Admin" {
            :Admin reçoit notification;
            :Admin examine justification;
            
            if (Justification valide?) then (Oui)
                :Valider justification;
                :Mettre à jour statut absence;
            else (Non)
                :Rejeter justification;
                :Ajouter commentaire;
            endif
            
            :Notifier étudiant;
        }
    endif
}

:Fin du processus;
stop

@enduml
```

## 6. Justification des Choix de Modélisation

### Architecture Globale
- **Pattern MVC** : Séparation claire entre les couches présentation (Angular/Flutter), logique métier (Spring Boot), et données (MongoDB)
- **Architecture microservices** : Services découplés pour l'authentification, la gestion des présences, et les justifications
- **API REST** : Communication standardisée entre frontend et backend

### Choix de Conception
1. **Classe abstraite Utilisateur** : Factorisation du code commun aux trois types d'utilisateurs
2. **Énumérations** : Garantir l'intégrité des données pour les statuts et types
3. **Relations bidirectionnelles** : Faciliter la navigation entre entités liées
4. **Service de notification** : Découplage de la logique de notification

### Sécurité
- **JWT** pour l'authentification stateless
- **Rôles et permissions** différenciés par type d'utilisateur
- **Validation côté serveur** de toutes les données

### Performance
- **MongoDB** : Base NoSQL pour flexibilité et scalabilité
- **Indexation** sur les champs fréquemment recherchés (matricule, date)
- **Pagination** pour les listes volumineuses
