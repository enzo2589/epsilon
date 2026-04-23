# 📤 Système de Gestion d'Upload de Fichiers

## 📋 Vue d'ensemble

Système professionnel et maintenable de gestion d'uploads de documents PDF. Le code est structuré pour être **facile à modifier**, **réutilisable**, et **scalable**.

---

## 📂 Structure du Projet

```
epsilon/
├── index.php           # Page principale - Affichage du formulaire
├── header.php          # En-tête HTML partagé
├── config.php          # Configuration centralisée ⭐ À MODIFIER POUR CHANGER LES PARAMÈTRES
├── functions.php       # Fonctions réutilisables et logique métier
├── style.css           # Feuille de styles externe
├── uploads/            # Dossier de stockage des fichiers uploadés
└── README.md           # Ce fichier
```

---

## 🔧 Configuration et Personnalisation

### 📝 Modifier les paramètres

**Tous les paramètres configurables sont centralisés dans `config.php`**

```php
// Exemples de modifications possibles:

// Changer la taille maximale d'upload (5 MB par défaut)
define('MAX_FILE_SIZE', 10 * 1024 * 1024); // 10 MB

// Autoriser d'autres formats de fichier
define('ALLOWED_EXTENSIONS', ['pdf', 'doc', 'docx']);
define('ALLOWED_MIME_TYPES', [
    'application/pdf',
    'application/msword',
    'application/vnd.openxmlformats-officedocument.wordprocessingml.document'
]);

// Changer les messages
define('MESSAGES', [
    'TITLE' => 'Mon nouveau titre',
    'BTN_SUBMIT' => 'Uploader le fichier',
    // ... etc
]);
```

### 🎨 Modifier les styles

**Les styles sont dans `style.css`**

```css
/* Changer les couleurs principales */
:root {
    --primary-color: #00ff00;      /* Couleur principale */
    --success-color: #0066ff;      /* Couleur de succès */
    --error-color: #ff0066;        /* Couleur d'erreur */
    --max-width: 600px;            /* Largeur du conteneur */
}
```

### 📄 Modifier les messages

**Éditer le tableau `MESSAGES` dans `config.php`**

```php
define('MESSAGES', [
    'TITLE' => 'Mon titre personnalisé',
    'SUBTITLE' => 'Ma description',
    'FILE_INPUT_BROWSE' => 'Sélectionner votre fichier',
    'ACCEPTED_FORMATS' => 'Formats: PDF, DOCX',
    'BTN_SUBMIT' => 'Uploader',
    'AUTHOR' => 'Votre nom',
    // ... et tous les autres messages
]);
```

---

## 🔒 Sécurité

Le système implémente plusieurs couches de sécurité:

1. ✅ **Validation d'extension** - Vérification du format de fichier
2. ✅ **Vérification du type MIME** - Détection du vrai type de fichier
3. ✅ **Limite de taille** - Vérification de la taille maximale
4. ✅ **Noms de fichiers sécurisés** - Génération de noms uniques avec `uniqid()`
5. ✅ **Permissions de fichier** - Définition des permissions appropriées (0644)
6. ✅ **Échappement HTML** - Protection contre les injections XSS avec `htmlspecialchars()`

---

## 🏗️ Architecture - Séparation des Responsabilités

### `config.php` - Configuration
- Contient TOUTES les constantes modifiables
- Centralisé et facile à maintenir
- **Avantage**: Changer un paramètre affecte toute l'application

### `functions.php` - Logique métier
- Fonctions réutilisables et testables
- Chaque fonction a un rôle unique
- **Avantage**: Facile à déboguer, tester, réutiliser

### `header.php` - En-tête HTML
- Contient la structure HTML de base
- Import du CSS externe
- **Avantage**: Partagé entre plusieurs pages

### `index.php` - Page principale
- Appelle les fonctions utilitaires
- Gère l'affichage
- Code propre et lisible

### `style.css` - Styles
- Externalisé pour maintenabilité
- Organisations par sections
- Variables CSS pour facile personnalisation

---

## 🚀 Fonctionnalités Principales

Ce projet implémente plusieurs fonctions essentielles pour gérer les uploads de manière **sécurisée** et **robuste**.

---

## 📘 Détails des Fonctions Principales

### 1️⃣ **processFileUpload()** - Fonction Maître 👑
**Rôle**: Orchestre tout le processus d'upload (validation + sauvegarde)

```php
$result = processFileUpload($_FILES['file']);
```

**Ce qu'elle fait**:
- Appelle `validateUploadedFile()` pour vérifier le fichier
- Si valide, appelle `saveUploadedFile()` pour l'enregistrer
- Retourne un tableau avec `['success' => bool, 'message' => string]`

**Pourquoi c'est important**: 
Centralise la logique complète en une seule fonction réutilisable. Exemple: si on ajoute une nouvelle page d'upload, on peut appeler cette même fonction.

**Exemple d'utilisation**:
```php
$result = processFileUpload($_FILES['file']);
if ($result['success']) {
    echo "✓ Succès: " . $result['message'];
} else {
    echo "✗ Erreur: " . $result['message'];
}
```

---

### 2️⃣ **validateUploadedFile()** - Validation Complète 🔍
**Rôle**: Vérifie TOUS les critères de sécurité avant acceptation

```php
$validation = validateUploadedFile($file);
```

**Vérifie**:
1. ✅ Un fichier a bien été uploadé (pas de `UPLOAD_ERR_NO_FILE`)
2. ✅ Pas d'erreurs PHP (code `UPLOAD_ERR_OK`)
3. ✅ La taille est acceptable (< `MAX_FILE_SIZE`)
4. ✅ L'extension est autorisée (dans `ALLOWED_EXTENSIONS`)
5. ✅ Le type MIME est valide (dans `ALLOWED_MIME_TYPES`)

**Retour**:
```php
[
    'success' => true/false,
    'message' => 'Explication du résultat'
]
```

**Pourquoi c'est important**: 
Prévient les attaques (fichiers malveillants, oversize, etc.). Chaque vérification est indépendante et explicite.

---

### 3️⃣ **saveUploadedFile()** - Enregistrement Sécurisé 💾
**Rôle**: Enregistre le fichier avec un nom sécurisé

```php
$save_result = saveUploadedFile($file);
```

**Ce qu'elle fait**:
1. Génère un nom de fichier sécurisé avec `generateSecureFilename()`
2. Déplace le fichier vers `/uploads/` avec `move_uploaded_file()`
3. Définit les permissions appropriées (`chmod 0644`)
4. Retourne le résultat

**Pourquoi c'est important**:
- Empêche les attaques par traversée de répertoire
- Génère des noms uniques (pas d'écrasement)
- Permissions restrictives (lecture seule, pas exécution)

---

### 4️⃣ **generateSecureFilename()** - Génération de Nom Sécurisé 🔐
**Rôle**: Crée un nom unique et sûr pour le fichier

```php
$secure_name = generateSecureFilename("Mon Document.pdf");
// Résultat: "upload_5f7a8b2c_mon_document.pdf"
```

**Comment ça marche**:
1. Extrait l'extension du fichier original
2. Nettoie le nom (remplace caractères spéciaux par `_`)
3. Ajoute `uniqid()` pour garantir l'unicité
4. Combine: `upload_[timestamp]_[nom_nettoyé].[ext]`

**Pourquoi c'est important**:
- `uniqid()` garantit qu'aucun fichier ne sera écrasé
- Le nettoyage du nom empêche les injections de chemin
- Noms lisibles mais sécurisés

---

### 5️⃣ **isAllowedExtension()** - Vérification Extension ✔️
**Rôle**: Vérifie que l'extension est autorisée

```php
if (isAllowedExtension("fichier.pdf")) {
    // PDF autorisé
}
```

**Fonctionnement**:
- Extrait l'extension du fichier
- La convertit en minuscules (case-insensitive)
- Vérifie dans la liste `ALLOWED_EXTENSIONS`

**Exemple**:
```php
define('ALLOWED_EXTENSIONS', ['pdf', 'doc', 'docx']);
isAllowedExtension("MyFile.PDF");  // true
isAllowedExtension("image.jpg");   // false
```

---

### 6️⃣ **isAllowedMimeType()** - Vérification MIME 🔬
**Rôle**: Détecte le vrai type du fichier (pas le type du nom)

```php
if (isAllowedMimeType("/tmp/php12345")) {
    // Type MIME valide
}
```

**Pourquoi c'est critique**:
Un utilisateur malveillant peut:
- Renommer `virus.exe` en `virus.pdf`
- Le navigateur dit "c'est un PDF"
- Mais PHP doit vérifier le VRAI type

**Comment PHP le fait**:
```php
$finfo = finfo_open(FILEINFO_MIME_TYPE);
$mime = finfo_file($finfo, $filepath);  // Examine le contenu réel
```

**Sécurité**:
- Extension `.pdf` renommée d'un `.exe` sera rejetée ✅
- Prévient les attaques de type malveillant

---

### 7️⃣ **isAllowedFileSize()** - Vérification Taille 📏
**Rôle**: Empêche les fichiers trop gros

```php
if (isAllowedFileSize($file['size'])) {
    // Taille acceptable
}
```

**Exemple**:
```php
define('MAX_FILE_SIZE', 5 * 1024 * 1024);  // 5 MB
isAllowedFileSize(3000000);   // true (< 5 MB)
isAllowedFileSize(10000000);  // false (> 5 MB)
```

**Pourquoi c'est important**:
- Prévient les attaques par DoS (empêcher les serveurs)
- Gère l'espace disque efficacement
- Évite les timeouts de l'upload

---

### 8️⃣ **getUploadErrorMessage()** - Traduction des Erreurs 📝
**Rôle**: Convertit les codes d'erreur PHP en messages lisibles

```php
echo getUploadErrorMessage($file['error']);
// Affiche: "Fichier trop volumineux"
```

**Codes PHP traduits**:
| Code | Message |
|------|---------|
| `UPLOAD_ERR_INI_SIZE` | Le fichier dépasse la limite php.ini |
| `UPLOAD_ERR_FORM_SIZE` | Le fichier dépasse la limite du formulaire |
| `UPLOAD_ERR_PARTIAL` | Le fichier a été partiellement uploadé |
| `UPLOAD_ERR_NO_FILE` | Aucun fichier n'a été envoyé |
| `UPLOAD_ERR_NO_TMP_DIR` | Dossier temporaire manquant |
| `UPLOAD_ERR_CANT_WRITE` | Impossible d'écrire le fichier |

---

### 9️⃣ **initUploadDirectory()** - Initialisation du Dossier 📂
**Rôle**: Crée le dossier `/uploads/` s'il n'existe pas

```php
initUploadDirectory();  // Crée `/uploads/` avec permission 0755
```

**Utile pour**:
- Premier lancement de l'application
- Assurer que le dossier existe avant d'y écrire
- Permissions appropriées (0755 = rwxr-xr-x)

---

### 🔟 **formatFileSize()** - Formattage Taille 📊
**Rôle**: Convertit les bytes en format lisible

```php
echo formatFileSize(5242880);  // "5 MB"
echo formatFileSize(1024);     // "1 KB"
echo formatFileSize(512);      // "0.5 B"
```

**Utile pour**: Afficher les tailles de manière humaine lisible

---

## 📊 Flux de Traitement Détaillé

### Étape par Étape - Ce qui se Passe Réellement

```
╔════════════════════════════════════════════════════════════════════════╗
║ 1. UTILISATEUR INTERAGIT AVEC LE FORMULAIRE                           ║
╚════════════════════════════════════════════════════════════════════════╝

┌─────────────────────────────────────────────────────────────────┐
│ Utilisateur:                                                    │
│ 1. Clique sur le formulaire                                     │
│ 2. Sélectionne un fichier PDF                                   │
│ 3. Clique sur "✓ Envoyer"                                       │
│ 4. Le navigateur affiche: "Fichier sélectionné : [nom]"        │
└─────────────────────────────────────────────────────────────────┘

        ↓

╔════════════════════════════════════════════════════════════════════════╗
║ 2. NAVIGATEUR SOUMET LE FORMULAIRE (POST REQUEST)                    ║
╚════════════════════════════════════════════════════════════════════════╝

Le navigateur envoie:
  • Méthode: POST
  • Enctype: multipart/form-data (pour les fichiers)
  • Données: $_FILES['file'] contenant:
    - name: "MonDocument.pdf"
    - type: "application/pdf"
    - tmp_name: "/tmp/php12345xyz"
    - error: 0
    - size: 1048576

        ↓

╔════════════════════════════════════════════════════════════════════════╗
║ 3. INDEX.PHP REÇOIT LA REQUÊTE                                        ║
╚════════════════════════════════════════════════════════════════════════╝

Code: if ($_SERVER['REQUEST_METHOD'] === 'POST' && isset($_FILES['file']))

Actions:
  1. session_start() - Initialiser la session
  2. Importe config.php - Charger les constantes
  3. Importe functions.php - Charger les fonctions

        ↓

╔════════════════════════════════════════════════════════════════════════╗
║ 4. APPEL À processFileUpload() - FONCTION PRINCIPALE                  ║
╚════════════════════════════════════════════════════════════════════════╝

Code: $result = processFileUpload($_FILES['file']);

Cette fonction fait tout:
  a) Appelle validateUploadedFile() pour vérifier
  b) Si valide, appelle saveUploadedFile() pour enregistrer
  c) Retourne le résultat

        ↓

╔════════════════════════════════════════════════════════════════════════╗
║ 5. VALIDATION (processFileUpload → validateUploadedFile)              ║
╚════════════════════════════════════════════════════════════════════════╝

validateUploadedFile() effectue 5 vérifications en cascade:

┌─ Vérification 1: Erreur d'upload PHP ───────────────────┐
│ Code: if ($file['error'] === UPLOAD_ERR_NO_FILE)        │
│ Si ÉCHOUE: "Aucun fichier sélectionné"                  │
│ Si OK: Continue                                          │
└────────────────────────────────────────────────────────────┘

┌─ Vérification 2: Code d'erreur PHP ─────────────────────┐
│ Code: if ($file['error'] !== UPLOAD_ERR_OK)             │
│ Vérifie: UPLOAD_ERR_INI_SIZE, UPLOAD_ERR_FORM_SIZE, etc │
│ Si ÉCHOUE: Affiche le message d'erreur correspondant    │
│ Si OK: Continue                                          │
└────────────────────────────────────────────────────────────┘

┌─ Vérification 3: Taille du fichier ─────────────────────┐
│ Code: if (!isAllowedFileSize($file['size']))            │
│ Vérifie: size <= MAX_FILE_SIZE (5 MB)                   │
│ Si ÉCHOUE: "Fichier trop volumineux"                    │
│ Si OK: Continue                                          │
└────────────────────────────────────────────────────────────┘

┌─ Vérification 4: Extension du fichier ──────────────────┐
│ Code: if (!isAllowedExtension($file['name']))           │
│ Vérifie: extension dans ALLOWED_EXTENSIONS []           │
│ Si ÉCHOUE: "Format non autorisé"                        │
│ Si OK: Continue                                          │
└────────────────────────────────────────────────────────────┘

┌─ Vérification 5: Type MIME du fichier ──────────────────┐
│ Code: if (!isAllowedMimeType($file['tmp_name']))        │
│ Utilise: finfo_file() pour lire le VRAI type           │
│ Vérifie: type dans ALLOWED_MIME_TYPES []                │
│ Si ÉCHOUE: "Type invalide détecté"                      │
│ Si OK: Toutes les vérifications sont passées ✅         │
└────────────────────────────────────────────────────────────┘

Résultat de validateUploadedFile():
  success: true/false
  message: "Explication détaillée"

        ↓

╔════════════════════════════════════════════════════════════════════════╗
║ 6. SI VALIDE: SAUVEGARDE (processFileUpload → saveUploadedFile)       ║
╚════════════════════════════════════════════════════════════════════════╝

saveUploadedFile() effectue 4 étapes:

┌─ Étape 1: Générer un nom sécurisé ─────────────────────┐
│ Appel: generateSecureFilename("MonDocument.pdf")        │
│ Résultat: "upload_5f7a8b2c_monDocument.pdf"             │
│ Sécurité: Nom unique, pas de chemin dangereux           │
└────────────────────────────────────────────────────────────┘

┌─ Étape 2: Déplacer le fichier ─────────────────────────┐
│ Code: move_uploaded_file($tmp_name, $final_path)        │
│ Du: /tmp/php12345xyz                                    │
│ Au: /uploads/upload_5f7a8b2c_monDocument.pdf            │
│ Si ÉCHOUE: "Erreur lors de la sauvegarde"              │
└────────────────────────────────────────────────────────────┘

┌─ Étape 3: Définir les permissions ─────────────────────┐
│ Code: chmod($final_path, 0644)                          │
│ Permissions: rw-r--r-- (lecture, pas exécution)         │
│ Sécurité: Fichier lisible mais pas dangereux            │
└────────────────────────────────────────────────────────────┘

┌─ Étape 4: Retourner le résultat ──────────────────────┐
│ Succès:                                                 │
│   success: true                                         │
│   message: "Fichier téléchargé : MonDocument.pdf"       │
└────────────────────────────────────────────────────────────┘

        ↓

╔════════════════════════════════════════════════════════════════════════╗
║ 7. INDEX.PHP AFFICHE LE RÉSULTAT À L'UTILISATEUR                     ║
╚════════════════════════════════════════════════════════════════════════╝

Code:
  if ($result['success']) {
    $success = true;
    $message = $result['message'];
  } else {
    $error = $result['message'];
  }

Affichage HTML:
  • Si success: Message VERT avec ✓
  • Si erreur: Message ROUGE avec ✗

        ↓

╔════════════════════════════════════════════════════════════════════════╗
║ 8. UTILISATEUR VOIT LE RÉSULTAT                                       ║
╚════════════════════════════════════════════════════════════════════════╝

Succès:
  ✓ Succès !
  Fichier téléchargé avec succès : MonDocument.pdf

Erreur:
  ✗ Erreur !
  Format de fichier non autorisé. Formats acceptés : pdf
```

### Diagramme de Décision

```
DÉBUT
  │
  ├─ Fichier uploadé? ──► NON ──► Erreur: "Aucun fichier"
  │ OUI
  │
  ├─ Erreur PHP? ──► OUI ──► Erreur: [Message d'erreur PHP]
  │ NON
  │
  ├─ Taille OK? ──► NON ──► Erreur: "Fichier trop volumineux"
  │ OUI
  │
  ├─ Extension OK? ──► NON ──► Erreur: "Format non autorisé"
  │ OUI
  │
  ├─ MIME OK? ──► NON ──► Erreur: "Type invalide détecté"
  │ OUI
  │
  ├─ Générer nom sécurisé
  │
  ├─ Déplacer fichier
  │
  ├─ Définir permissions
  │
  └─► SUCCÈS: "Fichier téléchargé avec succès"
```

---

## 💡 Avantages de cette Architecture

| Aspect | Avantage |
|--------|----------|
| **Maintenabilité** | Code organisé, facile à comprendre |
| **Modifiabilité** | Tous les paramètres en un seul endroit |
| **Réutilisabilité** | Fonctions génériques, utilisables ailleurs |
| **Scalabilité** | Structure prête pour ajouter des features |
| **Sécurité** | Bonnes pratiques appliquées |
| **Testabilité** | Chaque fonction peut être testée |

---

## 🔄 Comment Ajouter une Nouvelle Fonctionnalité

### Exemple: Ajouter la compression de PDF

1. **Créer la fonction dans `functions.php`**:
```php
function compressPDF($filepath) {
    // Votre code de compression
    return $success;
}
```

2. **L'utiliser dans `index.php`**:
```php
if ($result['success']) {
    $result = compressPDF($final_path);
    // ...
}
```

3. **Ajouter un message dans `config.php`**:
```php
define('MESSAGES', [
    'PDF_COMPRESSED' => 'PDF compressé avec succès',
    // ...
]);
```

---

## 🧪 Tester le Système

1. **Upload d'un PDF valide** → Devrait fonctionner
2. **Upload d'un fichier non-PDF** → Erreur "Format non autorisé"
3. **Upload d'un fichier > 5MB** → Erreur "Fichier trop volumineux"
4. **Aucun fichier sélectionné** → Erreur "Aucun fichier"

---

## 📝 Notes de Maintenance

- **Vérifier régulièrement** le contenu du dossier `/uploads/`
- **Nettoyer les anciens fichiers** selon votre politique de rétention
- **Monitorer les erreurs** PHP dans les logs
- **Mettre à jour les messages** si nécessaire dans `config.php`

---

## 👨‍💻 Auteur

**Enzo Deyrich**

---

## 📄 Licence

Libre d'utilisation pour fins éducatives et commerciales.

---

**Dernière mise à jour**: April 2026
