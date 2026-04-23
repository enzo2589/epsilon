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

### Validation multi-niveaux
```php
validateUploadedFile()  // Vérifie TOUT
```

### Génération de noms sécurisés
```php
generateSecureFilename()  // Crée des noms uniques
```

### Gestion complète d'upload
```php
processFileUpload()  // Fait tout: valide + enregistre
```

---

## 📊 Flux de Traitement

```
Utilisateur soumet le formulaire
        ↓
index.php reçoit la requête POST
        ↓
Appelle processFileUpload($_FILES['file'])
        ↓
validateUploadedFile()  → Vérifications de sécurité
        ↓
saveUploadedFile()      → Enregistre le fichier
        ↓
Retourne le résultat    → Affiche le message à l'utilisateur
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
