# NioticBlock — Pack « Olympus : L'Odyssée des Dieux »

Pack de configuration pour le serveur Minecraft **Workase** (OuiHeberg, Paper 26.2).
Il prolonge le contenu existant de **WorkaseCore** et **MythicMobs** sur le thème de la mythologie grecque.

> ⚠️ **Rien de ce pack n'a encore été testé en jeu.** Il a été écrit à partir des fichiers lus sur le serveur,
> mais la clé IA OuiPanel est en lecture seule : aucun fichier n'a pu être posé ni aucun démarrage lancé.
> Toute la syntaxe YAML est valide, et toutes les références croisées (boss → compétences → butins → objets) se résolvent.

---

## 1. Installation

Le dossier `serveur/` reproduit l'arborescence de la racine du serveur.

1. **Faire une sauvegarde** depuis OuiPanel (« Sauvegardes » → « Créer »), puis vérifier qu'elle est marquée *réussie*.
   La seule sauvegarde valide actuelle date du 14/09/2026.
2. Copier le contenu de `serveur/plugins/` dans `/plugins/` du serveur (SFTP ou gestionnaire de fichiers).
3. Redémarrer le serveur, puis lire `logs/latest.log` en cherchant `MythicMobs` et `WorkaseCore`.
4. Test rapide en console : `mm mobs spawn Workase_Medusa 1 world,0,100,0`

### Fichiers qui REMPLACENT un fichier existant
| Fichier | Changement |
|---|---|
| `WorkaseCore/bosses/minotaur.yml` | Ajoute `mythic_mob_id: Workase_Minotaur` (le boss devient invocable) |
| `WorkaseCore/reputation/factions.yml` | Les 8 entrées d'origine sont conservées à l'identique ; 14 entrées sont ajoutées |

Tous les autres fichiers sont **nouveaux** et n'écrasent rien. `labyrinth_minotaur.yml` et `medusa.yml` ne sont pas modifiés.

---

## 2. Contenu

### MythicMobs
- **6 boss** avec barre de boss, compétences et butin : Méduse, Minotaure, Polyphème, Cerbère, Hydre de Lerne, Typhon (boss final).
  `Workase_Medusa` était déjà référencé par WorkaseCore, mais il n'existait pas. Il est maintenant défini.
- **7 créatures** : `kosmos_rat` (la cible de la quête d'accueil, qui n'existait pas non plus), serpents, satyres, hoplites déchus, harpies, ombres d'Hadès, sanglier d'Érymanthe.
- **9 tables de butin** et **16 objets** : ambroisie, nectar, trophées de boss, armes légendaires (Foudre de Zeus, Casque d'Hadès, Arc de Persée…).

### WorkaseCore (même schéma que les fichiers existants)
- **4 dieux** ajoutés : Héra, Déméter, Dionysos, Hestia. Les 12 Olympiens et Hadès sont désormais tous présents.
- **5 boss** ajoutés au registre, chacun relié à son mob MythicMobs.
- **5 donjons** : Antre de la Gorgone, Grotte de Polyphème, Marais de Lerne, Portes des Enfers, Mont Etna.
- **11 quêtes** en 6 actes, de Kosmos jusqu'à Typhon, plus une quête de chasse.
- **7 objets divins** (registre).
- **Réputation** : cités de Thèbes, Argos et Delphes, région des Enfers, et tous les dieux.

### Progression prévue
| Acte | Contenu | Boss |
|---|---|---|
| 1 | Kosmos, temple, hoplites | — |
| 2 | Harpies, sanglier d'Érymanthe | — |
| 3 | Gorgone, Labyrinthe | Méduse, Minotaure |
| 4 | Île des Cyclopes, Lerne | Polyphème, Hydre |
| 5 | Descente aux Enfers | Cerbère |
| 6 | Mont Etna | Typhon |

---

## 3. Ce qui reste à faire (à la main ou dans WorkaseCore)

**Points à vérifier dans le code de WorkaseCore.** Son code source n'est pas dans ce dépôt, donc je n'ai pas pu le lire.
- **Coordonnées des donjons** : ce sont des valeurs provisoires (`0.5 / 100 / 0.5`), comme l'indiquait déjà le fichier d'origine. À remplacer par les vraies.
- **Enchaînement des quêtes** : les quêtes des actes 1 à 6 sont en `auto_start: false`. Le schéma ne montre aucun champ de prérequis. Il faut donc vérifier comment WorkaseCore les démarre (PNJ, commande…).
- **Valeurs de `difficulty`** : seules `hard` (vue dans le fichier d'origine) et `medium` sont utilisées. Vérifier que l'énumération les accepte.
- **Cibles `kill`** : les quêtes visent les identifiants MythicMobs, sur le modèle de `kosmos_rat`. Vérifier que WorkaseCore compare bien l'identifiant MythicMobs.

**Nettoyage recommandé** (constaté le 07/10/2026) :
- **Doublons** : EssentialsX en même temps que HuskHomes et Spawn pour les homes, TP et spawn ; deux économies déclarées à Vault (Essentials et WorkaseDrachmes). En garder un seul par fonction.
- Retirer **PlugManX** : le rechargement de plugins à chaud est déconseillé en production.
- `LagFixer-1.7.1.jar` est à la racine, donc **jamais chargé**. Le déplacer dans `/plugins` ou le supprimer.
- `worldguard-bukkit-7.0.19 (1).jar` : vérifier qu'il n'existe pas de second exemplaire de WorldGuard.
- Désactiver le mode **debug** d'EconomyShopGUI.
- Supprimer les exemples MythicMobs (`ExampleMobs.yml`, `ExampleSkills.yml`, les dialogues `QuestOffer` et `CharacterSetup`), qui produisent des avertissements au démarrage.
- `CoreProtect-CE-24.0.jar.disabled` : à réactiver si une version compatible 26.2 existe (journal anti-grief).

**`server.properties`** : à modifier à la main. Ne jamais commiter ce fichier : il contient un secret.
- `difficulty=easy` → `normal` (plus cohérent avec un serveur RPG à boss).
- `view-distance=10` → `8` et `simulation-distance=10` → `6` (4 Gio de RAM pour 23 plugins).

**Plugins à envisager** (à installer à la main, **compatibilité 26.2 à vérifier avant**) :
- **Chunky** : pré-génération du monde, gros gain de performance.
- Protection de terrain pour les joueurs (GriefPrevention ou équivalent).
- Métiers et compétences (AuraSkills ou Jobs Reborn), à relier aux Drachmes.
- Votes (NuVotifier et un plugin de récompenses).

Chaque plugin ajouté pèse sur 4 Gio de RAM : mesurer avec **spark** (déjà installé) avant et après.

---

## 4. Pour que Claude applique tout lui-même

Sur OuiPanel, dans `/compte` → clé IA, cocher : **sauvegardes**, **fichiers (écriture)**, **gestion des mods/plugins**, **versions**, **alimentation**, **console**.
Le panel vérifie les droits à chaque appel : redemander à Claude devrait suffire, sans recréer la clé.
