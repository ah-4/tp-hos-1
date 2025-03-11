# TP3 : Moar isolation

**Dans ce TP on va approfondir des techniques d'isolation de processus.** Plus couramment appelé "sandboxing" de processus, ou d'applications/

Un système, ce n'est que des fichiers (sur stockage) et des processus (en mémoire). Le CPU exécutant les instructions des processus en cours d'exécution.

On continue donc à se concentrer sur les interactions entre processus, fichiers et OS dans ce TP, avec un zoom sur certains mécanismes spécifiquement liés au sandboxing.

**Au menuuuu du jour :**

- exploration de Linux avec `/proc`, `/dev` et `/sys`
- (re)visite de `chroot`
- cgroups : pour restreindre l'accès aux ressources
- namespaces : isolation de processus
- hardening de service systemd avec tout ce qui a été appris

> Ca va au passage vous permettre de comprendre comment fonctionne les conteneurs sous Linux (avec Docker par exemple) sous le capot.

![afraid to ask](./img/ask.png)

## Prérequis

➜ **Une seule VM Rocky**

- une seule fera le taff, on se concentre sur le fonctionnement de l'OS en lui-même aujourd'hui encore

## Suite du TP

➜ [**Part I** : A bit of exploration](./part1.md)

➜ [**Part II** : Gotta get chrooty](./part2.md)

➜ [**Part III** : CGroup](./part3.md)

➜ [**Part IV** : Namespaces](./part4.md)

➜ [**Part V** : Harden me baby](./part5.md)
