# Comprendre le dépôt "Wifi-Hacking"

Ce document a pour objectif d'expliquer, pas à pas et en termes accessibles, la structure de ce dépôt et le fonctionnement général du script principal. Il constitue un point de départ pour toute personne qui découvre la cybersécurité offensive ou Python.

## 1. Vue d'ensemble du projet

Le dépôt contient essentiellement trois éléments :

- `Wifi-Hacking.py` : le script principal, écrit en Python, qui orchestre toutes les fonctionnalités de l'outil.【F:Wifi-Hacking.py†L1-L336】
- `README.md` : un aperçu rapide du projet, des fonctionnalités promises et des captures d'écran de l'interface en mode texte.【F:README.md†L1-L90】
- `Snapshots/` : un répertoire d'images statiques illustrant l'outil en action.【F:README.md†L64-L74】

Il n'y a pas de modules Python supplémentaires ni de paquets internes : toute l'application repose sur un seul fichier script et sur des outils système installés via `apt`.

## 2. Comment est structuré `Wifi-Hacking.py` ?

Le script suit une structure relativement simple :

1. **Importations et installation des dépendances** : dès l'exécution, le script tente d'installer plusieurs utilitaires (aircrack-ng, crunch, reaver, etc.) via `apt-get`. Cette étape est réalisée à l'aide de `os.system` et suppose que l'utilisateur dispose des droits administrateur.【F:Wifi-Hacking.py†L1-L11】
2. **Fonction `intro()`** : cette fonction affiche le menu principal en ASCII art, recueille le choix de l'utilisateur et appelle la branche de code correspondante. C'est le cœur de l'application, car elle est rappelée en boucle après chaque action pour maintenir l'interaction.【F:Wifi-Hacking.py†L12-L58】【F:Wifi-Hacking.py†L302-L336】
3. **Branches fonctionnelles** : chaque option du menu principal déclenche une série de commandes système orientées vers l'audit Wi-Fi :
   - Gestion du mode moniteur (options 1 et 2) via `airmon-ng` et `network-manager` pour activer/désactiver l'écoute passive.【F:Wifi-Hacking.py†L24-L44】
   - Scan des réseaux et capture de handshakes (options 3 et 4) avec `airodump-ng` et `aireplay-ng` afin d'observer ou de forcer les échanges WPA/WPA2.【F:Wifi-Hacking.py†L45-L104】
   - Installation de suites d'outils supplémentaires (option 5) en proposant une liste d'applications de pentest Wi-Fi que l'utilisateur peut installer individuellement ou en bloc.【F:Wifi-Hacking.py†L105-L224】
   - Tentatives de cassage de mots de passe Wi-Fi (options 6 à 8) s'appuyant sur `aircrack-ng` et, pour la génération de dictionnaires, `crunch`. Ces branches nécessitent préalablement d'avoir capturé un handshake.【F:Wifi-Hacking.py†L225-L308】
   - Génération de listes de mots de passe personnalisées (option 9) au format brut avec `crunch` pour une utilisation ultérieure.【F:Wifi-Hacking.py†L309-L318】
   - Attaques et scans WPS (options 10 et 11) en mobilisant `reaver`, `bully`, `wifite` et `airodump-ng` pour cibler les points d'accès vulnérables au protocole WPS.【F:Wifi-Hacking.py†L319-L335】

L'application n'utilise pas de structures de données avancées ni de classes : tout est géré par des conditions `if/elif` au sein de la fonction `intro()`.

## 3. Points importants à connaître

- **Permissions nécessaires** : la majorité des commandes exécutées requièrent des privilèges super-utilisateur (root). Le script lui-même suppose que vous le lancez avec `sudo`.
- **Dépendance forte aux outils système** : le code Python sert principalement de surcouche interactive à une collection d'utilitaires en ligne de commande (`aircrack-ng`, `airodump-ng`, `reaver`, `crunch`, etc.). Comprendre l'effet réel d'une option revient donc à connaître l'outil appelé.
- **Absence de validations avancées** : les entrées utilisateurs sont directement injectées dans des commandes shell. Il faut saisir des interfaces, chemins de fichiers ou paramètres valides pour éviter les erreurs d'exécution.
- **Risques légaux** : comme le rappelle le README, ces techniques sont à utiliser uniquement dans un cadre légal (laboratoire personnel, tests autorisés). Toute utilisation sur un réseau sans permission est illégale.【F:README.md†L96-L104】

## 4. Repères pour progresser

Pour approfondir votre compréhension et apprendre de façon méthodique :

1. **Se familiariser avec les outils sous-jacents** : avant même de modifier le script, lisez la documentation officielle de `airmon-ng`, `airodump-ng`, `aireplay-ng`, `aircrack-ng`, `reaver`, `bully`, `wifite` et `crunch`. L'objectif est de comprendre ce que fait chaque commande appelée et dans quel contexte elle est pertinente.
2. **Isoler les fonctionnalités** : lancez le script dans une machine virtuelle dédiée, puis testez chaque option une par une. Prenez des notes sur les entrées requises (interface, BSSID, canal, chemins de fichiers) et sur les résultats attendus.
3. **Écrire vos propres wrappers** : utilisez ce projet comme exemple pour écrire de petits scripts Python qui automatisent des tâches similaires (par exemple, un script qui vérifie si `airmon-ng` est installé ou qui valide les interfaces disponibles). Cela vous aidera à pratiquer la manipulation de subprocess en Python.
4. **Renforcer la sécurité du code** : à mesure que vous progressez, essayez d'améliorer le script : ajouter des validations d'entrée, gérer les erreurs, ou migrer vers le module `subprocess.run` pour un contrôle plus précis.
5. **Culture cybersécurité** : complétez votre apprentissage par des lectures sur les protocoles Wi-Fi (WEP, WPA/WPA2, WPS) et les méthodologies de test d'intrusion. Comprendre la théorie vous permettra de contextualiser chaque commande.

## 5. Ressources complémentaires

- Le README d'origine reste une bonne carte postale du projet, mais il date et n'explique pas tous les prérequis. Utilisez ce guide comme support de démarrage et créez votre propre journal de bord.
- Recherchez des tutoriels de laboratoire Wi-Fi éthique (par exemple sur des distributions comme Kali Linux ou Parrot OS) pour voir ces outils en contexte pratique.

En résumé, `Wifi-Hacking.py` est un grand menu automatisant des commandes d'audit Wi-Fi. Pour un néophyte, l'essentiel est de comprendre les briques externes que le script assemble et de pratiquer dans un environnement maîtrisé.
