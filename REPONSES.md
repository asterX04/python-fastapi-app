# TP 7 - Actions tierces et actions personnalisées

## Questions

Q1. Pourquoi actions/checkout n'a-t-il pas besoin de with alors que setup-node en a besoin ?

actions/checkout a des valeurs par defaut pour tous ses parametres : il recupere le depot courant, avec la branche et le token du workflow. setup-node, lui, ne peut pas deviner quelle version de Node.js installer, donc il faut lui donner node-version avec with.

Q2. Pourquoi le job verify ne retrouve-t-il pas report.txt sans l'artefact, alors que les deux jobs partagent le meme depot ?

Chaque job tourne sur son propre runner, une machine neuve. report.txt a ete cree sur le runner du job build et n'est pas commite dans le depot. Dans verify, le depot est seulement clone depuis GitHub, donc le fichier n'existe pas. L'artefact sert a stocker le fichier chez GitHub pour qu'un autre job puisse le recuperer.

Q6. Pourquoi actions/checkout doit-il s'executer avant l'appel d'une action locale ?

Une action locale est un dossier du depot (.github/actions/...). Au debut du job le runner est vide, le depot n'est pas encore recupere. Sans checkout, le fichier action.yml n'existe pas sur le runner et GitHub ne peut pas le trouver.

Q7. Pourquoi shell: bash est-il obligatoire sur les steps run d'une action composite ?

Dans un workflow normal, un step run a un shell par defaut. Dans une action composite il n'y en a pas, car l'action peut etre utilisee sur differents systemes. GitHub oblige donc a indiquer le shell explicitement pour chaque step run.

## Comparaison des temps

D'après les exécutions du workflow, l'action composite (Run build info action) s'exécute en 0 seconde, tandis que l'action Docker (Run docker hello action) nécessite 3 secondes.

L'action composite est instantanée car elle se contente d'exécuter directement les commandes shell sur le runner hôte. À l'inverse, l'action Docker paie un coût d'initialisation. Les 3 secondes mesurées englobent le téléchargement de l'image de base (si absente du cache), la construction de l'image via le Dockerfile et le démarrage du conteneur.
Pour ce TP, le temps de 3 secondes est un minimum. Sur un premier run ou avec une image plus lourde, ce temps serait de 20 à 60 secondes. Cela démontre que les actions Docker sont très utiles pour l'isolation et les environnements complexes, mais qu'elles ont un surcoût de performance par rapport aux actions composites pour des tâches simples.