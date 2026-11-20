# TP 6 - Variables d'environnement, Inputs et Outputs

## Questions

Q1. Pourquoi la variable n'est-elle pas disponible dans la step qui l'a definie, mais seulement dans les suivantes ?

GITHUB_ENV est un fichier que le runner lit quand la step est terminee. La step en cours a deja ete lancee avec son environnement, donc elle ne voit pas la nouvelle variable. Les steps suivantes demarrent apres la lecture du fichier, donc elles l'ont.

Q2. Que constate-t-on dans verify-env ? Quelle est la portee de GITHUB_ENV ?

Dans verify-env, $APP_NAME est vide. Chaque job tourne sur un runner different et le fichier GITHUB_ENV reste sur le runner du job qui l'a ecrit. Donc GITHUB_ENV ne marche que pour les steps suivantes du meme job.

Q3. Que se passe-t-il avec > au lieu de >> dans GITHUB_OUTPUT si deux outputs sont definis dans la meme step ?

Le symbole > efface le fichier a chaque ecriture alors que >> ajoute a la fin. Avec deux outputs dans la meme step, la deuxieme ligne efface la premiere, donc il ne reste que le dernier output et le premier est vide. Avec un seul output > marche quand meme car le fichier est vide au depart, mais des qu'il y en a plusieurs il faut utiliser >>.

Q4. Pourquoi GITHUB_ENV ne suffit-il pas ici, alors que GITHUB_OUTPUT fonctionne entre jobs ?

Chaque job tourne sur sa propre machine, donc le fichier GITHUB_ENV n'est jamais transmis a un autre job. Les outputs sont envoyes a GitHub, declares avec outputs: au niveau du job, et les autres jobs les recuperent avec needs.<job>.outputs.

Q5. Que se passe-t-il si on retire needs: build du job deploy ?

Sans needs: build, deploy ne depend plus de build, donc les deux jobs partent en meme temps. needs.build n'existe plus pour deploy, donc ${{ needs.build.outputs.full_version }} est vide. needs sert donc a ordonner les jobs et aussi a recuperer leurs outputs.