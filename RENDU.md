# Rendu TD1 - Emile

## Exercice 1
- Lien PR : https://github.com/Mlelil/td1-devops-ensta
- Pourquoi crée-t-on une branche plutôt que de committer directement sur main ?
On ne modifie pas directement le main qui est la branche avec uniquement des versions finies et exportables en production.
Pour ajouter une nouvelle fonctionnalité ou corriger un bug, on crée une nouvelle branche dans laquelle on travaille tranquillement
avant de remerger vers le main.


## Exercice 2 - Capture The Bug

### Bug 1 — Division par zéro
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