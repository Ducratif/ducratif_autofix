# DOCUMENTATION: https://autofix.ducratif.com/


## 📖 Présentation
Ducratif AutoFix est un script FiveM open-source conçu pour diagnostiquer automatiquement votre environnement serveur, détecter les incompatibilités, valider vos configurations et vous alerter en temps réel via un dashboard web sécurisé.

C'est un script FiveM 100% GRATUIT compatible esx | esx_legacy | qbcore développper par Ducratif en 2026.

`Il agit comme un filtre de qualité permanent : à chaque scan, il vérifie que votre framework, votre base de données, vos scripts et vos ressources sont correctement configurés et fonctionnels.`

## ℹ️ Fonctionnement général
* AutoFix s'installe une seule fois et tourne en arrière-plan. Vous pouvez lancer des scans manuels (/autofix) ou automatiques à intervalle défini. Chaque scan produit un rapport HTML + JSON consultable depuis un navigateur.
* Ce que fait AutoFix
* Détecte automatiquement le framework (ESX Legacy, QB-Core)
* Détecte le driver MySQL (oxmysql, mysql-async, ghmattimysql)
* Détecte l'inventaire (ox_inventory, qs-inventory, qb-inventory, esx)
* Vérifie la version FXServer (build actuel vs dernier disponible)
* Valide la connexion à la base de données
* Exécute une liste de checks configurables (PASS / WARN / FAIL)
* Génère des rapports HTML et JSON avec historique
* Expose un dashboard web sécurisé (Discord OAuth2)
* Permet de gérer les ressources (start/stop/restart)
* Permet de gérer les joueurs connectés (voir, kick)
* Permet d'éditer le server.cfg depuis le navigateur
* Notifie via webhook Discord les anomalies détectées
* Propose un système de rôles et permissions granulaire
