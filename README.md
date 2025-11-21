# Samsung TV Smart with Art Mode - Intégration Home Assistant

[![Version](https://img.shields.io/badge/version-2.0.0-blue.svg)](https://github.com/ollo69/ha-samsungtv-smart)
[![License](https://img.shields.io/badge/license-LGPL--3.0-green.svg)](LICENSE)

Intégration personnalisée Home Assistant pour les TV Samsung avec support complet de l'**Art Mode** pour les téléviseurs Samsung The Frame.

Basée sur [ollo69/ha-samsungtv-smart](https://github.com/ollo69/ha-samsungtv-smart) avec l'ajout de l'API Art Mode de [NickWaterton/samsung-tv-ws-api](https://github.com/NickWaterton/samsung-tv-ws-api).

## 🎨 Fonctionnalités Art Mode

### Entités ajoutées

- **Switch Art Mode** (`switch.samsungtv_art_mode`)
  - Active/désactive le mode Art
  - État ON quand la TV est en mode galerie d'art

- **Select Artwork** (`select.samsungtv_art_*`)
  - Sélection d'œuvres d'art par catégorie :
    - My Photos (vos photos téléchargées)
    - Favourites (vos favoris)
    - Samsung Collection (collection Samsung)

### Services disponibles

#### `samsungtv_smart.art_upload`
Télécharge une image vers le mode Art.

```yaml
service: samsungtv_smart.art_upload
target:
  entity_id: media_player.samsung_tv
data:
  image_path: "/config/www/art/my_photo.jpg"
  matte_type: "modern"
  matte_color: "warm"
  file_type: "JPEG"
```

**Paramètres:**
- `image_path` (requis): Chemin local vers l'image
- `matte_type` (optionnel): Type de cadre
  - Options: `none`, `modernthin`, `modern`, `modernwide`, `flexible`, `shadowbox`, `panoramic`, `triptych`, `mix`, `squares`
- `matte_color` (optionnel): Couleur du cadre
  - Options: `black`, `neutral`, `antique`, `warm`, `polar`, `sand`, `seafoam`, `sage`, `burgundy`, `navy`, `apricot`, `byzantine`, `lavender`, `redorange`, `skyblue`, `turquoise`
- `file_type` (optionnel, défaut: PNG): `PNG` ou `JPEG`

#### `samsungtv_smart.art_delete`
Supprime une œuvre d'art.

```yaml
service: samsungtv_smart.art_delete
target:
  entity_id: media_player.samsung_tv
data:
  content_id: "MY_F0001"
```

#### `samsungtv_smart.art_delete_multiple`
Supprime plusieurs œuvres d'art.

```yaml
service: samsungtv_smart.art_delete_multiple
target:
  entity_id: media_player.samsung_tv
data:
  content_ids:
    - "MY_F0001"
    - "MY_F0002"
    - "MY_F0003"
```

#### `samsungtv_smart.art_set_brightness`
Règle la luminosité du mode Art.

```yaml
service: samsungtv_smart.art_set_brightness
target:
  entity_id: media_player.samsung_tv
data:
  brightness: 75  # 0-100
```

#### `samsungtv_smart.art_slideshow`
Configure le diaporama Art Mode.

```yaml
service: samsungtv_smart.art_slideshow
target:
  entity_id: media_player.samsung_tv
data:
  interval: 30  # minutes
  category: "MY-C0002"  # My Photos
  shuffle: true
```

**Catégories disponibles:**
- `MY-C0002`: My Photos
- `MY-C0004`: Favourites
- `MY-C0003`: Samsung Collection

#### `samsungtv_smart.art_select_image`
Sélectionne une œuvre spécifique.

```yaml
service: samsungtv_smart.art_select_image
target:
  entity_id: media_player.samsung_tv
data:
  content_id: "SAM-F0206"
  show_now: true
```

#### `samsungtv_smart.art_set_filter`
Applique un filtre photo.

```yaml
service: samsungtv_smart.art_set_filter
target:
  entity_id: media_player.samsung_tv
data:
  content_id: "MY_F0001"
  filter_name: "ink"
```

#### `samsungtv_smart.art_get_thumbnail`
Récupère la miniature d'une œuvre.

```yaml
service: samsungtv_smart.art_get_thumbnail
target:
  entity_id: media_player.samsung_tv
data:
  content_id: "SAM-F0206"
  save_path: "/config/www/thumbnails/artwork.jpg"
```

## 📦 Installation

### Via HACS (recommandé)

1. Ouvrir HACS dans Home Assistant
2. Aller dans "Integrations"
3. Cliquer sur les 3 points en haut à droite
4. Sélectionner "Custom repositories"
5. Ajouter l'URL du repository
6. Catégorie: Integration
7. Installer "Samsung TV Smart with Art Mode"
8. Redémarrer Home Assistant

### Installation manuelle

1. Télécharger le code source
2. Copier le dossier `custom_components/samsungtv_smart` dans votre dossier Home Assistant
3. Structure finale:
   ```
   config/
   └── custom_components/
       └── samsungtv_smart/
           ├── __init__.py
           ├── manifest.json
           ├── const.py
           ├── switch.py
           ├── select.py
           ├── services.yaml
           ├── art_services.py
           └── api/
               ├── samsungws.py
               └── artmode.py
   ```
4. Redémarrer Home Assistant

## ⚙️ Configuration

### Configuration via l'interface utilisateur

1. Aller dans Paramètres → Appareils et services
2. Cliquer sur "Ajouter une intégration"
3. Rechercher "Samsung TV Smart"
4. Entrer l'adresse IP de votre TV Samsung
5. **Important**: Accepter la connexion sur l'écran de votre TV quand le popup apparaît
6. L'intégration détectera automatiquement si l'Art Mode est supporté

### Prérequis

- Home Assistant 2023.1 ou supérieur
- TV Samsung The Frame (2016+) avec support Art Mode
- Connexion réseau (même VLAN) entre Home Assistant et la TV
- IP statique recommandée pour la TV

### TV compatibles

L'Art Mode est supporté sur les modèles Samsung "The Frame":
- QN series (2016+)
- LS03 series
- Tous les modèles "The Frame" récents

## 🚀 Exemples d'utilisation

### Activation automatique du mode Art à l'extinction

```yaml
automation:
  - alias: "Active Art Mode quand TV s'éteint"
    trigger:
      - platform: state
        entity_id: media_player.samsung_tv
        to: "off"
    action:
      - service: switch.turn_on
        target:
          entity_id: switch.samsung_tv_art_mode
```

### Diaporama quotidien

```yaml
automation:
  - alias: "Diaporama photos quotidien"
    trigger:
      - platform: time
        at: "07:00:00"
    action:
      - service: samsungtv_smart.art_slideshow
        target:
          entity_id: media_player.samsung_tv
        data:
          interval: 60  # Change toutes les heures
          category: "MY-C0002"
          shuffle: true
```

### Luminosité selon le soleil

```yaml
automation:
  - alias: "Luminosité Art Mode - Jour"
    trigger:
      - platform: sun
        event: sunrise
    action:
      - service: samsungtv_smart.art_set_brightness
        target:
          entity_id: media_player.samsung_tv
        data:
          brightness: 80

  - alias: "Luminosité Art Mode - Nuit"
    trigger:
      - platform: sun
        event: sunset
    action:
      - service: samsungtv_smart.art_set_brightness
        target:
          entity_id: media_player.samsung_tv
        data:
          brightness: 30
```

Plus d'exemples disponibles dans [automations_examples.md](automations_examples.md).

## 🎨 Interface Lovelace

### Carte de contrôle Art Mode

```yaml
type: vertical-stack
cards:
  - type: entities
    title: Samsung Frame - Art Mode
    entities:
      - entity: switch.samsung_tv_art_mode
        name: Mode Art
      - entity: select.samsung_tv_art_my_photos
        name: Mes Photos
      - entity: select.samsung_tv_art_favourites
        name: Favoris

  - type: horizontal-stack
    cards:
      - type: button
        name: Jour
        icon: mdi:brightness-7
        tap_action:
          action: call-service
          service: samsungtv_smart.art_set_brightness
          service_data:
            entity_id: media_player.samsung_tv
            brightness: 90

      - type: button
        name: Nuit
        icon: mdi:brightness-4
        tap_action:
          action: call-service
          service: samsungtv_smart.art_set_brightness
          service_data:
            entity_id: media_player.samsung_tv
            brightness: 30
```

## 🔧 Dépannage

### L'Art Mode n'est pas détecté

1. Vérifier que votre TV est bien un modèle "The Frame"
2. Vérifier que la TV et Home Assistant sont sur le même VLAN
3. Redémarrer la TV (pour fermer les apps qui bloquent la connexion WebSocket)
4. Vérifier les logs: `Configuration → Logs`

### Impossible de télécharger des images

1. Vérifier que le chemin de l'image est accessible depuis Home Assistant
2. Vérifier les permissions du fichier
3. S'assurer que l'image est au format PNG ou JPEG
4. Taille recommandée: 3840x2160 pixels (4K)

### La connexion WebSocket échoue

1. Vérifier que la TV et HA sont sur le même réseau
2. Désactiver temporairement le pare-feu
3. Utiliser une IP statique pour la TV
4. Port par défaut: 8002

### Les services ne fonctionnent pas

1. Vérifier que l'intégration est correctement chargée
2. Vérifier dans `Developer Tools → Services` que les services sont disponibles
3. Redémarrer Home Assistant après l'installation

## 📝 Notes importantes

- **Format d'image**: PNG et JPEG supportés. JPEG recommandé pour la taille.
- **Résolution**: 3840x2160 recommandé (4K). Les images seront redimensionnées automatiquement.
- **Stockage TV**: Capacité limitée, pensez à supprimer les anciennes images.
- **Content ID**: Les images téléchargées commencent par `MY_F****`, les images Samsung par `SAM-****`.

## 🤝 Contributions

Les contributions sont les bienvenues ! N'hésitez pas à :
- Signaler des bugs
- Proposer de nouvelles fonctionnalités
- Soumettre des pull requests
- Améliorer la documentation

## 📜 Licence

LGPL-3.0 License

## 🙏 Remerciements

- [ollo69](https://github.com/ollo69) - Pour l'intégration Samsung TV Smart originale
- [NickWaterton](https://github.com/NickWaterton) - Pour l'API Art Mode
- [xchwarze](https://github.com/xchwarze) - Pour la base de l'API Samsung TV WebSocket

## 💡 Support

Pour obtenir de l'aide :
1. Consulter la [documentation](https://github.com/ollo69/ha-samsungtv-smart)
2. Ouvrir une [issue](https://github.com/ollo69/ha-samsungtv-smart/issues)
3. Consulter les [discussions](https://github.com/ollo69/ha-samsungtv-smart/discussions)

---

**Note**: Cette intégration nécessite une TV Samsung The Frame pour utiliser les fonctionnalités Art Mode. Les autres fonctionnalités de base fonctionnent avec toutes les TV Samsung Tizen (2016+).
