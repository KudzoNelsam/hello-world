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
