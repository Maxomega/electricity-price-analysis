# Rapport

## Organisation

Avoir une seule histoire à raconter et pas plusieurs petits projets séparés, autour des prix day-ahead de l'électricité France-Allemagne en 2025.

Dataset: ENTSO-E 12.1.D fournit les prix day-ahead par bidding zone et market time unit, organisé en fichiers csv mensuels, téléchargement sur https://transparency.entsoe.eu/fileLibrary/fileBrowserMode?appState=%7B%22path%22%3A%22%2FTP_export%2FEnergyPrices_12.1.D_r3%2F%22%7D

Angle : dépendance, extrêmes et clustering temporel des tensions sur les prix day-ahead FR-DE en 2025

Structure du code : description et nettoyage, structure temporelle, extrêmes, dépendance par copules, clustering d'événements par Hawkes

Question simple : quand le marché devient tendu, les prix français et allemands deviennent-ils plus dépendants et les épisodes extrêmes se propagent-ils dans le temps et entre pays ? (On fait pas juste un catalogue de méthodes, je suis ouvert au feedback sur la question et la structure, c'est juste une première proposition)

Plan du rapport
- 1 page: intro, question éco/stat, source des données, lien ENTSO-E, description du dataset
- 2 pages: préparation des données, conversion UTC, filtrage FR/DE, construction des variables, premiers grpahiques de description
- 2 à 3 pages: partie séries temporelles de Max, saisonnalités, horaire/hebdomadaire, autocorr, éventuellement un travail sur prix variations ou résidus
- 2 à 3 pages: Thaïs partie extrêmes, négatifs, pics, quantiles élevés, dépassements de seuils, comparaison FR/DE, toute la partie qui estime la distribution des extrêmes (théorie du cours)
- 3 pages : partie copule, estimation, comparaison Gaussian / Gumbel / Clayton, dépendance globale et dépendance de queue
- 2 à 3 pagees : Hawkes, définition des événements, estimation univariée puis bivariée, interprétation de l'autoexcitation et excitation croisée
- 0.5 page: conclusion commune, limites, ouverture

Il ne faut pas séparer artificiellement les parties, les data clean doivent être les mêmes utilisées par tout le monde, la partie Hawkes doit utiliser les seuils motivés par Thais (code à rerun après car on fait dans l'ordre chronologique inverse), mémoire statistique plutôt que juste juxtaposition de techniques

Discours
- Les marges ont été étudiées avant la dépendance
- Les seuils Hawkes ne sont pas arbitraires
- Les copules s'appliquent à des variables mieux préparées
- Hawkes sert à étudier le clustering temporel que les copules ne capturent pas

Visuels, privilégier figures statiques et très lisibles
- Courbes hroires FR et DE sur quelques périodes choisies
- Heatmap jour x heur des prix
- Distribution et zoom sur les queues
- Pseudo-observations pour les copules
- Contours/diagonstics de copules
- raster des evts Hawkes
- intensités estimées
- tableau final qui relie les résultats des 4 méthodes...

## Partie copule

- Définir deux variables X_t, Y_t résidus ou variations horaires FR/DE
- Représenter Sklar, séparation marges / dépendance
- Construction des pseudo_observations ^U_t = ^F_X(X_t), ^V_t = ^F_Y(Y_t)
- Calcul d'un Kendall et Spearman
- Estimer les trois familles vues : gaussienne, Gumbel, Clayton
- Deux estimations, inversion de tau puis maximum de vraisemblance
- Comparer les log vraisemblances, AIC/BIC, interprétation des queues
- Rappel: copule gaussienne (cours) bonne base de comparaison mais pas de dépendance de queue, gumbel pertinente si les hausses extrêmes se produisent ensemble, clayton pertinent si les prix très bas ou négatifs sont fortement conjoints
- Pas "quelle copule gagne" mais dans quel régime elle gagne, donc on peut faire des analyses différentes en fonction de régime calme vs tendu, jours où le spread ou la volatilité dépasse un seuil

Visuels
- Nuage de point FR-DE sur données brutes et pseudo-observations
- Empirical copula heatmap
- Contours des copules ajustées
- Graphiques des queues, par exemple concentration des observations dans les coins
- Tableau comparatif ^tau, ^rho, paramètre estimé, log-vraisemblance, lambda_u, lambda_l

Structure rapport
- Statistiques de rang
- Construction des pseudo-observations
- Estimation paramétrique
- Dépendance de queue
- Interprétation économique

## Partie Hawkes

Eviter de modéliser les prix comme un processus ponctuel, il faut regarder des événements, donc transformer la série horaire en suite d'horodatages pour événements du type : P_t < 0, P_t > q_95 ou q_99, |Delta P_t| > q_95, spread FR-DE anormalement grand

- Hawkes sur evts extrêmes en France
- Même chose en Allemagne
- Hawkes bivarié FR/DE
- Interpréter les mu, alpha, beta de l'estimation et branching ratio
- En dimension 2, ommenter l'auto-excitation et l'excitation croisée

Visuels
- Timeline / raster plot des évts sur une année
- Zoom sur une semaine tendue avec evts FR et DE
- Intensité estimée lambda_t superposée aux evts
- Schéma matrice d'excitation Hawkes bivarié
- Décomposition immigrants/enfants si possible

