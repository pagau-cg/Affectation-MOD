# Déploiement

## Publier le pilote — GitHub Pages

Le dépôt peut rester **privé** ; seul le site publié est public.

1. `Settings` → `Pages`
2. **Source** : `Deploy from a branch`
3. **Branch** : `main`, dossier `/ (root)` → `Save`

Une à deux minutes plus tard, l'outil est à :

```
https://pagau-cg.github.io/Affectation-MOD/
```

Aucune configuration de plus : GitHub sert `index.html` tel quel. Chaque
poussée sur `main` republie automatiquement.

### Mettre à jour

Remplacer `index.html`, pousser. Demander à Max et Keven de recharger avec
`Ctrl + Maj + R` (ou `Cmd + Maj + R`) la première fois — le navigateur garde
l'ancienne version en cache une dizaine de minutes autrement.

## La limite à connaître

**Un site GitHub Pages privé exige GitHub Enterprise Cloud.** Avec un compte
personnel ou une organisation Team, un dépôt privé peut quand même publier un
site, mais ce site est public : n'importe qui ayant l'adresse l'ouvre. L'adresse
n'est pas indexée, mais elle est devinable.

Conséquence pratique : **tant que l'outil est sur Pages, ses données restent
fictives.** Pas de vrais noms de charpentiers, pas de vrais numéros de
téléphone, pas de vrais budgets d'heures. Pour un pilote qui valide le
comportement et la présentation, c'est suffisant.

## Passer en hébergement privé

Le jour où l'outil doit contenir de vraies données, avant la migration Somtech,
l'option la plus directe utilise ce que CG a déjà.

### Azure Static Web Apps + Entra ID

CG a déjà Microsoft 365, donc déjà Entra ID. Le plan gratuit d'Azure Static Web
Apps suffit pour un fichier statique.

1. Portail Azure → `Static Web Apps` → `Créer`
2. Source : ce dépôt GitHub, branche `main`
3. `App location` : `/` · `Api location` : vide · `Output location` : vide
4. Une fois créée : `Authentification` → fournisseur **Microsoft Entra ID**
5. Ajouter `staticwebapp.config.json` à la racine :

```json
{
  "routes": [
    { "route": "/*", "allowedRoles": ["authenticated"] }
  ],
  "responseOverrides": {
    "401": { "redirect": "/.auth/login/aad", "statusCode": 302 }
  }
}
```

Résultat : seuls les comptes `@constructiongauthier.com` ouvrent le site. Azure
crée lui-même le workflow GitHub Actions qui redéploie à chaque poussée.

À valider avec qui gère le tenant Microsoft 365 avant de créer la ressource.

### Autres options

| Option | Privé | Effort | Note |
|---|---|---|---|
| Azure Static Web Apps + Entra ID | oui | moyen | Réutilise les comptes M365 existants |
| SharePoint — bibliothèque de documents | oui | faible | Le fichier s'ouvre mal depuis SharePoint ; à tester |
| Fichier distribué par courriel | oui | nul | Chacun sa copie, versions divergentes |
| GitHub Pages | **non** | nul | Convient au pilote à données fictives |

## Vérification automatique

`.github/workflows/verif.yml` exécute `outils/verifier.mjs` à chaque poussée et
à chaque pull request. Il refuse le changement si l'outil :

- cesse d'être un fichier autonome
- ajoute un appel réseau ou du stockage navigateur
- charge une bibliothèque externe
- câble un bouton sur une fonction qui n'existe pas
- ne s'analyse plus

Ce dernier point n'est pas théorique : pendant le développement, la suppression
d'une fonction a cassé la moitié des boutons sans aucun message d'erreur. La
vérification existe pour ça.

## Avant de faire confiance à l'outil pour de vrai

- [ ] Hébergement privé en place
- [ ] Indisponibilités modélisées (vacances, maladie, CNESST, formation)
- [ ] Persistance décidée et bâtie
- [ ] Liste des charpentiers branchée sur Ma Place RH
- [ ] Projets et budgets d'heures branchés sur Ops
- [ ] Heures faites branchées sur Maestro
- [ ] Calendrier CCQ confirmé pour l'année à venir sur ccq.org
