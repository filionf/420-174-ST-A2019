---
title: Docker
description: Introduction
---


## Ce qu'est Docker
La grande idée derrière Docker est de séparer l'application de l'infrastructure. C'est donc de pouvoir prendre une application serveur et de pouvoir la déployer facilement sur différents serveurs sans avoir à se soucier du serveur lui-même ou des composants nécessaires à installer.

## Pourquoi?
Si l'on recule un peu dans le temps, lorsqu'on avait une application serveur à déployer, il fallait déterminer les spécifications matérielles et logicielles nécessaires à son bon fonctionnement. 
Ensuite, il fallait acheter le serveur, puis finalement installer les dépendances et l'application elle-même. 
Pour chaque environnement (développement/test/staging/préprod/prod/...), il fallait faire un peu la même procédure. 
Même chose si un serveur cesse de fonctionner. 
Ce qui complique le déploiement dans ces scénarios, c'est qu'il ne faut pas avoir d'impact sur les autres applications qui peuvent exister dans l'infrastructure, c'est pour ça qu'on doit habituellement avoir un nouveau serveur.

Ensuite, c'est la virtualisation qui est devenue populaire. 
Comme les serveurs étaient rarement utilisés à leur pleine capacité, la virtualisation a permis d'épargner sur le coût des serveurs en en achetant de plus gros, mais en partageant les ressources matérielles à travers celles-ci. 
Ainsi, même si l'application partage les ressources d'un serveur, elle fonctionne quand même de façon indépendante. 
Mis à part donner la possibilité de louer des serveurs dans l'infonuagique, ça ne change toutefois pas grand-chose pour le développement des applications. 
C'est vraiment pour épargner qu'on va utiliser cette technique. Au point de vue des logiciels, ils sont considérés comme des serveurs à part entière et il faut tout de même continuer d'acheter les licences nécessaires à leur bon fonctionnement.

Et finalement vient la conteneurisation. Docker étant évidemment la plus populaire des technologies dans ce domaine.

## Comment?
La principale différence entre une machine virtuelle et un conteneur est que le conteneur n'a pas besoin de système d'exploitation complet pour pouvoir fonctionner, il va partager le noyau de l'hôte. 
Ainsi, si un seul conteneur s'exécute sur la machine virtuelle, il n'y a pas tant d'avantages au niveau des ressources. 
C'est en exécutant plusieurs conteneurs qu'on économise tout ce dont le système d'exploitation aurait besoin, donc les mémoires, l'espace disque, le CPU, les licences ... 
En plus, comme ce n'est pas un système d'exploitation complet qui doit démarrer, le démarrage de l'application est ultrarapide en comparaison.
