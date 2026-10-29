# Lab OpenTofu pour la classe : infrastructure as code avec plan, apply et state

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-tofu-lab/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-tofu-lab/actions/workflows/check.yml)

Un **lab OpenTofu** prêt à l’emploi pour enseigner l’infrastructure as code, issu de la [plateforme d’apprentissage MitsuruTech](https://mitsurutech.co.uk/fr/learning), où les établissements scolaires donnent à chaque élève ses propres machines et son terminal dans le navigateur. Chaque élève reçoit une **VM** Ubuntu avec **OpenTofu** installé et trois dossiers de fichiers au format Terraform. Il écrit un petit projet avec des **variables**, des **sorties** (outputs), **count** et **for_each**, lit un **plan** après une modification, observe le **state** et **répare un projet cassé**. Rien n’est installé sur l’ordinateur de l’élève, et OpenTofu est libre et gratuit.

## Ce que fait l’élève

Le lab laisse trois dossiers dans `~/tofu-lab` et un `README.md` dans le dossier personnel qui explique quoi faire en six parties sans donner aucune réponse :

1. `hello` : un premier `tofu init`, `tofu plan` et `tofu apply`, et le fichier d’état qui en reste.
2. `hello` : ajouter une variable, modifier la sortie et voir si un changement est une mise à jour ou un remplacement.
3. `campus` : écrire un projet à partir de rien. Trois variables avec une valeur par défaut, une ressource `terraform_data` par nom de serveur avec `for_each`, une par salle avec `count`, une ressource de synthèse qui lit les deux, et deux sorties. `tofu fmt` ne doit rien changer aux fichiers.
4. `campus` : modifier la liste des serveurs et dire, d’après le plan seul, ce qui sera détruit, créé et laissé tel quel.
5. `hello` : lire le state avec `tofu state list`, le supprimer et voir le plan vouloir tout recréer.
6. `fix` : un projet qui contient trois erreurs (une valeur du mauvais type, une variable qui n’existe pas, deux ressources qui s’attendent l’une l’autre pour toujours), trouvées avec `tofu validate`. Une fois réparé, `tofu apply -var pods=7` doit annoncer 7 pods.

Une leçon de base de la plateforme, OpenTofu Basics, se déroule aussi dans ce lab.

## Comment il est noté

Le lab comporte une **vérification**, un script qui note une VM rendue sans que l’enseignant ait à la lire. Elle s’exécute sur une copie de la machine enregistrée, après un démarrage à neuf. Elle copie les dossiers `campus` et `fix` de l’élève ailleurs, de sorte que son propre state ne compte pas, et refuse tout fournisseur (provider) ou provisioner. Elle lance `tofu validate` et `tofu fmt -check`. Pour `campus`, elle **choisit ensuite des valeurs que personne n’a vues** (un nom de site aléatoire, ses propres serveurs et salles), fait un plan puis un apply, et contrôle le state : un serveur par nom avec le site et le nom comme entrée, les salles numérotées à partir de un, une sortie `summary` qui lit une ressource au lieu d’être écrite en dur, et la sortie `server_names`. Elle retire un serveur et en ajoute deux, refait un plan et exige que seul le premier soit détruit et que seuls les deux autres soient créés. Elle applique, exige qu’il ne reste rien à changer, puis détruit. Enfin, `fix` doit passer la validation, s’appliquer avec `pods=7`, garder ses trois ressources et suivre la variable. La première ligne affichée dit ce qui ne va pas.

Ce qu’elle ne fait pas : elle n’atteint jamais un vrai cloud, puisqu’on ne peut en joindre aucun depuis le lab, et un élève qui a les droits root sur sa propre VM pourrait encore trafiquer OpenTofu lui-même. C’est une vérification pour la classe, pas une preuve.

## Ce que contient le fichier

`tofu-lab.lab.yaml` est une **définition de lab VM** complète, exactement ce que la plateforme exécute :

| Clé | Ce que c’est |
| --- | --- |
| `cpus`, `memory`, `disk` | la taille de la VM de chaque élève : 1 CPU, 1024 Mo et 10 Go |
| `open` | les ports qui s’ouvrent dans le navigateur : aucun, le lab est le terminal |
| `setup` | un script exécuté une fois, en tant que root, au premier démarrage de la VM de chaque élève : il installe quelques outils depuis l’archive d’Ubuntu, télécharge OpenTofu 1.13.2 depuis sa version publiée sur GitHub, le vérifie avec un SHA-256 fixé et l’installe, puis écrit les trois dossiers, l’outil `tofulab` (`sudo tofulab install` et `sudo tofulab reset`) et le README |
| `check` | le script de notation, exécuté en tant que root sur une copie du disque enregistré après un démarrage à neuf ; son code de sortie est le résultat et `0` signifie réussi |

## L’utiliser sur la plateforme

**Pour le donner comme travail à une classe :**

1. Dans votre module, ajoutez une tâche et choisissez la carte **Lab VM Ubuntu**.
2. Appuyez sur l’exemple OpenTofu, ou collez le contenu de `tofu-lab.lab.yaml`. Utiliser un exemple remplace le texte, et Annuler rétablit le vôtre.
3. Publiez et essayez d’abord votre propre copie. Le premier démarrage prend une minute ou deux, le temps de télécharger OpenTofu. Chaque élève a sa propre VM, conservée d’une session à l’autre.
4. Distribuez-le comme n’importe quelle tâche. Une fois rendu, lancez la vérification sur la ligne d’un élève dans la liste de la classe : elle démarre une copie de sa machine enregistrée, qui n’est pas sauvegardée en retour, et indique si elle a réussi et ce qu’elle a affiché.

La [page de ce lab](https://mitsurutech.co.uk/fr/help/lab-tofu-lab) dans le manuel, son [guide des labs VM Ubuntu](https://mitsurutech.co.uk/fr/help/labs) et sa [page des exemples de labs](https://mitsurutech.co.uk/fr/help/sample-labs) disent le reste.

Pas encore de compte pour votre établissement ? [Votre première année est gratuite](https://mitsurutech.co.uk/fr/pricing) jusqu’à 50 élèves, et [l’inscription](https://mitsurutech.co.uk/fr/signup) ne demande qu’une adresse e-mail.

## Vérifier le fichier

À chaque push, `.github/workflows/check.yml` vérifie que le fichier est un YAML valide avec les cinq clés ci-dessus et que `setup` et `check` tiennent chacun dans la limite de 16 Ko de la plateforme. Il ne démarre pas de VM : cela se fait sur les machines de la plateforme.

## Ce qui ne fonctionne pas dans un lab

La VM atteint l’archive de paquets d’Ubuntu, PyPI, npm et GitHub, et rien d’autre. C’est pourquoi **aucun fournisseur ne peut être téléchargé et rien de réel n’est jamais créé** : AWS, Docker et les autres viennent d’un registre que la VM ne peut pas joindre. Le lab utilise la seule ressource intégrée à OpenTofu, `terraform_data`, qui se contente de stocker une valeur et de la rendre ; variables, sorties, `count`, `for_each`, dépendances, plan, apply, destroy et state sont, eux, bien réels. Le README de l’élève montre le bloc de ressource d’un fournisseur comme un texte à lire, et le lab ne l’exécute jamais. OpenTofu lui-même se télécharge depuis GitHub, que la VM d’un élève n’atteint qu’avec un compte gratuit : la VM d’un simple visiteur ne peut pas l’installer, et la vérification indique alors qu’OpenTofu est absent.

## À propos

Réalisé par [Mitsuru Technologies](https://mitsurutech.co.uk/fr) pour la [plateforme d’apprentissage MitsuruTech](https://mitsurutech.co.uk/fr/learning) : Python, Java, Go, Node, SQL et Linux dans le navigateur pour les écoles et les établissements, avec des labs de conteneurs, des labs VM, des applications web déployées depuis GitHub et des quiz qui se corrigent tout seuls. Essayez du code sans compte dans le [bac à sable](https://mitsurutech.co.uk/fr/playground).

D’autres labs du même éditeur : [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea), [lab-sample](https://github.com/mitsurutech/lab-sample), [lab-sdn-lab](https://github.com/mitsurutech/lab-sdn-lab), [lab-reverse-lab](https://github.com/mitsurutech/lab-reverse-lab), [lab-formal-lab](https://github.com/mitsurutech/lab-formal-lab), [lab-hdl-lab](https://github.com/mitsurutech/lab-hdl-lab), [lab-k3s-lab](https://github.com/mitsurutech/lab-k3s-lab), [lab-ansible-roles](https://github.com/mitsurutech/lab-ansible-roles) et [lab-ansible-basics](https://github.com/mitsurutech/lab-ansible-basics).

## Licence

Cette œuvre est sous licence [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Vous pouvez la partager et l’adapter pour apprendre et enseigner, à condition de citer Mitsuru Technologies Ltd, de ne pas l’utiliser commercialement et de partager tout ce que vous en tirez sous la même licence. Le texte complet est dans le fichier `LICENSE`.

[OpenTofu](https://opentofu.org) (MPL-2.0) est téléchargé depuis la version publiée par le projet sur GitHub au démarrage d’une VM ; rien n’en est distribué ici.
