# Rendu TD1 - Emile

## Exercice 1
- Lien PR : https://github.com/Mlelil/td1-devops-ensta
- Pourquoi crée-t-on une branche plutôt que de committer directement sur main ?
On ne modifie pas directement le main qui est la branche avec uniquement des versions finies et exportables en production.
Pour ajouter une nouvelle fonctionnalité ou corriger un bug, on crée une nouvelle branche dans laquelle on travaille tranquillement
avant de remerger vers le main.


## Exercice 2 - Capture The Bug

### Bug 1 - Division par zéro
- Déclencheur : Ecrire un numéro de ligne > aux lignes présentes dans la database
- Commit fautif : 7abdaed
- Auteur : Serpico serpico@nypd.gov
- Ligne problématique : row_index = st.sidebar.number_input("Numéro de ligne", min_value=1, max_value=100, value=1)
- Correction appliquée : max_value=len(filtered)-1

### Bug 2 Caractères
- Déclencheur : Caractères spéciaux dans la barre "Rechercher un produit"
- Commit fautif : e9b5614
- Auteur : Serpico serpico@nypd.gov
- Ligne problématique : data = data[data["product"].str.contains(search)]
- Correction appliquée : data = data[data["product"].str.contains(search, regex=False, na=False)]

### Bug 3
- Déclencheur : Aucun produit dans la recherche par filtre
- Commit fautif : 002ad36
- Auteur : Serpico
- Ligne problématique : avg = total // count
- Correction appliquée : pas vraiment réussi à le corriger, donc pour faire un changement j'ai réecris l'ancienne version avec avg = total // count if count > 0 else 0

## Exercice 3
- Commit de résolution : cd5fb3e
- Qu'est-ce qu'un conflit Git et pourquoi survient-il ?
Un conflit Git survient quand deux branches ont modifié les mêmes lignes d'un même fichier et qu'on tente de les fusionner. Il faut donc manuellement résoudre le conflit en choisissant quelle version choisir par exemple.
- (Bonus) Différence entre merge et rebase :
git merge crée un commit de fusion qui préserve l'historique des deux branches, tandis que git rebase réécrit les commits de la branche courante par-dessus la branche cible, produisant un historique linéaire

## Exercice 4
- Commande utilisée pour squasher les 3 commits : git reset --soft HEAD~3 puis git commit -m "Fix: correction des 3 bugs (division zéro, index, caractere speciaux)"
- Pourquoi squasher avant de merger ? Pour garder un historique propre sur main : un seul commit logique plutôt que 3 commits intermédiaires facilite la lecture de l'historique et les éventuels git revert.
- Différence entre --soft, --mixed et --hard : --soft annule les commits mais garde les changements stagés (index intact) ; --mixed (défaut) annule les commits et déstage les fichiers mais conserve les modifications dans le working directory ; --hard annule commits, déstage et supprime définitivement les modifications locales.
- Hash du commit hotfix sur main : à noter après git log --oneline -1 sur main post-merge
- Pourquoi cherry-picker vers dev plutôt que merger main dans dev ? La branche dev contient des features en cours non prêtes pour la production ; merger main dans dev risquerait d'introduire des complications ; cherry-pick permet de n'apporter que le commit de correction sans embarquer d'autres changements.

## Exercices 5/6
Je me suis arrêté là




# Rendu TD2 - Docker

## Exercice 1

- Que se passe-t-il si vous modifiez `app.py` ? Pourquoi ?
Les changements ne sont pas visibles. Il faut rebuilder l'app docker car le code est déjà compilé.

- Comment arrêtez-vous le container ?
Un bon Ctrl+C ou en utilisant docker stop id_du_container

- Quelle commande permet de voir les containers en cours d'exécution ?
docker ps

## Exercice 2 - Volumes

- Pourquoi le changement est-il visible sans rebuild ?
Car le volume monte le dossier local directement dans le container et non compilé et inchangeable

- Si vous ajoutez une nouvelle dépendance dans `requirements.txt`, est-ce que le volume suffit ? Pourquoi ?
Par contre il faut rebuild, car le volume fait rentrer des fichiers, mais les dépendances sont prises en compte
au moment de créer le container, et donc le volume ne peut pas le prendre en compte.

## Exercice 3 - Docker Compose

- Pourquoi utilisez-vous `redis` comme hostname et pas `localhost` ?
Docker Compose avec redis va attribuer à chaque service un nom de service lui-même dans son réseau interne. redis:6379 va désigner une instance de l'application là ou localhost:8501 va désigner le container entier.

- Que fait `depends_on` ? Est-ce suffisant pour garantir que Redis est prêt ?
Cela va indiquer à Docker Compose l'ordre de démarrage, mais ce n'est pas suffisant, car le 2e service, s'il est rapide, peut démarrer avant le 1er.

- Comment vérifiez-vous que Redis répond depuis l'intérieur du container app ?
jsp, peut-être avec un redis-cli ping



# TD 3 - Azure
## Exercice 1 : Azure Container Registry

- Quelle est la différence entre Docker Hub et ACR ?
Docker Hub est public et utilisé pour partager des images, tandis que Azure Container Registry (ACR) est privé et intégré à Azure, conçu pour un usage d'entreprise avec plus de sécurité

- Pourquoi utiliser un registry privé en entreprise ?
Pour sécuriser les image, contrôler les accès ou encore éviter les fuites de données de la PI



## Exercice 2 : Azure Container Apps

- Quelle est la différence entre Container Apps et une VM classique ?
Container Apps est un service qui exécute des containers sans gérer d’infrastructure, alors qu’une VM nécessite la gestion complète du système depuis l'OS jusqu'à l'app



- Que signifie "serverless" ?
L’utilisateur ne gère pas les serveurs. Le cloud gère tout automatiquement. Par exemple container Apps est serverless, on n'a pas besoin de gérer le scaling, la disponibilité ou l’exécution.

- Combien de replicas tournent par défaut ?
1 version


## Exercice 3 : Variables d'environnement

- Où sont stockées ces variables dans Azure ?
Elles sont stockées dans la configuration du service Container Apps sur la page web d'Azure

- Sont-elles visibles en clair quelque part ?
Oui, elles peuvent être visibles en clair dans la configuration du service si elles ne sont pas configurées comme privé

- En production, comment protégeriez-vous des valeurs sensibles (clés API, mots de passe) ?
On peut utiliser le service Azure Key Vault qui est designé exactement pour ça je crois


## Exercice 4 : Scaling & Load Testing

- Combien de replicas ont été créés pendant le test ?
5

- Combien de temps faut-il pour qu'un nouveau replica démarre ?
A peu près une dizaine de secondes

Q11 - Que se passe-t-il quand le trafic diminue ?
Le nombre de replicas diminue automatiquement pour économiser les ressources.
