# Tutoriel Github Actions : Pipeline CI/CD

C'est un projet qui permet d'automatiser la création de base de données, des tests et de déployer des versions de ton projet sur Vercel.

Je te conseille de visionner cette vidéo si tu ne l'a pas déjà fait : [![Tutoriel Github Actions : Pipeline CI/CD](https://www.media.codympia.com/wp-content/uploads/2024/07/tutoriel-github-actions-pipeline-ci-cd.webp)](https://youtu.be/lPwSfw6bB_U)

## En quoi consiste cette pipeline ?

> Une pipeline CI/CD est une série d'étapes à réaliser en vue de distribuer une nouvelle version d'un logiciel.

C’est clairement une automatisation qui fait gagner du temps. Voici la suite d’actions qu’on va vouloir faire quand on sort une nouvelle version de notre SaaS par exemple :

- Build
- Tests (unitaires, e2e pour être sûr qu’on a rien cassé)
- Merge sur la branche main
- Déploiement automatique en production

[](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fd9853ffe-0450-4c13-80cf-8f5c8e0491e3_1418x743.png)

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fd9853ffe-0450-4c13-80cf-8f5c8e0491e3_1418x743.png)

> En entreprise, c’est souvent les devops qui s’occupent de toute cette partie.

**Problème 1** : Toi tu n’est pas en entreprise. Tu es solo pour sortir ton projet donc il te faut un truc simple.

**Problème 2** : Le système classique en entreprise a un autre problème (qu’on va résoudre).

### Les environnements

Dans 90% des entreprises tu as : un environnement de dev, un de staging et un de production.

**Le dev** : c’est un environnement local pour les dev (avec une base de données de dev).

**Staging** : c’est un environnement pour que les non-techs puissent tester les features sur une URL (sans devoir démarrer le projet en local sur un terminal)

**Production** : c’est l’environnement accessible publiquement par tous les utilisateurs.

#### Problématique

Tu as plusieurs devs dans une entreprise. Quand un dev sort une feature, elle est envoyée seule en staging afin de pouvoir la tester de manière isolée. Et enfin elle est envoyée en prod.

[](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fca3d0bfc-e76d-44a2-aeb5-43424330c733_1219x739.png)

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fca3d0bfc-e76d-44a2-aeb5-43424330c733_1219x739.png)

Le problème c’est que chaque dev est obligé d’attendre pour tester sa feature en staging (car c’est mieux de tester feature par feature).

### Solution

On va donc créer non pas un env de `staging` mais un env de `previews`.

[](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fe60f8efa-8d43-4062-ab07-e66ac3d4ca41_1170x731.png)

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fe60f8efa-8d43-4062-ab07-e66ac3d4ca41_1170x731.png)

Chaque dev peut envoyer sa review en preview et plusieurs testeurs peuvent tester indépendamment chaque feature. Mais ça introduit un nouveau problème.

#### Nouvelle problématique

Le truc c’est que si on a une base de données pour chaque environnement, on a PAS une base de données pour chaque preview.

Donc si on change le schéma de la base de données pour un preview, ça casse tous les autres previews.

Exemple simple : on change l’âge de l’utilisateur et à la place on met sa date de naissance).

[](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F9b6453a7-2012-40be-bdc8-20bde0ebf97a_707x845.png)

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F9b6453a7-2012-40be-bdc8-20bde0ebf97a_707x845.png)

Donc ça change la colonne âge dans la base de données pour la changer en birthday.

Donc tous les previews qui utilisent _âge_ vont être cassés car ce champ n’existe plus dans la base de données.

#### Nouvelle solution

Chaque preview et chaque dev a sa propre base de données.

[](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fe61955cb-db45-4da6-a4fc-c335a74a4c2d_953x857.png)

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fe61955cb-db45-4da6-a4fc-c335a74a4c2d_953x857.png)

> “Mais attend mais ça va être l’enfer pour créer ça pour chaque dev et pour chaque nouveau preview”.

Et c’est là que la pipeline CI/CD et le choix des bons outils entrent en jeu 😎 Une fois que c’est installé (3 fichiers que je te donne avec les explications) tout est fait automatiquement.

**Tu auras un meilleur système que 90% des entreprises.**
