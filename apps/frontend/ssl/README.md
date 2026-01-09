# Certificats SSL pour HTTPS en développement

Ce dossier contient les certificats SSL pour exécuter Angular en HTTPS localement.

## ⚠️ Pourquoi HTTPS ?

Les navigateurs modernes exigent HTTPS pour accéder à :

- 📸 **Caméra** (MediaDevices API)
- 📍 **Géolocalisation** (Geolocation API)
- 🎤 **Microphone**
- 🔔 **Notifications Push**
- Et autres APIs sensibles

## 🔧 Installation de mkcert

### Windows

```powershell
# Avec Chocolatey
choco install mkcert

# Ou télécharger depuis GitHub
# https://github.com/FiloSottile/mkcert/releases
```

### macOS

```bash
brew install mkcert
```

### Linux

```bash
# Debian/Ubuntu
sudo apt install libnss3-tools
wget https://github.com/FiloSottile/mkcert/releases/download/v1.4.4/mkcert-v1.4.4-linux-amd64
chmod +x mkcert-v1.4.4-linux-amd64
sudo mv mkcert-v1.4.4-linux-amd64 /usr/local/bin/mkcert
```

## 📜 Générer les certificats

```bash
# 1. Installer le certificat racine (une seule fois)
mkcert -install

# 2. Se placer dans ce dossier
cd apps/frontend/ssl

# 3. Trouver votre IP locale
# Windows
ipconfig
# macOS/Linux
ifconfig

# 4. Générer les certificats (remplacer 192.168.x.x par votre IP)
mkcert localhost 127.0.0.1 ::1 192.168.1.10

# Exemple de sortie :
# Created a new certificate valid for the following names 📜
#  - "localhost"
#  - "127.0.0.1"
#  - "::1"
#  - "192.168.1.10"
#
# The certificate is at "./localhost+3.pem" and the key at "./localhost+3-key.pem" ✅
```

## 🚀 Utilisation

### Démarrer en HTTPS

```bash
# Depuis la racine du projet
pnpm --filter @lootopia/frontend dev:https

# Ou depuis apps/frontend
cd apps/frontend
pnpm dev:https
```

L'application sera accessible sur :

- 🔒 **https://localhost:4200** (depuis votre PC)
- 🔒 **https://192.168.x.x:4200** (depuis votre téléphone)

### Tester depuis un téléphone

1. **S'assurer que le téléphone et le PC sont sur le même réseau WiFi**
2. **Trouver l'IP locale de votre PC** (ex: 192.168.1.10)
3. **Ouvrir sur le téléphone** : `https://192.168.1.10:4200`
4. **Accepter le certificat auto-signé** si demandé

## 🔍 Vérification

Pour vérifier que les certificats sont bien générés :

```bash
ls -la
```

Vous devriez voir :

- ✅ `localhost+3.pem` (certificat)
- ✅ `localhost+3-key.pem` (clé privée)
- ✅ `.gitignore` (pour ne pas les committer)

## 🛡️ Sécurité

⚠️ **IMPORTANT** :

- Ces certificats sont pour le **développement local uniquement**
- Ne **jamais** committer les fichiers `.pem` dans Git (déjà ignorés)
- Ne **jamais** utiliser ces certificats en production
- Générer de nouveaux certificats sur chaque machine de développement

## 🐛 Dépannage

### Le navigateur refuse le certificat

1. Vérifier que `mkcert -install` a bien été exécuté
2. Sur Chrome : Aller dans `chrome://flags` et activer "Allow invalid certificates for localhost"
3. Redémarrer le navigateur

### Erreur "Certificate not found"

1. Vérifier que les fichiers `.pem` sont bien dans `apps/frontend/ssl/`
2. Vérifier les noms des fichiers dans `angular.json` (lignes 74-75)
3. Régénérer les certificats si nécessaire

### Le téléphone ne peut pas accéder

1. Vérifier que le firewall autorise le port 4200
2. Vérifier que PC et téléphone sont sur le même réseau
3. Essayer de désactiver temporairement le firewall pour tester

## 📚 Ressources

- [mkcert GitHub](https://github.com/FiloSottile/mkcert)
- [Angular HTTPS Configuration](https://angular.io/guide/build#using-ssl-in-development)
- [MDN - MediaDevices API](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices)
- [MDN - Geolocation API](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API)
