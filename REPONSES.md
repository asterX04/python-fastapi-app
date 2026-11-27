# TP 8 – Stratégies GitHub Actions

## Q1. Combien de jobs sont générés ? Donnez la liste des combinaisons.

Avec la matrice de base configurée dans `tp-strategies.yml`, **6 jobs** sont générés. 
Le calcul est simple : 2 systèmes d'exploitation (`ubuntu-latest`, `windows-latest`) × 3 versions de Python (`"3.11"`, `"3.12"`, `"3.13"`) = **6 combinaisons**.

Voici la liste exhaustive des combinaisons générées :
1. `ubuntu-latest` + `"3.11"`
2. `ubuntu-latest` + `"3.12"`
3. `ubuntu-latest` + `"3.13"`
4. `windows-latest` + `"3.11"`
5. `windows-latest` + `"3.12"`
6. `windows-latest` + `"3.13"`

## Q2. Combien de jobs au total ? Rappel : exclude s'applique avant include.

Il y a **6 jobs** au total. 
Voici le détail du calcul :
1. La matrice de base génère 6 combinaisons (2 OS × 3 versions de Python).
2. **exclude** supprime 1 combinaison (`windows-latest` + `3.11`). Il reste donc 5 combinaisons.
3. **include** ajoute 1 nouvelle combinaison (`ubuntu-latest` + `3.14`).
Total : 6 - 1 + 1 = **6 jobs**.

La liste finale des jobs exécutés est donc :
- `ubuntu-latest` + `3.11`
- `ubuntu-latest` + `3.12`
- `ubuntu-latest` + `3.13`
- `windows-latest` + `3.12`
- `windows-latest` + `3.13`
- `ubuntu-latest` + `3.14` (avec `experimental: true`)

## Q3. Que se passerait-il si vous tentiez d'exclure la combinaison 3.14 ? Pourquoi ?

Cela **ne fonctionnerait pas** : la combinaison `3.14` serait quand même générée et ajoutée aux jobs.
**Pourquoi ?** Parce que le mot-clé `exclude` ne s'applique **que sur la matrice de base** (les combinaisons générées par les listes `os` et `python-version`). La version `"3.14"` n'existe pas dans la liste de base `["3.11", "3.12", "3.13"]`. 
De plus, l'ordre d'exécution de GitHub Actions est strict : la matrice de base est générée, puis `exclude` est appliqué, et enfin `include` est ajouté. Au moment où `exclude` s'exécute, la combinaison `3.14` n'existe pas encore, il est donc impossible de l'exclure de cette façon.


## Q4. Quelle est la différence observée ? Dans quel cas choisiriez-vous true ? false ?

**Différence observée :**
- Avec **`fail-fast: true`** (valeur par défaut) : Dès qu'un job de la matrice échoue, GitHub Actions annule immédiatement tous les autres jobs en cours ou en attente. Cela se traduit visuellement par des jobs qui deviennent gris (annulés).
- Avec **`fail-fast: false`** : GitHub Actions continue d'exécuter tous les jobs de la matrice jusqu'au bout, même si certains échouent. On obtient ainsi un rapport complet (tous les jobs vont au bout, ceux qui échouent sont rouges, ceux qui réussissent sont verts).

**Dans quel cas choisir `true` ?**
On choisit `true` lorsque l'on veut un retour rapide (feedback loop) pour économiser du temps et des ressources (minutes de CI/CD). Si un échec sur un OS signifie que le code est fondamentalement cassé, il est inutile de tester les autres combinaisons.

**Dans quel cas choisir `false` ?**
On choisit `false` lorsqu'on a besoin d'un rapport exhaustif, par exemple pour des tests de compatibilité. On veut savoir si le code échoue *uniquement* sur Windows ou *uniquement* sur Python 3.11, sans avoir à relancer le pipeline plusieurs fois.

## Q5. Donnez deux raisons d'utiliser max-parallel en entreprise.

1. **Éviter la surcharge des infrastructures ou des runners (limites de concurrence)** : 
   Dans une entreprise, on utilise souvent des *self-hosted runners* (des serveurs internes) qui ont une capacité limitée. Si une matrice génère 50 jobs d'un coup, cela peut saturer les serveurs et bloquer les autres équipes. `max-parallel` permet de lisser la charge.

2. **Contourner les limites de quotas d'API tierces (Rate Limiting)** : 
   Si vos tests doivent interagir avec des services externes (comme télécharger des dépendances depuis un registre privé, interroger une base de données de test, ou utiliser une API cloud), lancer trop de jobs simultanément peut déclencher des blocages de sécurité (rate limiting) ou des frais supplémentaires. `max-parallel` permet de contrôler le flux de requêtes.