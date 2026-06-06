# Rapport d’audit de sécurité Android avec Drozer

## 1. Informations générales

* **Lab** : Analyse de sécurité Android avec Drozer
* **Application analysée** : Diva
* **Package** : `jakhar.aseem.diva`
* **Version** : 1.0
* **Environnement** : Émulateur Android
* **Appareil utilisé** : `emulator-5554`
* **Outil principal** : Drozer
* **Objectif** : Identifier les composants Android exposés et évaluer les risques de sécurité associés

---

## 2. Introduction

Ce laboratoire a pour objectif d’utiliser **Drozer** pour analyser la surface d’attaque d’une application Android vulnérable.

Drozer permet d’interroger une application Android installée sur un appareil ou un émulateur afin d’identifier ses composants exposés : activités, services, receivers et content providers.

Dans ce lab, l’application analysée est **Diva**, dont le package est :

```text
jakhar.aseem.diva
```

L’analyse a été réalisée uniquement dans un environnement contrôlé et dans un cadre pédagogique.

---

## 3. Objectifs pédagogiques

Les objectifs de ce lab sont :

* installer et configurer Drozer ;
* connecter Drozer à l’émulateur Android ;
* identifier les applications installées ;
* analyser les composants exposés de l’application cible ;
* vérifier les protections appliquées aux composants ;
* identifier les risques de sécurité ;
* proposer des remédiations adaptées ;
* documenter les résultats sous forme de rapport d’audit.

---

## 4. Environnement utilisé

| Élément           | Valeur              |
| ----------------- | ------------------- |
| Système hôte      | Windows             |
| Terminal          | PowerShell          |
| Émulateur         | Android Emulator    |
| Device            | `emulator-5554`     |
| Outil d’analyse   | Drozer              |
| Agent Android     | Drozer Agent        |
| Application cible | Diva                |
| Package cible     | `jakhar.aseem.diva` |

---

## 5. Vérification de l’environnement

La première étape a été de vérifier que l’émulateur Android est bien reconnu par ADB.

Commande utilisée :

```powershell
adb devices
```

Résultat observé :

```text
List of devices attached
emulator-5554    device
```

<img width="350" height="52" alt="image" src="https://github.com/user-attachments/assets/0e7e0074-1d9d-49e4-89f3-b4b0d8e15e88" />


Cette étape confirme que l’émulateur Android est correctement connecté à la machine hôte.

---

## 6. Activation de l’agent Drozer

L’agent Drozer a été installé et lancé sur l’émulateur Android.

Dans l’application Drozer Agent, l’option suivante a été activée :

```text
Embedded Server
```

Le port utilisé par défaut est :

```text
31415
```

<img width="378" height="487" alt="image" src="https://github.com/user-attachments/assets/00d291d6-26b8-47bd-ac8b-6036e07d873d" />


Cette étape est nécessaire pour permettre à la console Drozer sur le PC de communiquer avec l’agent installé dans l’émulateur.

---

## 7. Configuration du port forwarding

Pour connecter Drozer au serveur embarqué de l’agent Android, le port forwarding ADB est configuré avec la commande suivante :

```powershell
adb forward tcp:31415 tcp:31415
```

Cette commande redirige le port local `31415` vers le port `31415` de l’émulateur.

---

## 8. Connexion à la console Drozer

Après l’activation de l’agent, la console Drozer est lancée avec :

```powershell
drozer console connect
```

Une fois connecté, l’invite Drozer apparaît :

```text
dz>
```

Cette invite confirme que Drozer est bien connecté à l’émulateur.

<img width="556" height="242" alt="image" src="https://github.com/user-attachments/assets/4bb8a5e5-3796-484c-a701-a90c04750cdf" />


<img width="668" height="105" alt="image" src="https://github.com/user-attachments/assets/4e644805-5f44-4fae-94a3-4c95e441aefc" />

---

## 9. Liste des modules Drozer

Pour vérifier que Drozer fonctionne correctement, la liste des modules disponibles a été affichée :

```text
dz> list
```

La sortie montre plusieurs modules utiles, par exemple :

```text
app.activity.info
app.activity.start
app.broadcast.info
app.package.info
app.package.list
app.package.manifest
app.provider.info
scanner.provider.finduris
```

Cette étape confirme que Drozer est opérationnel et que les modules d’analyse Android sont disponibles.

---

## 10. Identification des applications installées

La commande suivante permet de lister les applications installées sur l’émulateur :

```text
dz> run app.package.list
```

L’application cible identifiée est :

```text
jakhar.aseem.diva
```

<img width="647" height="390" alt="image" src="https://github.com/user-attachments/assets/cfaf91dc-96ad-4dd9-8127-ac314ca2425d" />

---

## 11. Informations générales sur l’application cible

La commande suivante permet d’obtenir les informations générales sur l’application Diva :

```text
dz> run app.package.info -a jakhar.aseem.diva
```

Résultat observé :

```text
Package: jakhar.aseem.diva
Application Label: Diva
Process Name: jakhar.aseem.diva
Version: 1.0
UID: 10182
GID: 3003
```

Permissions utilisées par l’application :

```text
android.permission.WRITE_EXTERNAL_STORAGE
android.permission.READ_EXTERNAL_STORAGE
android.permission.INTERNET
android.permission.ACCESS_MEDIA_LOCATION
```

<img width="660" height="180" alt="image" src="https://github.com/user-attachments/assets/77463cb5-6db8-40ae-888f-1a7026664b1b" />

Analyse :

L’application utilise des permissions sensibles liées au stockage externe et à Internet. Ces permissions doivent être vérifiées car elles peuvent augmenter l’impact d’une mauvaise configuration des composants exposés.

---

## 12. Analyse des activités exposées

La commande suivante a été utilisée pour analyser les activités de l’application :

```text
dz> run app.activity.info -a jakhar.aseem.diva
```

Activités identifiées :

```text
jakhar.aeseem.diva.MainActivity
jakhar.aeseem.diva.APICredsActivity
jakhar.aeseem.diva.APICreds2Activity
```

Les activités affichent :

```text
Permission: null
```

<img width="647" height="356" alt="image" src="https://github.com/user-attachments/assets/280e35a5-7281-4bc5-9a93-fe472a5c4944" />


Analyse :

Le champ `Permission: null` indique qu’aucune permission spécifique ne protège ces activités. Cela peut être risqué si des activités internes ou sensibles sont exportées et accessibles depuis une autre application.

---

## 13. Analyse des intent-filters

La commande suivante a été utilisée pour afficher les intent-filters associés aux activités :

```text
dz> run app.activity.info -a jakhar.aeseem.diva -i
```

Résultats importants :

### MainActivity

```text
Action:
android.intent.action.MAIN

Category:
android.intent.category.LAUNCHER
```

Cette activité est normale, car elle correspond au point d’entrée principal de l’application.

### APICredsActivity

```text
Action:
jakhar.aeseem.diva.action.VIEW_CREDS

Category:
android.intent.category.DEFAULT
Permission: null
```

### APICreds2Activity

```text
Action:
jakhar.aeseem.diva.action.VIEW_CREDS2

Category:
android.intent.category.DEFAULT
Permission: null
```

<img width="657" height="232" alt="image" src="https://github.com/user-attachments/assets/ccae5a2d-a9b1-4576-91eb-a38efe6d132d" />

<img width="662" height="557" alt="image" src="https://github.com/user-attachments/assets/77062a42-f35e-404a-b42f-b44b0fe3d523" />

Analyse :

Les activités `APICredsActivity` et `APICreds2Activity` possèdent des intent-filters personnalisés et ne sont pas protégées par une permission. Cela peut permettre à une autre application de les appeler directement.

---

## 14. Analyse des broadcast receivers

La commande utilisée est :

```text
dz> run app.broadcast.info -a jakhar.aeseem.diva
```

Résultat observé :

```text
No matching receivers.
```

<img width="641" height="97" alt="image" src="https://github.com/user-attachments/assets/b736390a-3ad0-4646-b31c-44678dbeb019" />


Analyse :

Aucun broadcast receiver exposé n’a été identifié dans l’application. Le risque associé aux receivers est donc faible dans ce lab.

---

## 15. Analyse des content providers

La commande suivante a été utilisée :

```text
dz> run app.provider.info -a jakhar.aeseem.diva
```

Résultat observé :

```text
Authority: jakhar.aeseem.diva.provider.notesprovider
Read Permission: null
Write Permission: null
Content Provider: jakhar.aeseem.diva.NotesProvider
Multiprocess Allowed: False
Grant Uri Permissions: False
```

<img width="665" height="206" alt="image" src="https://github.com/user-attachments/assets/fe2a8a92-9e04-4cb7-a127-4772cc23babb" />


Analyse :

Le provider `NotesProvider` est particulièrement sensible car :

* aucune permission de lecture n’est définie ;
* aucune permission d’écriture n’est définie ;
* une authority accessible est exposée ;
* le provider semble lié à des données de type notes.

Un content provider sans protection peut exposer des données à d’autres applications installées sur le même appareil.

---

## 16. Analyse du manifeste Android

Le manifeste de l’application a été consulté avec la commande :

```text
dz> run app.package.manifest jakhar.aeseem.diva
```

Dans le manifeste, on observe notamment :

```xml
<manifest
    package="jakhar.aeseem.diva"
    versionName="1.0">

<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.INTERNET" />

<application
    android:debuggable="true"
    android:allowBackup="true">
```

<img width="657" height="481" alt="image" src="https://github.com/user-attachments/assets/8d1b64b1-0716-4538-ba55-b030a9d91b5c" />

Analyse :

Deux éléments sont importants :

1. `android:debuggable="true"`
   Cela indique que l’application est compilée en mode debug, ce qui est dangereux pour une application de production.

2. `android:allowBackup="true"`
   Cela peut permettre la sauvegarde de données de l’application, ce qui peut être risqué si des données sensibles sont stockées localement.

---

## 17. Recherche des URI accessibles

La commande suivante a été utilisée pour rechercher les URI accessibles du content provider :

```text
dz> run scanner.provider.finduris -a jakhar.aeseem.diva
```

Résultats observés :

```text
Got a response from content URI:
content://jakhar.aeseem.diva.provider.notesprovider/notes

Got a response from content URI:
content://jakhar.aeseem.diva.provider.notesprovider/notes/
```

<img width="673" height="233" alt="image" src="https://github.com/user-attachments/assets/89f8a4bd-9138-46c5-a77a-596909b07a5a" />
`

Analyse :

Drozer a identifié des URI accessibles au niveau du provider. Cela confirme que le provider répond à certaines requêtes et qu’il doit être protégé correctement.

---

## 18. Tableau récapitulatif des composants exposés

| Type               | Composant             | Exporté / Accessible  | Protection                   | Risque                             |
| ------------------ | --------------------- | --------------------- | ---------------------------- | ---------------------------------- |
| Activity           | `MainActivity`        | Oui                   | Aucune permission spécifique | Normal si activité principale      |
| Activity           | `APICredsActivity`    | Oui                   | `Permission: null`           | Accès direct possible              |
| Activity           | `APICreds2Activity`   | Oui                   | `Permission: null`           | Accès direct possible              |
| Broadcast Receiver | Aucun receiver trouvé | Non                   | Non applicable               | Faible                             |
| Provider           | `NotesProvider`       | Oui / URI accessibles | Read/Write Permission: null  | Risque d’accès aux données         |
| Manifest           | Application debug     | Oui                   | `debuggable=true`            | Risque élevé en production         |
| Manifest           | Backup autorisé       | Oui                   | `allowBackup=true`           | Risque de fuite de données locales |

---

## 19. Découvertes principales

### V1 — Activités sensibles accessibles sans permission

Les activités suivantes ne sont pas protégées par une permission :

```text
APICredsActivity
APICreds2Activity
```

Ces activités semblent liées à des identifiants ou des informations sensibles, car leurs actions sont :

```text
VIEW_CREDS
VIEW_CREDS2
```

Risque :

Une application externe pourrait potentiellement lancer ces activités directement si elles sont exportées via intent-filter.

Sévérité : Élevée

---

### V2 — Content Provider NotesProvider sans permission

Le content provider suivant est exposé :

```text
jakhar.aeseem.diva.provider.notesprovider
```

Il ne définit aucune permission de lecture ou d’écriture :

```text
Read Permission: null
Write Permission: null
```

De plus, Drozer a identifié des URI accessibles :

```text
content://jakhar.aeseem.diva.provider.notesprovider/notes
content://jakhar.aeseem.diva.provider.notesprovider/notes/
```

Risque :

Une autre application pourrait interagir avec le provider si aucune protection n’est appliquée.

Sévérité : Critique

---

### V3 — Application compilée en mode debug

Le manifeste indique :

```xml
android:debuggable="true"
```

Risque :

Une application en mode debug facilite l’analyse, le débogage et l’instrumentation. Ce mode ne doit pas être activé en production.

Sévérité : Élevée

---

### V4 — Sauvegarde Android autorisée

Le manifeste indique :

```xml
android:allowBackup="true"
```

Risque :

Des données locales de l’application peuvent être sauvegardées et récupérées si elles ne sont pas protégées correctement.

Sévérité : Moyenne

---

### V5 — Permissions de stockage sensibles

L’application utilise :

```text
READ_EXTERNAL_STORAGE
WRITE_EXTERNAL_STORAGE
```

Risque :

Ces permissions peuvent exposer ou manipuler des fichiers du stockage externe si l’application gère mal ses données.

Sévérité : Moyenne

---

## 20. Tableau de triage

| ID | Composant            | Vulnérabilité                             | Confiance | Sévérité | Impact                                 | Recommandation                                     | Statut       |
| -- | -------------------- | ----------------------------------------- | --------- | -------- | -------------------------------------- | -------------------------------------------------- | ------------ |
| V1 | `APICredsActivity`   | Activité accessible sans permission       | Élevée    | Élevée   | Accès direct à un écran sensible       | Ajouter une permission ou `exported=false`         | À corriger   |
| V2 | `APICreds2Activity`  | Activité accessible sans permission       | Élevée    | Élevée   | Accès direct à un écran sensible       | Ajouter une permission ou `exported=false`         | À corriger   |
| V3 | `NotesProvider`      | Provider sans permission lecture/écriture | Élevée    | Critique | Accès potentiel aux données            | Ajouter permissions read/write ou `exported=false` | À corriger   |
| V4 | Application          | `debuggable=true`                         | Élevée    | Élevée   | Facilite l’analyse et le debug externe | Désactiver debug en production                     | À corriger   |
| V5 | Application          | `allowBackup=true`                        | Élevée    | Moyenne  | Risque d’extraction de données locales | Définir `allowBackup=false`                        | À corriger   |
| V6 | Permissions stockage | Permissions larges                        | Moyenne   | Moyenne  | Risque d’exposition de fichiers        | Réduire les permissions au strict nécessaire       | À surveiller |

---

## 21. Mapping OWASP MASVS / MASTG

| ID | Vulnérabilité                      | Référence MASVS / MASTG | Description                                                           |
| -- | ---------------------------------- | ----------------------- | --------------------------------------------------------------------- |
| V1 | Activités exposées sans permission | MSTG-PLATFORM-1         | L’application ne doit exposer que les composants nécessaires          |
| V2 | Content Provider mal protégé       | MSTG-PLATFORM-2         | Les composants exposés doivent être protégés par permissions          |
| V3 | Provider accessible                | MSTG-STORAGE-2          | Les données sensibles ne doivent pas être accessibles sans protection |
| V4 | Application debuggable             | MSTG-RESILIENCE-2       | L’application de production ne doit pas être facilement débogable     |
| V5 | Backup activé                      | MSTG-STORAGE-8          | Les données sensibles doivent être protégées contre l’extraction      |
| V6 | Permissions excessives             | MSTG-PLATFORM-1         | Les permissions doivent respecter le principe du moindre privilège    |

---

## 22. Recommandations et remédiations

### 22.1 Sécuriser les activités sensibles

Les activités internes ou sensibles doivent être non exportées si elles ne doivent pas être appelées par d’autres applications.

Avant :

```xml
<activity
    android:name=".APICredsActivity"
    android:exported="true" />
```

Après :

```xml
<activity
    android:name=".APICredsActivity"
    android:exported="false" />
```

Même correction pour :

```xml
<activity
    android:name=".APICreds2Activity"
    android:exported="false" />
```

Si l’activité doit rester accessible depuis l’extérieur, il faut ajouter une permission personnalisée de niveau `signature`.

---

### 22.2 Protéger le Content Provider

Avant :

```xml
<provider
    android:name=".NotesProvider"
    android:authorities="jakhar.aeseem.diva.provider.notesprovider"
    android:exported="true" />
```

Après, si le provider ne doit pas être accessible depuis d’autres applications :

```xml
<provider
    android:name=".NotesProvider"
    android:authorities="jakhar.aeseem.diva.provider.notesprovider"
    android:exported="false" />
```

Si le provider doit être exposé :

```xml
<permission
    android:name="jakhar.aeseem.diva.permission.READ_NOTES"
    android:protectionLevel="signature" />

<provider
    android:name=".NotesProvider"
    android:authorities="jakhar.aeseem.diva.provider.notesprovider"
    android:exported="true"
    android:readPermission="jakhar.aeseem.diva.permission.READ_NOTES"
    android:writePermission="jakhar.aeseem.diva.permission.WRITE_NOTES" />
```

---

### 22.3 Désactiver le mode debug

Avant :

```xml
<application
    android:debuggable="true">
```

Après :

```xml
<application
    android:debuggable="false">
```

En production, le mode debug ne doit jamais être activé.

---

### 22.4 Désactiver la sauvegarde Android si des données sensibles sont stockées

Avant :

```xml
<application
    android:allowBackup="true">
```

Après :

```xml
<application
    android:allowBackup="false">
```

Si la sauvegarde est nécessaire, il faut exclure les fichiers sensibles via des règles de backup.

---

### 22.5 Réduire les permissions

L’application demande des permissions de stockage :

```text
READ_EXTERNAL_STORAGE
WRITE_EXTERNAL_STORAGE
```

Il faut vérifier si ces permissions sont réellement nécessaires.

Recommandation :

* supprimer les permissions non utilisées ;
* éviter le stockage externe pour les données sensibles ;
* utiliser le stockage interne privé de l’application ;
* chiffrer les données sensibles si nécessaire.

---

## 23. Structure des preuves

Les preuves peuvent être organisées comme suit :

```text
preuves/
├── 01_adb_devices.png
├── 02_drozer_agent.png
├── 03_drozer_console.png
├── 04_package_list.png
├── 05_package_info.png
├── 06_activity_info.png
├── 07_activity_intent_filters.png
├── 08_broadcast_info.png
├── 09_provider_info.png
├── 10_manifest_analysis.png
└── 11_provider_finduris.png
```

---

## 24. Checklist de fin d’audit

* [x] L’émulateur Android est détecté par ADB
* [x] L’agent Drozer est installé et activé
* [x] La console Drozer est connectée
* [x] Le package cible est identifié
* [x] Les activités exposées sont analysées
* [x] Les intent-filters sont vérifiés
* [x] Les broadcast receivers sont vérifiés
* [x] Les content providers sont analysés
* [x] Les URI accessibles sont recherchées
* [x] Les risques sont documentés
* [x] Les remédiations sont proposées

---

## 25. Conclusion

Ce lab m’a permis d’utiliser Drozer pour analyser la surface d’attaque d’une application Android.

L’analyse de l’application **Diva** a permis d’identifier plusieurs faiblesses de configuration :

* des activités sensibles accessibles sans permission ;
* un content provider sans permission de lecture ou d’écriture ;
* des URI accessibles via le provider ;
* une application compilée avec `debuggable=true` ;
* l’option `allowBackup=true` activée ;
* des permissions de stockage potentiellement excessives.

Les résultats montrent que la mauvaise configuration des composants Android peut exposer une application à des risques importants, même sans exploiter directement une vulnérabilité complexe.

Les remédiations principales consistent à limiter l’exportation des composants, protéger les providers par des permissions fortes, désactiver le mode debug, désactiver la sauvegarde automatique si nécessaire et appliquer le principe du moindre privilège.

---

## 26. Remarque éthique

Cette analyse a été réalisée uniquement dans un environnement contrôlé, sur une application volontairement vulnérable et dans un cadre pédagogique.
Aucune donnée réelle n’a été manipulée et aucune exploitation offensive n’a été effectuée.
