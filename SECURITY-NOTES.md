Badge createur verifie : oui, "Verified creator" visible sur la page Marketplace
Nombre d'etoiles : 5k
Dossier du code source : src

Q3. Pourquoi faut-il épingler une version (@v4) plutôt que @main ?
Utiliser @main signifie que l'action utilisera toujours la toute dernière version du code, qui peut changer à tout moment. Cela peut casser le workflow ou introduire une faille de sécurité sans prévenir. Épingler une version (ex: @v4) garantit que le code exécuté est stable et validé.

Q4. Le badge « créateur vérifié » garantit-il qu'une action est sûre ? Justifiez.
Non. Il garantit seulement que l'identité du créateur (ex: GitHub, AWS) a été vérifiée par GitHub. Cela n'empêche pas le créateur de faire une erreur, d'introduire une faille ou de compromettre son propre compte. Il faut toujours vérifier le code de l'action.

Q5. Citez deux vérifications à faire avant d'utiliser, en production, une action d'un fournisseur inconnu.
1. Lire le code source de l'action (notamment le fichier action.yml et les scripts associés) pour vérifier qu'elle ne fait pas d'actions malveillantes.
2. Épingler l'action non pas à un tag, mais à un commit SHA précis, pour s'assurer que le code ne changera jamais sous nos pieds.