---
title: "Schéma de la carte mère"
pubDate: 2026-05-25
description: "Vue d'ensemble et explication des principaux composants d'une carte mère."
draft: false
tags:
  - hardware
  - carte-mere
  - schema
---

Conception électronique du robot tondeuse DIY : architecture, contraintes CEM et intégration modulaire

La conception électronique d’un robot mobile autonome impose un ensemble de contraintes fortes : robustesse, immunité CEM, modularité, facilité de maintenance et capacité d’évolution. Dans ce premier article, je présente l’architecture matérielle retenue pour mon robot tondeuse DIY, ainsi que les choix techniques qui ont guidé la réalisation de la carte principale et l’organisation des modules périphériques.

![Schéma de la carte mère](/assets/carte-mere.svg)

## 1. Architecture générale : migration vers une Nucleo H7 pour augmenter les capacités

Le projet a initialement débuté sur une Nucleo F401RE, suffisante pour un prototype fonctionnel mais rapidement limitée en termes :

- de puissance de calcul (Cortex‑M4 à 84 MHz)
- de nombre de périphériques disponibles
- de bande passante pour les capteurs
- de possibilités d’extension

La migration vers une Nucleo H7 (STM32H743) s’est imposée pour répondre à ces besoins :

- Cortex‑M7 cadencé jusqu’à 480 MHz
- double précision FPU
- DMA plus performant
- davantage de timers, ADC, SPI, I²C, UART
- meilleure gestion multitâche et traitement temps réel

Cette carte constitue désormais le backbone du système, orchestrant l’ensemble des modules : navigation, capteurs, drivers moteurs, sécurité et interface utilisateur.

## 2. Conception d’une carte dédiée sous KiCad : réduction du câblage et maîtrise CEM

Afin d’éviter l’enchevêtrement de câbles typique des prototypes sur breadboard, j’ai développé une carte d’intégration personnalisée sous KiCad.
Elle assure plusieurs fonctions :

- distribution propre des alimentations
- intégration des modules 5 V
- connectique standardisée pour les modules déportés
- routage optimisé pour limiter les perturbations
- fiabilité mécanique et électrique accrue

### 2.1 Modules intégrés (5 V)

Seuls les modules alimentés en 5 V sont montés directement sur la carte.
Raison : ces modules sont généralement logiques, à faible courant, et ne génèrent pas de perturbations significatives.

### 2.2 Modules déportés (12 V)

Les modules alimentés en 12 V, notamment ceux qui commutent des charges importantes (drivers moteurs, relais, convertisseurs), sont volontairement déportés.

Motivations techniques :

- limitation des perturbations électromagnétiques (CEM)
- réduction des couplages inductifs sur les pistes sensibles
- séparation claire entre puissance et logique
- meilleure dissipation thermique

Cette séparation physique est essentielle pour garantir la stabilité du magnétomètre, des capteurs analogiques et des communications numériques.

## 3. Déport du magnétomètre : impératif pour la précision de navigation

Le magnétomètre est un capteur particulièrement sensible aux champs magnétiques parasites.
Dans un robot tondeuse, les sources de perturbations sont nombreuses :

- moteurs DC ou brushless
- drivers H‑bridge
- câbles de puissance
- masses métalliques du châssis

Pour garantir une mesure fiable, le magnétomètre est déporté à 30 cm, monté en haut d’un mât.

Avantages :

- réduction drastique des perturbations magnétiques
- orientation plus stable
- meilleure précision de cap (heading)
- calibration plus simple et plus reproductible

Ce choix est courant dans les robots mobiles nécessitant une navigation précise.

## 4. Capteurs et caméra déportés : modularité et optimisation du placement

Certains capteurs ainsi que la caméra ne sont pas intégrés sur la carte principale.
Ce choix répond à plusieurs contraintes :

- positionnement optimal (hauteur, champ de vision, orientation)
- réduction des vibrations
- facilité de remplacement ou d’évolution
- gestion thermique et mécanique simplifiée

Chaque module est relié via des connecteurs dédiés, permettant une maintenance rapide et une architecture modulaire.

## 5. Interface utilisateur : écran de diagnostic intégré

Un écran de diagnostic est soudé directement sur la carte.
Il permet :

- l’affichage en temps réel de l’état du robot
- la visualisation des capteurs critiques
- le lancement d’un autotest matériel
- le debug sans ordinateur, utile en extérieur

Cet écran constitue un outil essentiel pour la mise au point et la maintenance.

## 6. Positionnement de la carte et protection mécanique

La carte principale est installée sur la partie supérieure du robot, ce qui présente plusieurs avantages :

- accès facilité pour la maintenance
- éloignement des projections d’herbe et d’humidité
- réduction des risques de condensation
- meilleure circulation de l’air

Un carénage viendra protéger l’ensemble contre la pluie, la poussière et les chocs.
Sa conception n’est pas encore finalisée, mais il devra respecter les contraintes d’étanchéité (IP54 minimum), de ventilation et d’accessibilité.

## Conclusion

Cette architecture électronique repose sur une séparation claire entre puissance et logique, une gestion rigoureuse des contraintes CEM, et une modularité pensée dès la conception.
La Nucleo H7 offre une base solide pour les futures évolutions, tandis que la carte personnalisée assure une intégration propre et fiable.

Les prochains articles détailleront :

- le schéma complet de la carte
- la gestion des moteurs et drivers
- les algorithmes de navigation
- la conception mécanique et le carénage
