<div align="center">

<h1>🔧 Ducratif AutoFix</h1>

<p><strong>Détecteur d'environnement · Validateur · Compat Layer · Dashboard Web</strong></p>

<p>
  <img src="https://img.shields.io/badge/Version-1.0.0-brightgreen?style=flat-square" alt="Version">
  <img src="https://img.shields.io/badge/FiveM-FXServer-blue?style=flat-square" alt="FiveM">
  <img src="https://img.shields.io/badge/Lua-5.4-orange?style=flat-square" alt="Lua 5.4">
  <img src="https://img.shields.io/badge/License-MIT-lightgrey?style=flat-square" alt="MIT">
</p>

<p>
  <a href="https://source.ducratif.com/project/autofix-scan-serveur"><strong>Télécharger</strong></a> ·
  <a href="https://autofix.ducratif.com/"><strong>Documentation complète</strong></a> ·
  <a href="https://github.com/Ducratif"><strong>GitHub</strong></a> ·
  <a href="https://ducratif.com/devis"><strong>Devis personnalisé</strong></a>
</p>

</div>

---

## Présentation

**Ducratif AutoFix** est un script FiveM entièrement gratuit conçu pour les administrateurs de serveurs ESX Legacy. Il diagnostique automatiquement votre environnement serveur, valide vos dépendances et expose une API unifiée compatible multi-framework.

### Ce que fait le script

| Fonctionnalité | Description |
|---|---|
| **Auto-détection** | Framework, MySQL, inventaire, ox_lib, OneSync, build FXServer |
| **Checklist** | Validateur PASS / WARN / FAIL avec suggestions de correction |
| **Compat Layer** | Exports universels pour ESX & QB (argent, items, callbacks, notifications) |
| **Rapports** | Génération HTML + JSON après chaque scan, avec historique et diff |
| **Dashboard Web** | Interface accessible depuis n'importe quel navigateur |
| **Scanner tiers** | Vérification de vos propres scripts (exports, fichiers, convars, tables SQL) |
| **Notifications Discord** | Webhook avec filtre par niveau et anti-spam |
| **Mise à jour auto** | Détection des nouvelles versions du script et du FXServer |

---

## Stack supportée

| Composant | Support |
|---|---|
| ESX Legacy (`es_extended`) | Natif complet |
| QBCore (`qb-core`) | Détection + compat layer |
| oxmysql | Natif (SQL checks, diagnostics DB) |
| mysql-async / ghmattimysql | Détecté |
| ox_lib | Notifications + callbacks |
| ox_inventory | AddItem / RemoveItem / HasItem |
| qs-inventory | AddItem / RemoveItem / HasItem |
| qb-inventory | AddItem / RemoveItem / HasItem |
| ESX inventory (fallback) | Intégré |

---

## Installation

### 1. Déposer la ressource

```
resources/
  [ducratif]/
    ducratif_autofix/
```

### 2. Configurer `server.cfg`

```cfg
# Charger APRÈS es_extended, oxmysql et ox_lib
ensure ducratif_autofix

# Donner l'accès admin aux commandes /autofix*
add_ace group.admin ducratif.autofix allow

# (Optionnel) Webhook Discord via convar plutôt que config.lua
set autofix_webhook "https://discord.com/api/webhooks/..."
```

### 3. Configurer `config.lua`

Ouvrez `config.lua` et adaptez les paramètres à votre environnement (voir section [Configuration](#configuration) ci-dessous).

### 4. Lancer le serveur

```
✅ Au démarrage, le script s'initialise et affiche un banner en console.
✅ Utilisez /autofix pour lancer votre premier scan complet.
```

---

## Configuration

### Paramètres principaux

| Clé | Défaut | Description |
|---|---|---|
| `Config.StrictMode` | `false` | Bloque les exports si un check FAIL est détecté |
| `Config.AutoScanOnStart` | `false` | Lancer le scan automatiquement au démarrage |
| `Config.AutoScanIntervalMinutes` | `0` | Intervalle de scan automatique en minutes (`0` = désactivé) |
| `Config.PreferredFramework` | `'auto'` | `'auto'` \| `'esx'` \| `'qb'` |
| `Config.PreferredInventory` | `'auto'` | `'auto'` \| `'ox_inventory'` \| `'qs-inventory'` \| etc. |
| `Config.AdminAce` | `'ducratif.autofix'` | Permission ACE requise pour les commandes |

### Ressources

```lua
Config.RequiredResources = {
    'es_extended',  -- FAIL si absente
    'oxmysql',
}
Config.RecommendedResources = {
    'ox_lib',       -- WARN si absente
    'ox_inventory',
}
```

### Rapports

```lua
Config.Report = {
    enable_json    = true,   -- Générer last_report.json
    enable_html    = true,   -- Générer last_report.html
    path           = 'reports',
    enable_history = true,   -- Conserver les anciens rapports
    max_history    = 15,     -- Nombre maximum d'entrées dans l'historique
    enable_diff    = true,   -- Afficher les différences entre deux scans
}
```

### Logging & Discord

```lua
Config.Logging = {
    console          = true,
    webhook          = '',           -- URL webhook Discord (ou convar autofix_webhook)
    webhook_cooldown = 60,           -- Anti-spam : secondes entre deux webhooks identiques
    webhook_levels   = { 'WARN', 'ERROR', 'SUCCESS' },
    file             = true,         -- Écriture dans reports/autofix_YYYY-MM-DD.log
}
```

### Dashboard HTTP

```lua
Config.Http = {
    enable      = true,
    server_ip   = 'localhost',         -- IP publique ou localhost
    help_public = true,                -- Centre d'aide accessible sans connexion
}
```

### Authentification Dashboard

```lua
Config.Auth = {
    discord_client_id     = '',   -- App ID Discord pour OAuth2
    discord_client_secret = '',   -- Client Secret Discord
    session_ttl_minutes   = 120,  -- Durée de session
    token                 = '',   -- Accès direct par token (sans Discord)
}
```

> Pour Discord OAuth2 : créez une application sur [discord.com/developers](https://discord.com/developers/applications), activez **Requires OAuth2 Code Grant** et ajoutez le redirect `http://VOTRE_IP:PORT/ducratif_autofix/callback`.

### Vérification de version FXServer

```lua
Config.ServerVersion = {
    check_latest = true,  -- Vérifie la dernière version Windows/Linux disponible
}
```

### Diagnostics base de données

```lua
Config.DB = {
    AutoDiagnose        = true,
    RetryAttempts       = 3,
    RetryDelay          = 5000,
    PrintFixSuggestions = true,
    PrintConfigHelp     = true,
    TestOnStart         = true,
}
```

---

## Commandes admin

> Accessible en console serveur (source 0) ou en jeu avec la permission ACE `ducratif.autofix`.

| Commande | Description |
|---|---|
| `/autofix` | Lance un scan complet et génère les rapports |
| `/autofix scan` | Alias du scan complet |
| `/autofix scan [resource]` | Scan d'un script spécifique |
| `/autofix report` | Résumé du dernier rapport en console/chat |
| `/autofix env` | Affiche l'environnement détecté (framework, MySQL, inventaire…) |
| `/autofix checks` | Liste tous les checks avec leur résultat |
| `/autofix db` | Lance le diagnostic de la base de données |
| `/autofix strict on\|off` | Active ou désactive le mode strict |
| `/autofix interval [minutes]` | Modifie l'intervalle de scan automatique |
| `/autofix help` | Affiche l'aide |

---

## Dashboard Web

Une fois `Config.Http.enable = true`, le dashboard est accessible depuis n'importe quel navigateur :

```
http://VOTRE_IP:PORT/ducratif_autofix/
```

### Pages disponibles

| Endpoint | Description |
|---|---|
| `/` | Rapport HTML complet du dernier scan |
| `/api/status` | Données JSON du dernier scan |
| `/api/history` | Liste JSON de l'historique des scans |
| `/report/N` | Rapport HTML d'un slot de l'historique |

### Permissions du panel

| Permission | Action |
|---|---|
| `panel.view` | Accéder au panel |
| `panel.scan` | Lancer un scan |
| `panel.reports` | Voir les rapports |
| `panel.resources` | Voir les ressources |
| `panel.resources.manage` | Démarrer / Arrêter / Redémarrer des ressources |
| `panel.players` | Voir les joueurs connectés |
| `panel.players.kick` | Kicker des joueurs |
| `panel.servercfg.read` | Lire le `server.cfg` |
| `panel.servercfg.edit` | Modifier le `server.cfg` |
| `panel.scanners` | Voir les scanners personnalisés |
| `panel.scanners.manage` | Gérer les scanners personnalisés |
| `panel.users` | Voir les utilisateurs & rôles |
| `panel.users.manage` | Gérer les utilisateurs & rôles |
| `panel.help` | Accéder au centre d'aide |

---

## Scanner de scripts tiers

Vous pouvez configurer le scan de vos propres ressources dans `config.lua` via `Config.ScriptsToScan`. Des vérifications peuvent être effectuées sur :

- **État de la ressource** (démarrée ou non)
- **Fichiers** (présence dans la ressource)
- **Exports** (accessibilité)
- **Convars** (définis dans `server.cfg`)
- **Tables SQL** (existence en base de données)

```lua
Config.ScriptsToScan = {
    {
        resource    = 'mon_garage',
        label       = 'Mon Garage',
        required    = true,
        description = 'Gestion des véhicules',
        checks = {
            exports    = { 'openGarage', 'getVehicles' },
            files      = { 'config.lua', 'client/main.lua', 'server/main.lua' },
            convars    = {},
            sql_tables = { 'owned_vehicles' },
        },
        help = 'Assurez-vous que mon_garage est démarré APRÈS es_extended.',
    },
}
```

> Les scanners peuvent aussi être créés et gérés directement depuis le dashboard web.

---

## Exports (Compat Layer)

Utilisez les exports d'AutoFix dans vos scripts pour une compatibilité multi-framework automatique.

### Depuis un script serveur

```lua
-- Informations sur l'environnement
local fw  = exports['ducratif_autofix']:GetFramework()   -- 'esx' | 'qb' | 'unknown'
local env = exports['ducratif_autofix']:GetEnv()

-- Notifications (ox_lib si présent, sinon ESX)
exports['ducratif_autofix']:Notify(source, 'Titre', 'Message', 'success', 5000)
-- types : 'success' | 'error' | 'inform' | 'warning'

-- Argent (ESX & QB, comptes : 'money' | 'bank' | 'black_money')
exports['ducratif_autofix']:AddMoney(source, 500, 'money')
exports['ducratif_autofix']:RemoveMoney(source, 100, 'bank')
local solde = exports['ducratif_autofix']:GetMoney(source, 'money')

-- Inventaire (ox_inventory, qs-inventory, qb-inventory, ESX)
exports['ducratif_autofix']:AddItem(source, 'bread', 2)
exports['ducratif_autofix']:RemoveItem(source, 'bread', 1)
local possede = exports['ducratif_autofix']:HasItem(source, 'bread', 1)

-- Wrapper joueur
local player = exports['ducratif_autofix']:GetPlayer(source)
-- player.identifier, player.name, player.job, player.jobGrade

-- Callbacks (ox_lib si présent, sinon ESX)
exports['ducratif_autofix']:RegisterCallback('monScript:getData', function(source, cb, arg)
    cb({ data = 'réponse' })
end)

-- Logs structurés
exports['ducratif_autofix']:Log('INFO', 'Mon message', { extra = 'data' })

-- État du script
local pret   = exports['ducratif_autofix']:IsReady()
local checks = exports['ducratif_autofix']:GetChecks()
local resume = exports['ducratif_autofix']:GetCheckSummary()
local diff   = exports['ducratif_autofix']:GetDiff()
local dbOk   = exports['ducratif_autofix']:IsDBConnected()
local histo  = exports['ducratif_autofix']:GetHistory()

-- Lancer un scan depuis votre code
exports['ducratif_autofix']:RunScan()
exports['ducratif_autofix']:RunScanOne('nom_ressource')
```

### Événement déclenché après chaque scan

```lua
AddEventHandler('ducratif_autofix:scanComplete', function(summary, env)
    print('PASS:', summary.pass, '| WARN:', summary.warn, '| FAIL:', summary.fail)
end)
```

---

## Arborescence

```
ducratif_autofix/
├── fxmanifest.lua
├── config.lua
├── client/
│   └── main.lua
├── server/
│   ├── main.lua          ← Point d'entrée, commandes, exports, HTTP handler
│   ├── detector.lua      ← Auto-détection d'environnement
│   ├── validator.lua     ← Moteur de checks (PASS/WARN/FAIL)
│   ├── compat.lua        ← API unifiée multi-framework
│   ├── scanner.lua       ← Scanner de scripts tiers
│   ├── report.lua        ← Génération rapports HTML/JSON + historique
│   ├── panel.lua         ← Dashboard HTTP + API REST
│   ├── auth.lua          ← Authentification Discord OAuth2 + rôles
│   ├── dbcheck.lua       ← Diagnostics base de données
│   ├── logger.lua        ← Console + webhook Discord + fichier log
│   ├── utils.lua         ← Utilitaires internes
│   ├── master_data.lua   ← Données de référence
│   └── help_data.lua     ← Contenu du centre d'aide
├── reports/              ← Rapports générés (HTML, JSON, logs)
│   └── .gitkeep
├── web/
│   └── report_template.html
├── README.md
└── LICENSE
```

---

## Résolution de problèmes

**ESX non détecté / export `getSharedObject()` indisponible**
→ Assurez-vous que `es_extended` est démarré **avant** `ducratif_autofix` dans `server.cfg`.

**Permission refusée sur `/autofix`**
→ Ajoutez dans `server.cfg` :
```cfg
add_ace group.admin ducratif.autofix allow
```

**Rapports non générés**
→ Vérifiez que le dossier `reports/` existe (le fichier `.gitkeep` doit être présent) et que FXServer a les droits d'écriture.

**Webhook Discord silencieux**
→ Vérifiez l'URL dans `Config.Logging.webhook` ou le convar `autofix_webhook`. Vérifiez aussi `webhook_cooldown` et `webhook_levels`.

**Le dashboard HTTP n'est pas accessible**
→ Vérifiez `Config.Http.enable = true`, que `server_ip` correspond à votre IP publique, et que le port FiveM est ouvert.

**La base de données n'est pas connectée**
→ Lancez `/autofix db` pour obtenir un diagnostic détaillé avec suggestions de correction.

---

## Liens

| Ressource | Lien |
|---|---|
| Télécharger / Nouvelle version | [source.ducratif.com](https://source.ducratif.com/project/autofix-scan-serveur) |
| Documentation complète | [autofix.ducratif.com](https://autofix.ducratif.com/) |
| GitHub | [github.com/Ducratif](https://github.com/Ducratif) |
| Serveur Discord | Disponible dans le script |
| Devis service personnalisé | [ducratif.com/devis](https://ducratif.com/devis) |

---

## Licence

MIT — Voir [LICENSE](LICENSE)

---

<div align="center">
<sub>Développé avec soin par <strong>Ducratif</strong> · Script 100% gratuit</sub>
</div>
