# Les principes FAIR appliqués à la bioinformatique

L'Institut Français de Bioinformatique ([IFB](https://www.france-bioinformatique.fr/)) organise en partenariat avec l'Institut de Biologie Intégrative de la Cellule (I2BC) une formation à destination des bioinformaticiens et biostatisticiens souhaitant mettre en oeuvre les principes "FAIR" (Facile à trouver, Accessible, Interopérable, Réutilisable) dans leurs projets d'analyse et de développement. Les concepts FAIR, initialement définis dans le contexte d'ouverture des données de la recherche, seront ici adaptés pour cadrer avec un projet type de développement et/ou analyse bioinformatique/biostatistique. Ainsi, la formation n'abordera pas les aspects "FAIR" spécifiques aux données mais introduira plusieurs outils permettant d'améliorer la reproductibilité des analyses.

## Objectifs pédagogiques

A la fin de cette formation, les participants pourront mettre en oeuvre les principes de la science reproductible : encapsuler un environnement de travail, concevoir et exécuter des workflows, gérer des versions de code, passer à l’échelle sur un cluster de calcul,  gérer des environnements logiciels et assurer la traçabilité de leur analyse à l’aide de Notebooks.

## La formation organisée en deux temps

La formartion s'organise en deux temps :

- Premier et deuxième jours : formation théorique et pratique aux outils de la science reproductible dans un projet bioinformatique
- Troisième jour : atelier optionnel de travail sur le matériel pédagogique de la formation à destination de futurs formateurs  

## Prérequis

Linux, programmation shell, notions de R et Python

## Tarifs

Académique : 120 € pour 2 jours et 180 € pour 3 jours
Non-académique : 600 € pour 2 jours ou 900 € pour 3 jours

## Important

Le nombre de participants est limité à 15 personnes maximum . Il est possible d'assister uniquement aux deux premiers jours de la formation, merci de nous indiquer cette information dans le formulaire d’inscription.  

Vous devrez vous munir de votre ordinateur portable avec installation préalable de [docker](https://docs.docker.com/get-docker) pour pouvoir participer à la formation

## Organisateurs

- IFB : Hélène Chiapello, Gildas Le Corguillé, Yoursra Mahmah, Julien Seiler, Jacques van Helden
- I2BC : Thomas Denecker, Céline Hernandez, Claire Toffano-Nioche

## Programme et slides

### Jour 1

<table style="width: 100%;">
    <thead>
        <tr>
            <th>Créneaux</th>
            <th>Titre</th>
            <th>Intervenants</th>
            <th>Slides</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>9h30 - 10h00</td>
            <td>Accueil café</td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <td>10h00 - 10h30</td>
            <td>Tour de table, présentation de la formation. </td>
            <td>Hélène Chiapello & Jacques van Helden</td>
            <td><a href="#" target="_blank">Lien</a></td>
        </tr>
         <tr>
            <td>10h30-11h30</td>
            <td>Introduction à la reproductibilité</td>
            <td>Claire Toffano-Nioche</td>
            <td><a href="#" target="_blank">Lien</a></td>
        </tr>
         <tr>
            <td>11h30-12h00</td>
            <td>Encapsulation (docker) </td>
            <td>Céline Hernandez</td>
            <td><a href="#" target="_blank">Lien</a></td>
        </tr>
         <tr>
            <td>12h00-12h30></td>
            <td>TP1</td>
            <td>Céline Hernandez & Thomas Denecker</td>
            <td><a href="#" target="_blank">Lien</a></td>
        </tr>
         <tr>
            <td>12h30-13h00</td>
            <td>TP2</td>
            <td>Céline Hernandez & Thomas Denecker</td>
            <td><a href="#" target="_blank">Lien</a></td>
        </tr>
         <tr>
            <td>13h00-14h00</td>
            <td>Repas</td>
            <td></td>
            <td><a href="#" target="_blank">Lien</a></td>
        </tr>
         <tr>
            <td>14h00-14h30</td>
            <td>Workflows (snakemake)</td>
            <td>Claire Toffano-Nioche</td>
            <td><a href="#" target="_blank">Lien</a></td>
        </tr>
         <tr>
            <td>14h30-15h00</td>
            <td>TP1</td>
            <td>Claire Toffano-Nioche & Thomas Denecker</td>
            <td><a href="#" target="_blank">Lien</a></td>
        </tr>
         <tr>
            <td>15h00-15h30</td>
            <td>TP2</td>
            <td>Claire Toffano-Nioche & Thomas Denecker</td>
            <td><a href="#" target="_blank">Lien</a></td>
        </tr>
         <tr>
            <td>15h30-16h00</td>
            <td>Pause</td>
            <td></td>
            <td></td>
        </tr>
         <tr>
            <td>16h00-16h30</td>
            <td>Présentation de l’infrastructure IFB (cluster/slurm) </td>
            <td> Julien Seiler & Gildas Le Corguillé </td>
            <td><a href="#" target="_blank">Lien</a></td>
        </tr>
         <tr>
            <td>16h30-17h00</td>
            <td>TP1</td>
            <td>Julien Seiler & Gildas Le Corguillé</td>
            <td><a href="#" target="_blank">Lien</a></td>
        </tr>
         <tr>
            <td>17h00-17h30</td>
            <td>TP2</td>
            <td>Julien Seiler & Gildas Le Corguillé</td>
            <td><a href="#" target="_blank">Lien</a></td>
        </tr>
    </tbody>
</table>

### Jour 2

<table style="width: 100%;">
    <thead>
        <tr>
            <th>Créneaux</th>
            <th>Titre</th>
            <th>Intervenants</th>
            <th>Slides</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td> 09h00-09h30</td>
            <td> Gestion de l’historique</td>
            <td> Claire Toffano-Nioche</td>
            <td> <a href="#" target="_blank">Lien</a></td>
        </tr>
        <tr>
            <td> 09h30-10h00 </td>
            <td> TP1</td>
            <td> Claire Toffano-Nioche & Thomas Denecker</td>
            <td> <a href="#" target="_blank">Lien</a></td>
        </tr>
        <tr>
            <td> 10h00-10h30</td>
            <td> TP2</td>
            <td> Claire Toffano-Nioche & Thomas Denecker</td>
            <td> <a href="#" target="_blank">Lien</a></td>
        </tr>
        <tr>
            <td> 10h30-11h00</td>
            <td> Pause</td>
            <td> </td>
            <td> </td>
        </tr>
        <tr>
            <td> 11h00-11h30</td>
            <td> TP3</td>
            <td> Claire Toffano-Nioche & Thomas Denecker</td>
            <td> <a href="#" target="_blank">Lien</a></td>
        </tr>
        <tr>
            <td> 11h30-12h00</td>
            <td> Gestion des environnements logiciels</td>
            <td> Claire Toffano-Nioche</td>
            <td> <a href="#" target="_blank">Lien</a></td>
        </tr>
        <tr>
            <td> 12h00-12h30</td>
            <td> TP1</td>
            <td> Claire Toffano-Nioche & Thomas Denecker</td>
            <td> <a href="#" target="_blank">Lien</a></td>
        </tr>
        <tr>
            <td> 12h30-14h00</td>
            <td> Repas</td>
            <td> </td>
            <td> </td>
        </tr>
        <tr>
            <td> 14h00-14h30</td>
            <td> Traçabilité avec les Notebooks </td>
            <td> Céline Hernandez</td>
            <td> <a href="#" target="_blank">Lien</a></td>
        </tr>
        <tr>
            <td> 14h30-15h30</td>
            <td> TP Markdown </td>
            <td> Céline Hernandez & Thomas Denecker</td>
            <td> <a href="#" target="_blank">Lien</a></td>
        </tr>
        <tr>
            <td> 15h30-16h00</td>
            <td> Pause</td>
            <td> </td>
            <td> </td>
        </tr>
        <tr>
            <td> 16h00-17h00</td>
            <td> Partage et diffusion </td>
            <td> Céline Hernandez</td>
            <td> <a href="#" target="_blank">Lien</a></td>
        </tr>
        <tr>
            <td> 17h00-17h30</td>
            <td> Conclusion</td>
            <td> Céline Hernandez & Thomas Denecker</td>
            <td> <a href="#" target="_blank">Lien</a></td>
        </tr>
    </tbody>
</table>

### Jour 3

<table style="width: 100%;">
    <thead>
        <tr>
            <th>Créneaux</th>
            <th>Titre</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>09h00-10h00</td>
            <td>Bilan des évaluations des participants et discussions sur les points d'amélioration.</td>
        </tr>
        <tr>
            <td>10h00-12h00</td>
            <td>Travail sur le matériel de cours (diapos, exercices) </td>
        </tr>
        <tr>
            <td>12h00-13h00</td>
            <td>Pause déjeuner</td>
        </tr>
        <tr>
            <td>13h00-15h00</td>
            <td>Travail sur le matériel de cours (diapos, exercices)</td>
        </tr>
        <tr>
            <td>15h00-16h00 </td>
            <td>Restitution et conclusion</td>
        </tr>
    </tbody>
</table>

## Informations pratiques

La formation aura lieu du **lundi 31 août 2020 - 10:00 (accueil café à 9:30) au mercredi 2 septembre 2020 - 16:00 à l'[Institut des Systèmes Complexes](https://iscpif.fr), 113 rue Nationale 75013 Paris** 

**Métros** : Olympiades (L14) ou Nationale (L6)

**Station Vélib** : place Nationale.

Les déjeuners et pauses des 3 jours sont pris en charge par l'IFB. Les frais de déplacement et logement sont à la charge des participants.

**Suggestion d'hôtels**

- Hotel ibis (Paris Place d'Italie) :  25 avenue Stephen Pichon, Paris 13ÈME
- AMHotel : 96 Avenue de Choisy, Paris 13ÈME
- Green Hotel: 27-90 Rue DE Patay, Paris 13ÈME
- Timhotel (Paris Place d’Italie) : 178 Boulevard Vincent Auriol, Paris 13ÈME
- Hotel Neptune Paris : 15 rue Godefroy, Paris 13ÈME
- Ibis Styles (Paris Tolbiac Bibliothèque) : 21 rue de Tolbiac, Paris 13ÈME

## Localisation 

<iframe src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d2626.5057125355834!2d2.3625620150479514!3d48.82949177928466!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x47e672275f8e5c6b%3A0xfd9a8e9ec6d52ab6!2sInstitut%20des%20Syst%C3%A8mes%20Complexes!5e0!3m2!1sfr!2sfr!4v1598362007233!5m2!1sfr!2sfr" width="600" height="450" frameborder="0" style="border:0;" allowfullscreen="" aria-hidden="false" tabindex="0"></iframe>

## Inscription

Les inscriptions ont lieux sur le site suivant : [ici](https://ressources.france-bioinformatique.fr/fr/evenements/principes-fair-appliques-a-la-bioinformatique#map)