# 🚀 Guide de démarrage rapide - Samsung TV Art Mode

## Installation en 5 minutes

### Étape 1: Télécharger les fichiers
Vous avez déjà tous les fichiers nécessaires !

### Étape 2: Préparer la structure
```bash
cd /config/custom_components/
mkdir -p samsungtv_smart/api
```

### Étape 3: Copier les fichiers principaux

**Fichiers à copier dans `custom_components/samsungtv_smart/`:**
- `__init__.py`
- `const.py`
- `manifest.json`
- `services.yaml`
- `switch.py`
- `select.py`
- `art_services.py`

**Fichier à copier dans `custom_components/samsungtv_smart/api/`:**
- `artmode.py`

```bash
# Exemple de commandes
cp __init__.py /config/custom_components/samsungtv_smart/
cp const.py /config/custom_components/samsungtv_smart/
cp manifest.json /config/custom_components/samsungtv_smart/
cp services.yaml /config/custom_components/samsungtv_smart/
cp switch.py /config/custom_components/samsungtv_smart/
cp select.py /config/custom_components/samsungtv_smart/
cp art_services.py /config/custom_components/samsungtv_smart/
cp artmode.py /config/custom_components/samsungtv_smart/api/
```

### Étape 4: Ajouter les fichiers de base

**Important**: Vous devez aussi avoir les fichiers de base de l'intégration Samsung TV Smart:
- `config_flow.py`
- `media_player.py`
- `api/samsungws.py`
- Et autres fichiers de ollo69/ha-samsungtv-smart

**Option A - Via HACS** (si disponible):
1. Installer d'abord l'intégration Samsung TV Smart via HACS
2. Puis ajouter les fichiers Art Mode par dessus

**Option B - Installation manuelle**:
1. Télécharger https://github.com/ollo69/ha-samsungtv-smart
2. Copier tous les fichiers dans `custom_components/samsungtv_smart/`
3. Puis ajouter les fichiers Art Mode

### Étape 5: Redémarrer Home Assistant

```bash
# Via l'interface: Configuration → Système → Redémarrer
# Ou en ligne de commande:
ha core restart
```

### Étape 6: Configurer votre TV

1. Aller dans **Paramètres → Appareils et services**
2. Cliquer sur **+ Ajouter une intégration**
3. Rechercher **"Samsung TV Smart"**
4. Entrer l'**adresse IP** de votre TV
5. ⚠️ **IMPORTANT**: Accepter la connexion sur l'écran de votre TV !

### Étape 7: Vérifier l'installation

Vérifier dans **Developer Tools → Services** que vous voyez:
- `samsungtv_smart.art_upload`
- `samsungtv_smart.art_delete`
- `samsungtv_smart.art_set_brightness`
- Et 5 autres services Art Mode

### Étape 8: Premier test

**Test du Switch Art Mode**:
```yaml
# Developer Tools → Services
service: switch.turn_on
target:
  entity_id: switch.samsung_tv_art_mode
```

**Test d'upload d'image**:
```yaml
service: samsungtv_smart.art_upload
target:
  entity_id: media_player.samsung_tv
data:
  image_path: "/config/www/test_image.jpg"
  matte_type: "modern"
  matte_color: "warm"
```

## ⚡ Configuration minimale

### Automatisation de base
Activer l'Art Mode quand la TV s'éteint:

```yaml
automation:
  - alias: "Active Art Mode automatiquement"
    trigger:
      - platform: state
        entity_id: media_player.samsung_tv
        to: "off"
    action:
      - service: switch.turn_on
        target:
          entity_id: switch.samsung_tv_art_mode
```

### Carte Lovelace simple

```yaml
type: entities
title: Samsung Frame
entities:
  - entity: switch.samsung_tv_art_mode
    name: Mode Art
  - entity: select.samsung_tv_art_my_photos
    name: Choisir une photo
```

## 📱 Utilisation quotidienne

### Uploader une photo
1. Placer votre photo dans `/config/www/art/`
2. Appeler le service:
   ```yaml
   service: samsungtv_smart.art_upload
   data:
     image_path: "/config/www/art/ma_photo.jpg"
   ```

### Changer de photo
Utiliser le select dans l'interface, ou:
```yaml
service: select.select_option
target:
  entity_id: select.samsung_tv_art_my_photos
data:
  option: "ma_photo.jpg"
```

### Régler la luminosité
```yaml
service: samsungtv_smart.art_set_brightness
data:
  brightness: 75  # 0-100
```

## 🔧 Dépannage rapide

### Problème: Switch Art Mode n'apparaît pas
**Causes possibles**:
- TV n'est pas un modèle "The Frame"
- Connexion WebSocket non établie

**Solutions**:
1. Vérifier les logs: `Configuration → Logs`
2. Rechercher "Art Mode" dans les logs
3. Redémarrer la TV

### Problème: Services non disponibles
**Solution**:
```bash
# Vérifier que les fichiers sont bien présents
ls -la /config/custom_components/samsungtv_smart/

# Redémarrer HA
ha core restart

# Vérifier les logs
tail -f /config/home-assistant.log | grep samsungtv
```

### Problème: Erreur lors de l'upload
**Solutions**:
- Vérifier que le fichier existe
- Utiliser des chemins absolus: `/config/www/...`
- Tester avec une petite image (< 2 MB)
- Format PNG ou JPEG uniquement

## 📚 Documentation complète

Pour aller plus loin:
1. **README.md** - Documentation complète
2. **automations_examples.md** - Plus d'exemples
3. **ADVANCED_CONFIG.md** - Configurations avancées
4. **INTEGRATION_GUIDE.md** - Détails techniques

## ✅ Checklist de vérification

Installation complète:
- [ ] Fichiers Python copiés
- [ ] Fichiers YAML copiés  
- [ ] Structure des dossiers correcte
- [ ] Home Assistant redémarré
- [ ] TV configurée
- [ ] Switch Art Mode visible
- [ ] Services disponibles
- [ ] Premier test réussi

## 🎯 Premiers usages recommandés

1. **Jour 1**: Tester le switch Art Mode
2. **Jour 2**: Uploader 2-3 photos favorites
3. **Jour 3**: Créer une automatisation simple
4. **Jour 4**: Configurer la luminosité automatique
5. **Jour 5**: Créer un diaporama

## 💡 Astuces

### Préparation d'images
```bash
# Redimensionner pour The Frame (3840x2160)
convert input.jpg -resize 3840x2160^ -gravity center -extent 3840x2160 output.jpg
```

### Organisation des fichiers
```
/config/www/art/
├── family/       # Photos de famille
├── nature/       # Paysages
├── abstract/     # Art abstrait
└── seasonal/     # Photos saisonnières
    ├── spring/
    ├── summer/
    ├── autumn/
    └── winter/
```

### Backup automatique
```yaml
shell_command:
  backup_art_list: >
    ls /config/www/art/ > /config/backups/art_list_$(date +%Y%m%d).txt
```

## 🔗 Liens utiles

- [Repo original](https://github.com/ollo69/ha-samsungtv-smart)
- [API Art Mode](https://github.com/NickWaterton/samsung-tv-ws-api)
- [Forum Home Assistant](https://community.home-assistant.io/)

## 🆘 Besoin d'aide ?

1. Vérifier les logs: `Configuration → Logs`
2. Rechercher dans les Issues GitHub
3. Consulter le forum Home Assistant
4. Lire la documentation complète

## 🎉 Prêt à commencer !

Vous avez maintenant tout ce qu'il faut pour transformer votre Samsung Frame en une vraie galerie d'art connectée !

---

**Temps d'installation**: ~10 minutes
**Difficulté**: Facile
**Prérequis**: Home Assistant + Samsung The Frame TV

**Bon art mode ! 🖼️**
