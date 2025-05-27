# Modélisation UML - Système de Gestion des Absences ISM

## 1. Diagramme de Cas d'Utilisation (Use Case)

```mermaid
@startuml
!define RECTANGLE class

skinparam actor {
    BackgroundColor<< Vigile >> LightBlue
    BackgroundColor<< Etudiant >> LightGreen
    BackgroundColor<< Admin >> LightCoral
}

actor "Vigile" as vigile << Vigile >>
actor "Étudiant" as etudiant << Etudiant >>
actor "Administrateur" as admin << Admin >>

rectangle "Système de Gestion des Absences ISM" {
    ' Cas d'utilisation communs
    usecase "Se connecter" as UC_Login
    
    ' Cas d'utilisation Vigile
    usecase "Scanner QR Code étudiant" as UC_ScanQR
    usecase "Saisir matricule étudiant" as UC_SaisirMatricule
    usecase "Pointer un étudiant" as UC_Pointer
    usecase "Consulter présences du jour" as UC_ConsulterPresencesJour
    
    ' Cas d'utilisation Étudiant
    usecase "Consulter ses absences" as UC_ConsulterAbsences
    usecase "Consulter ses retards" as UC_ConsulterRetards
    usecase "Justifier absence/retard" as UC_Justifier
    usecase "Consulter emploi du temps" as UC_ConsulterEDT
    usecase "Télécharger justificatif" as UC_TelechargerJustif
    
    ' Cas d'utilisation Administrateur
    usecase "Consulter toutes absences" as UC_ConsulterToutesAbsences
    usecase "Consulter tous retards" as UC_ConsulterTousRetards
    usecase "Valider justification" as UC_ValiderJustif
    usecase "Rejeter justification" as UC_RejeterJustif
    usecase "Gérer inscriptions" as UC_GererInscriptions
    usecase "Gérer classes" as UC_GererClasses
    usecase "Gérer enseignants" as UC_GererEnseignants
    usecase "Gérer cours" as UC_GererCours
    usecase "Gérer séances" as UC_GererSeances
    usecase "Gérer planning global" as UC_GererPlanning
    usecase "Générer rapports" as UC_GenererRapports
}

' Relations Vigile
vigile --> UC_Login
vigile --> UC_ScanQR
vigile --> UC_SaisirMatricule
vigile --> UC_Pointer
vigile --> UC_ConsulterPresencesJour

' Relations Étudiant
etudiant --> UC_Login
etudiant --> UC_ConsulterAbsences
etudiant --> UC_ConsulterRetards
etudiant --> UC_Justifier
etudiant --> UC_ConsulterEDT

' Relations Administrateur
admin --> UC_Login
admin --> UC_ConsulterToutesAbsences
admin --> UC_ConsulterTousRetards
admin --> UC_ValiderJustif
admin --> UC_RejeterJustif
admin --> UC_GererInscriptions
admin --> UC_GererClasses
admin --> UC_GererEnseignants
admin --> UC_GererCours
admin --> UC_GererSeances
admin --> UC_GererPlanning
admin --> UC_GenererRapports

' Relations include/extend
UC_Pointer ..> UC_ScanQR : <<include>>
UC_Pointer ..> UC_SaisirMatricule : <<include>>
UC_Justifier ..> UC_TelechargerJustif : <<extend>>

@enduml
```

## 2. Diagramme de Classes Détaillé

```mermaid
@startuml
!define ENTITY class

' Énumérations
enum TypeUtilisateur {
    ETUDIANT
    VIGILE
    ADMIN
}

enum StatutPresence {
    PRESENT
    ABSENT
    RETARD
}

enum StatutJustification {
    EN_ATTENTE
    VALIDEE
    REJETEE
}

enum TypeJustification {
    MEDICAL
    FAMILIAL
    AUTRE
}

' Classe abstraite Utilisateur
abstract class Utilisateur {
    - id: String
    - email: String
    - password: String
    - nom: String
    - prenom: String
    - telephone: String
    - typeUtilisateur: TypeUtilisateur
    - dateCreation: Date
    - actif: Boolean
    + seConnecter()
    + seDeconnecter()
    + modifierProfil()
}

' Classes concrètes
class Etudiant extends Utilisateur {
    - matricule: String
    - dateNaissance: Date
    - adresse: String
    - photo: String
    - qrCode: String
    + consulterAbsences(): List<Presence>
    + consulterRetards(): List<Presence>
    + justifierAbsence(justification: Justification)
    + consulterEmploiDuTemps(): List<Seance>
    + genererQRCode()
}

class Vigile extends Utilisateur {
    - numeroAgent: String
    - poste: String
    + pointerEtudiant(etudiant: Etudiant, seance: Seance)
    + scannerQRCode(qrCode: String): Etudiant
    + saisirMatricule(matricule: String): Etudiant
    + consulterPresencesJour(): List<Presence>
}

class Admin extends Utilisateur {
    - role: String
    + validerJustification(justification: Justification)
    + rejeterJustification(justification: Justification)
    + genererRapport(type: String, periode: Periode): Rapport
    + gererInscriptions()
    + gererPlanning()
}

class Classe {
    - id: String
    - nom: String
    - niveau: String
    - filiere: String
    - anneeAcademique: String
    - effectif: Integer
    + ajouterEtudiant(etudiant: Etudiant)
    + retirerEtudiant(etudiant: Etudiant)
    + obtenirListeEtudiants(): List<Etudiant>
}

class Enseignant {
    - id: String
    - nom: String
    - prenom: String
    - email: String
    - telephone: String
    - specialite: String
    - grade: String
    + obtenirCours(): List<Cours>
    + obtenirSeances(): List<Seance>
}

class Cours {
    - id: String
    - code: String
    - nom: String
    - credit: Integer
    - volumeHoraire: Integer
    - description: String
    + ajouterSeance(seance: Seance)
    + obtenirSeances(): List<Seance>
}

class Seance {
    - id: String
    - date: Date
    - heureDebut: Time
    - heureFin: Time
    - salle: String
    - type: String
    + marquerPresence(presence: Presence)
    + obtenirPresences(): List<Presence>
    + calculerTauxPresence(): Double
}

class Presence {
    - id: String
    - heurePointage: DateTime
    - statut: StatutPresence
    - commentaire: String
    + estEnRetard(): Boolean
    + calculerDureeRetard(): Integer
}

class Justification {
    - id: String
    - motif: String
    - typeJustification: TypeJustification
    - dateDepot: Date
    - dateTraitement: Date
    - statut: StatutJustification
    - documentJustificatif: String
    - commentaireAdmin: String
    + valider()
    + rejeter()
    + telechargerDocument(): File
}

class Rapport {
    - id: String
    - type: String
    - dateGeneration: Date
    - periode: String
    - contenu: String
    + generer()
    + exporter(format: String): File
}

' Relations
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

@enduml
```

## 3. Diagramme de Séquence - Processus de Pointage

```mermaid
@startuml
actor Vigile
participant "App Mobile\nVigile" as AppVigile
participant "API Gateway" as API
participant "Service\nAuthentification" as AuthService
participant "Service\nPresence" as PresenceService
participant "Service\nEtudiant" as EtudiantService
database "MongoDB" as DB

== Connexion du Vigile ==
Vigile -> AppVigile: Saisir identifiants
AppVigile -> API: POST /auth/login
API -> AuthService: authenticate(email, password)
AuthService -> DB: findUserByEmail()
DB --> AuthService: User data
AuthService -> AuthService: validatePassword()
AuthService -> AuthService: generateJWT()
AuthService --> API: JWT token
API --> AppVigile: { token, user }
AppVigile -> AppVigile: Stocker token

== Scanner/Saisir Étudiant ==
alt Scanner QR Code
    Vigile -> AppVigile: Scanner QR Code
    AppVigile -> AppVigile: Décoder QR Code
else Saisir Matricule
    Vigile -> AppVigile: Saisir matricule
end

AppVigile -> API: GET /etudiants/{matricule}
API -> EtudiantService: findByMatricule()
EtudiantService -> DB: query étudiant
DB --> EtudiantService: Etudiant data
EtudiantService --> API: Etudiant object
API --> AppVigile: Etudiant info

== Pointer Présence ==
AppVigile -> AppVigile: Afficher info étudiant
Vigile -> AppVigile: Confirmer pointage
AppVigile -> API: POST /presences
note right: { etudiantId, seanceId, heurePointage }
API -> PresenceService: createPresence()
PresenceService -> PresenceService: Calculer statut\n(PRESENT/RETARD)
PresenceService -> DB: save presence
DB --> PresenceService: Presence saved
PresenceService --> API: Presence created
API --> AppVigile: Confirmation
AppVigile -> AppVigile: Afficher succès

@enduml
```

## 4. Diagramme de Séquence - Processus de Justification

```mermaid
@startuml
actor Etudiant
participant "App Mobile\nÉtudiant" as AppEtudiant
participant "API Gateway" as API
participant "Service\nJustification" as JustifService
participant "Service\nNotification" as NotifService
database "MongoDB" as DB
actor Admin
participant "App Web\nAdmin" as AppAdmin

== Soumission de Justification ==
Etudiant -> AppEtudiant: Sélectionner absence
AppEtudiant -> API: GET /absences/{id}
API --> AppEtudiant: Détails absence

Etudiant -> AppEtudiant: Remplir formulaire\njustification
Etudiant -> AppEtudiant: Télécharger document
AppEtudiant -> API: POST /justifications
note right: { absenceId, motif, type, document }

API -> JustifService: createJustification()
JustifService -> JustifService: Valider données
JustifService -> DB: save justification
DB --> JustifService: Justification saved
JustifService -> NotifService: notifyAdmin()
NotifService -> NotifService: Envoyer notification
JustifService --> API: Justification created
API --> AppEtudiant: Confirmation

== Validation par Admin ==
Admin -> AppAdmin: Consulter justifications
AppAdmin -> API: GET /justifications/pending
API -> JustifService: getPendingJustifications()
JustifService -> DB: query justifications
DB --> JustifService: List<Justification>
JustifService --> API: Justifications list
API --> AppAdmin: Afficher liste

Admin -> AppAdmin: Sélectionner justification
AppAdmin -> API: GET /justifications/{id}
API --> AppAdmin: Détails + document

Admin -> AppAdmin: Valider/Rejeter
AppAdmin -> API: PUT /justifications/{id}/status
note right: { statut: VALIDEE/REJETEE, commentaire }

API -> JustifService: updateStatus()
JustifService -> DB: update justification
JustifService -> NotifService: notifyStudent()
NotifService -> NotifService: Envoyer notification
JustifService --> API: Status updated
API --> AppAdmin: Confirmation

@enduml
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
