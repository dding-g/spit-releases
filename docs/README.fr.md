# spit

[English](../README.md) · [한국어](README.ko.md) · [日本語](README.ja.md) · [简体中文](README.zh-CN.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Português](README.pt.md)

Une petite application résidente pour Windows et macOS qui enregistre rapidement des notes d’une ligne en Markdown local. L’éditeur épuré reste préchargé ; l’écriture des fichiers se fait en arrière-plan. Le guide anglais fait référence.

Le dépôt du code source est privé. Le [dépôt public de distribution](https://github.com/dding-g/spit-releases) contient uniquement les fichiers officiels d’installation, les mises à jour et les guides d’utilisation.

## Installation et utilisation

Téléchargez une archive publiée pour votre système et processeur depuis [Releases](https://github.com/dding-g/spit-releases/releases), puis décompressez-la. [Site web](https://spit-ashy.vercel.app). Sur macOS 12 ou ultérieur, copiez `spit.app` dans Applications. Sur Windows, lancez `spit.exe` depuis le dossier choisi ; Microsoft WebView2 Runtime est requis. Aucun installateur ni démarrage automatique de session. macOS utilise une signature ad-hoc, **sans Developer ID ni notarisation** ; Windows n’a pas de signature Authenticode. Gatekeeper ou SmartScreen peuvent avertir ou bloquer l’exécution. N’ouvrez que des fichiers de confiance.

Choisissez `macos-arm64` pour Apple silicon ou `macos-amd64` pour Intel ; sur Windows, `windows-amd64` pour x64 ou `windows-arm64` pour ARM64. **L’exécution, la mise à jour et le redémarrage n’ont pas été vérifiés sur Windows** ; la compilation croisée ne valide pas l’exécution.

Au premier lancement, spit détecte les coffres Obsidian enregistrés sans parcourir récursivement le disque et choisit le plus récemment utilisé. À défaut, choisissez un dossier dans les paramètres. Tout dossier existant convient ; Obsidian n’est pas obligatoire.

- `Cmd + \` sur macOS / `Ctrl + \` sur Windows : afficher ou masquer l’éditeur rapide.
- `Entrée` : masquer immédiatement et envoyer la note à l’enregistrement en arrière-plan. Pendant une composition IME, Entrée n’envoie pas la note.
- `Échap` : masquer et conserver le brouillon non envoyé **pour la session en cours uniquement**. Un arrêt et relancement ordinaire ne le restaure pas ; seul le redémarrage explicite de mise à jour ci-dessous fait exception.
- Un collage multiligne est ramené à une ligne. Les notes vides sont ignorées ; limite de 10 000 unités de code UTF-16.
- Un clic gauche sur l’icône de la zone de notification/barre des menus ouvre ou ferme **l’historique en lecture seule**, affichant aujourd’hui par défaut. Sélectionnez toute date enregistrée, passez à la date enregistrée précédente/suivante ou actualisez. Ni modification, suppression, recherche, ni widget permanent de bureau ou du système.
- Le menu de l’icône ouvre aussi l’historique, Nouvelle note et les paramètres de dossier et de langue. Utilisez Nouvelle note si le raccourci global ne peut pas être enregistré. Fermer une fenêtre laisse l’application active ; choisissez Quitter pour l’arrêter.

La langue peut être **détectée automatiquement à partir du système/navigateur** ou choisie manuellement et mémorisée : anglais, coréen, japonais, chinois simplifié, espagnol, français, allemand ou portugais. Le choix manuel est prioritaire ; les langues non prises en charge et les traductions manquantes utilisent l’anglais. Le brouillon actuel est conservé lors d’un changement de langue.

## Mises à jour et confidentialité

spit recherche les mises à jour sur GitHub Releases et les télécharge automatiquement en arrière-plan. Les paramètres affichent l’état, les erreurs, la recherche manuelle et le redémarrage lorsque tout est prêt. **L’installation et le redémarrage exigent un clic sur Redémarrer** ; aucune interruption automatique de saisie. Le redémarrage attend les enregistrements en cours et la persistance de la file, et est refusé pendant une composition IME. Le brouillon non envoyé est conservé pour être restauré après la mise à jour, sans rendre persistants les brouillons des sessions ordinaires.

Avant l’installation, SHA-256 et la signature Ed25519 sont vérifiés avec la clé publique intégrée à l’application ; tout échec bloque l’installation. Cette signature est distincte de la signature du système, de la notarisation Apple et de la confiance d’éditeur Windows. Les recherches et téléchargements contactent GitHub et ses hôtes de téléchargement, sans envoyer notes ni fichiers du coffre. La synchronisation du coffre dépend des autres logiciels.

## Fichiers et récupération

Choisir `chosenRoot` écrit dans `chosenRoot/admo/YYYY-MM-DD.md`. Un dossier déjà nommé `admo` est utilisé directement, sans `admo/admo`. Les notes quotidiennes déjà présentes à la racine ne sont pas déplacées. La date et l’heure locales de l’envoi restent celles de la note, même si l’écriture est différée :

```markdown
- 09:12:04 Acheter du lait <!-- admo:UUID -->
```

Le commentaire HTML masqué identifie la note pour éviter les doublons lors d’une récupération ; il reste visible dans la source. Le texte est échappé, pas interprété en Markdown/HTML, et le contenu existant est préservé. L’historique lit les fichiers datés du dossier effectif `admo`, pas l’ensemble du coffre, et reconnaît les anciens marqueurs pris en charge.

Les notes envoyées sont d’abord conservées dans la file locale `state.json`, puis ajoutées et synchronisées au fichier Markdown avant leur retrait de la file. En cas d’échec, elles restent en attente, une erreur apparaît dans les paramètres et sur l’icône, et une nouvelle tentative a lieu toutes les **5 secondes**, ainsi qu’au redémarrage. Si la file locale elle-même ne peut pas être enregistrée, le texte revient dans le brouillon.

Les notes en attente gardent leur destination initiale ; changer de dossier ne concerne que les nouvelles notes. Le traitement est ordonné : une destination indisponible bloque les suivantes. Rétablissez le dossier ou les droits d’accès pour permettre la reprise. Un état corrompu **n’est pas réinitialisé automatiquement** : préservez le fichier indiqué par l’erreur avant de le réparer.

État local, distinct du dossier Markdown :

- macOS : `~/Library/Application Support/spit/state.json`
- Windows : `%AppData%\spit\state.json`

À l’emplacement par défaut, en l’absence d’état `spit`, l’ancien état `admo`, puis `adhd-memo`, est migré sans écraser le nouvel état existant. Le dossier Markdown et les marqueurs existants restent `admo` pour la compatibilité : aucun dossier du coffre n’est renommé, déplacé ou supprimé et les notes en attente gardent leur destination.

Notes et état sont du **texte en clair non chiffré**. Masquer l’éditeur ne prouve pas la persistance : un arrêt forcé ou une coupure avant l’écriture de la file peut perdre cette saisie. Une écriture interrompue peut laisser une ligne partielle ; la récupération la conserve et ajoute la note complète. Les pannes du système de fichiers ou du matériel et les modifications simultanées d’éditeurs ou d’outils de synchronisation ne sont pas garanties.
