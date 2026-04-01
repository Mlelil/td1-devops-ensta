# Rendu TD1 - Emile Sassine

## Exercice 1
- Lien PR : https://github.com/Mlelil/td1-devops-ensta
- Pourquoi crée-t-on une branche plutôt que de committer directement sur main ?
On ne modifie pas directement le main qui est la branche avec uniquement des versions finies et exportables en production.
Pour ajouter une nouvelle fonctionnalité ou corriger un bug, on crée une nouvelle branche dans laquelle on travaille tranquillement avant de remerger vers le main.

## Exercice 2 - Capture The Bug

### Bug 1 Sidebar
- Déclencheur : Ecrire un numéro de ligne > aux lignes présentes dans la database
- Commit fautif : 7abdaed
- Auteur : Serpico <serpico@nypd.gov>
- Ligne problématique : row_index = st.sidebar.number_input("Numéro de ligne", min_value=1, max_value=100, value=1)
- Correction appliquée : max_value=len(filtered)-1

### Bug 2 Caractères
- Déclencheur : Caractères spéciaux dans la barre "Rechercher un produit"
- Commit fautif : e9b5614
- Auteur :  Serpico <serpico@nypd.gov>
- Ligne problématique : data = data[data["product"].str.contains(search)]
- Correction appliquée : data = data[data["product"].str.contains(search, regex=False, na=False)]

### Bug 3 
- Déclencheur : Aucun produit dans la recherche par filtre
- Commit fautif : 002ad36
- Auteur : Serpico
- Ligne problématique : avg = total // count
- Correction appliquée : pas vraiment réussi à le corriger, donc pour faire un changement j'ai réecris l'ancienne version avec
avg = total // count if count > 0 else 0


## Exercice 3
- Commit de résolution : [hash]
- Qu'est-ce qu'un conflit Git et pourquoi survient-il ? [1-2 phrases]
- (Bonus) Différence entre merge et rebase : [1-2 phrases]

## Exercice 4
- Commande utilisée pour squasher les 3 commits : [commande]
- Pourquoi squasher avant de merger ? [1 phrase]
- Différence entre `--soft`, `--mixed` et `--hard` : [1-2 phrases]
- Hash du commit hotfix sur main : [hash]
- Pourquoi cherry-picker vers dev plutôt que merger main dans dev ? [1 phrase]

## Exercices 5/6
- Lien vers le workflow GitHub Actions : [URL]
- Qu'apporte la CI par rapport à des tests lancés manuellement ? [1-2 phrases]
- Pourquoi filtrer les fichiers qui déclenchent la CI ? [1 phrase]