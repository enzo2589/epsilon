#  Système de Gestion d'Upload de Fichiers

##  Vue d'ensemble

Système professionnel et maintenable de gestion d'uploads de documents PDF. Le code est structuré pour être **facile à modifier**, **réutilisable**, et **scalable**.

---

##  Structure du Projet

```
epsilon/
├── index.php           # Page principale - Affichage du formulaire
├── header.php          # En-tête HTML partagé
├── config.php          # Configuration centralisée  À MODIFIER POUR CHANGER LES PARAMÈTRES
├── functions.php       # Fonctions réutilisables et logique métier
├── style.css           # Feuille de styles externe
├── uploads/            # Dossier de stockage des fichiers uploadés
└── README.md           # Ce fichier
```

---

##  Configuration et Personnalisation

###  Modifier les paramètres

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
##  Sécurité

Le système implémente plusieurs couches de sécurité:

1. ✅ **Validation d'extension** - Vérification du format de fichier
2. ✅ **Vérification du type MIME** - Détection du vrai type de fichier
3. ✅ **Limite de taille** - Vérification de la taille maximale
4. ✅ **Noms de fichiers sécurisés** - Génération de noms uniques avec `uniqid()`
5. ✅ **Permissions de fichier** - Définition des permissions appropriées (0644)
6. ✅ **Échappement HTML** - Protection contre les injections XSS avec `htmlspecialchars()`

---

##  Architecture - Séparation des Responsabilités

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

##  Fonctionnalités Principales

### Les 5 Fonctions Essentielles

**1. `processFileUpload()`** - Traite un upload complet  
- Valide + enregistre le fichier
- Retourne: `['success' => bool, 'message' => string]`
- Utilisée dans `index.php` pour traiter les formulaires

**2. `validateUploadedFile()`** - Vérifie 5 critères
- Erreur d'upload PHP
- Taille du fichier (< 5 MB)
- Extension (PDF autorisé)
- Type MIME (protection contre spoofing)
- Retourne les erreurs détaillées

**3. `saveUploadedFile()`** - Enregistre le fichier
- Génère un nom sécurisé avec `uniqid()`
- Déplace le fichier dans `/uploads/`
- Définit les permissions (0644)

**4. `generateSecureFilename()`** - Crée un nom unique
- Format: `upload_[timestamp]_[nom_nettoyé].[ext]`
- Empêche les collisions et les injections de chemin

**5. `isAllowedMimeType()`** - Détecte le vrai type
- Utilise `finfo_file()` pour lire le contenu réel
- Prévient les attaques (ex: virus.exe renommé en virus.pdf)

**Autres fonctions**: `isAllowedExtension()`, `isAllowedFileSize()`, `getUploadErrorMessage()`, `initUploadDirectory()`, `formatFileSize()`

---

##  Flux de Traitement

---

##  Avantages de cette Architecture

| Aspect | Avantage |
|--------|----------|
| **Maintenabilité** | Code organisé, facile à comprendre |
| **Modifiabilité** | Tous les paramètres en un seul endroit |
| **Réutilisabilité** | Fonctions génériques, utilisables ailleurs |
| **Scalabilité** | Structure prête pour ajouter des features |
| **Sécurité** | Bonnes pratiques appliquées |
| **Testabilité** | Chaque fonction peut être testée |

---

##  Auteur

**Enzo Deyrich**

---

##  Licence

Libre d'utilisation pour fins éducatives et commerciales.

---

**Dernière mise à jour**: April 2026
