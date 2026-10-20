# Affectation des forces propres — Construction Gauthier

Outil de planification des charpentiers et des contremaîtres sur les chantiers,
jour par jour. **Prototype** : il sert de référence de comportement avant la
reconstruction dans Somtech.

> **Données fictives seulement.** Les noms, téléphones, budgets et heures du
> fichier sont inventés. Le site est publié sur GitHub Pages, donc **public** —
> n'y mettez jamais la vraie liste de Ma Place RH ni de vrais budgets. Voir
> [DEPLOIEMENT.md](DEPLOIEMENT.md) pour passer en hébergement privé le jour où
> ce sera nécessaire.

## Ce que l'outil fait

Projets empilés verticalement, jours à l'horizontale. On pense « qui est sur ce
chantier », pas « où est cette personne ».

- **Trois vues** — Jour (AM / PM), Planning (1 à 12 semaines), Mois (capacité).
- **Affectation par période** — début et fin, ou début et durée, avec choix des
  jours de semaine et de la plage. Un aperçu annonce les jours-homme, les jours
  fériés sautés et les affectations qui seront écrasées.
- **Glisser-déposer** — souris et tactile, du panneau vers une case ou d'une
  case à l'autre.
- **Équipes** — monter une équipe, désigner son chef, l'affecter en bloc.
- **Contremaîtres** — affectation datée ; la date de fin devient la **date de
  libération**. Vue d'ensemble triée par disponibilité.
- **Location de main-d'œuvre** — N hommes d'un sous-traitant entre deux dates,
  comptés en jours-homme et en heures.
- **Heures par projet** — budget, faites à ce jour, affectées à venir, total, et
  l'écart. Avec la part de sous-traitance.
- **Calendrier CCQ** — fériés chômés et congés annuels 2026 à 2028, colorés et
  exclus des affectations.
- **Transmission** — résumé de la période par charpentier, par SMS ou Somtech
  (bouchon dans le prototype).

## Contraintes de conception

Elles ne sont pas décoratives : la vérification automatique les fait respecter à
chaque poussée.

| Contrainte | Pourquoi |
|---|---|
| **Un seul fichier** `index.html` | Se copie, s'ouvre, se courriel sans installation |
| **Aucun appel réseau** | Les données ne quittent jamais le poste |
| **Aucun stockage navigateur** | L'outil ne laisse rien derrière lui |
| **Aucune bibliothèque externe** | Rien à mettre à jour, rien qui casse à distance |

Seule exception : la police Barlow chargée depuis Google Fonts. Sans réseau, le
navigateur retombe sur une police système et tout fonctionne.

## Sources de données — un champ, une source

| Donnée | Source maîtresse |
|---|---|
| Charpentiers, contremaîtres | Ma Place RH |
| Projets, budgets d'heures | Ops (Somtech) |
| Heures faites à ce jour | Maestro |
| Affectations | Cet outil |

Aucune de ces données ne doit être ressaisie à la main dans la version finale.

## Ce qui manque encore

1. **Les indisponibilités** — vacances individuelles, maladie, CNESST,
   formation. Tant qu'elles n'y sont pas, « libre dès le 2 nov » peut désigner
   quelqu'un qui est en congé ce jour-là, et le taux d'affectation surestime la
   capacité réelle. C'est le manque le plus structurant.
2. **La persistance** — tout est en mémoire ; un rechargement efface le travail.
   Volontaire pour le pilote, qui sert à valider le comportement, pas à
   planifier pour vrai.
3. **Les permissions** — qui affecte, qui lit. À câbler sur les rôles Somtech.
4. **L'historique** — comparer le planifié au réel (heures Maestro) demande de
   conserver les affectations passées. Choix de schéma, à faire dès le départ.

## Fichiers

```
index.html                     l'outil
GUIDE-TEST.md                  pour Max et Keven
DEPLOIEMENT.md                 publication et hébergement privé
outils/verifier.mjs            vérification des invariants
.github/workflows/verif.yml    la même, à chaque poussée
```

## Vérifier avant de pousser

```
node outils/verifier.mjs
```

## Lien avec la planification MOD

Cet outil et `planif-mod` répondent à deux questions successives.

| | Question | Granularité | Horizon |
|---|---|---|---|
| **planif-mod** | Combien d'hommes ce projet demande-t-il, et quand ? | semaine-homme, par activité | 6–18 mois |
| **Affectation** | Qui, précisément, et quels jours ? | demi-journée, par personne | 1–12 semaines |

La sortie du premier est l'entrée du second. À la migration vers Somtech, ils
doivent au minimum partager la liste des projets et le calendrier.
