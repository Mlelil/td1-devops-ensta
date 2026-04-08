# TD1 : Git — Capture The Bug 🐛

## Contexte

Vous rejoignez **Enstartup**. L'équipe a mis en prod une app Streamlit... mais des utilisateurs rapportent des crashs. L'app a 3 bugs cachés que personne n'arrive à reproduire.

Votre mission : les trouver, identifiez qui les a introduits, et les corrigez proprement avec Git.

---

## Prérequis

- [ ] Compte GitHub
- [ ] Git installé
- [ ] VSCode + extension **GitLens**
- [ ] Clé SSH configurée (voir Setup ci-dessous)
- [ ] Python 3.10+

---

## Setup SSH

```bash
ssh-keygen -t ed25519 -C "votre@email.com"
cat ~/.ssh/id_ed25519.pub
```
→ Copiez dans GitHub → Settings → SSH and GPG keys → New SSH key

Test : `ssh -T git@github.com`

---

## Setup Python (venv)

Créez un environnement virtuel pour isoler les dépendances :

```bash
python -m venv venv
source venv/bin/activate        # Linux/Mac
# venv\Scripts\activate         # Windows
pip install streamlit pandas
```

> Vous devrez activer le venv à chaque nouvelle session (`source venv/bin/activate`).

---

## Exercice 1 : Prise en main 
### Objectif
Fork, clone, branche, commit, PR.

### Étapes

1. **Fork** : allez sur `https://github.com/ThibaudMrx/td1-devops-ensta` et cliquez sur **Fork** en haut à droite pour copier le repo dans votre compte GitHub.

2. **Clone** : clonez votre fork (remplacez `[VOTRE-USERNAME]` par votre pseudo GitHub) :
```bash
git clone git@github.com:[VOTRE-USERNAME]/td1-devops-ensta.git
cd td1-devops-ensta
```

   Récupérez également la branche `dev` :
```bash
git checkout dev
git checkout main
```

3. **Lancez l'app** pour voir qu'elle fonctionne :
```bash
streamlit run app.py
```

   **Vérification** : dans votre terminal, confirmez que vous êtes bien sur `main` :
```bash
git branch
git log --oneline -5
```
   Vous pouvez aussi visualiser l'historique dans GitLens (icône Source Control → History).

4. **Créez une branche `feature/ajout-[votre-nom]`**, créez `contributors.md` et ajoutez votre nom :
```bash
git checkout -b feature/ajout-[votre-nom]
# Créez contributors.md et ajoutez votre nom
git add contributors.md
git commit -m "Add [votre-nom] to contributors"
```

   **Poussez la branche** et ouvrez une Pull Request :
```bash
git push origin feature/ajout-[votre-nom]
```
   → Sur GitHub, un bandeau apparaîtra pour créer la PR. Cliquez sur **Compare & pull request**, ajoutez une description, puis **Create pull request**.

---

## Exercice 2 : Trouvez 3 bugs 

### Contexte

L'app a **3 bugs cachés**. Chaque bug :
- A été introduit par un développeur à un moment précis
- Se déclenche par une interaction utilisateur spécifique
- Provoque une erreur visible (crash ou comportement anormal)
- Regardez les logs streamlit en parallèle. 

- Un des bugs est simplement un warning dans les logs
- Un autre est issu d'un input de caractère spécial
- Le dernier vient d'un out of range

Il suffit ici de trouver les bugs, ils seront corrigés dans l'exercice 4

### L'exercice

Pour chaque bug, trouvez :
1. **Comment le déclencher** (quelle interaction utilisateur)
2. **Le commit fautif** (hash)
3. **L'auteur** du commit
4. **La ligne de code** problématique

### Méthode

1. **Reproduisez le bug** : lancez l'app, testez différentes interactions
2. **Trouvez le commit** : utilisez GitLens ou les commandes :
```bash
git log --oneline
git blame app.py
git show <hash>
```
On peut aussi utiliser GitLens

### ✅ Checkpoint
- [ ] 3 commits identifiés
- [ ] 3 auteurs identifiés

---

## Exercice 3 : Résolution de conflit 

### Scénario

Lorsqu'on travaille longtemps sur la même branche, sans pull les changements de la branche `dev`, on peut modifier les mêmes fichiers que quelqu'un d'autre. Lorsqu'on pousse ces changements, il y aura un conflit de versions qu'il faut régler. On simule ce comportement ici

### Étapes

1. Depuis main, **commencez votre propre branche** :
```bash
git checkout main
git checkout -b ma-config
```

2. Modifiez `config.json` : changez `"debug": false` en `"debug": true`

```bash
git add config.json
git commit -m "Enable debug"
```

3. **Pendant ce temps, votre collègue** (depuis main) fait aussi une modif sur le même fichier. Simulez-le :
```bash
git checkout main
git checkout -b collegue-changes
# Modifiez config.json : "theme": "light" → "theme": "dark"
git add config.json
git commit -m "Change theme to dark"
git checkout main
git merge collegue-changes
```

4. Retournez sur votre branche et tentez de merger main → conflit !
```bash
git checkout ma-config
git merge main
```

5. Résolvez le conflit : gardez les **deux** changements (`"debug": true` ET `"theme": "dark"`).

6. Finalisez :
```bash
git add config.json
git commit -m "Merge: résolution conflit"
```

7. **Bonus — Rebase** : recommencez le même scénario sur de nouvelles branches, mais résolvez cette fois avec `git rebase` au lieu de `git merge`.
   Observez le résultat avec `git log --oneline --graph` et comparez avec l'historique obtenu à l'étape précédente.
   Quelle différence observez-vous ? Dans quel cas préférer l'un ou l'autre ?

---

## Exercice 4 : Hotfix 
En production, on ne corrige jamais directement sur main. On crée une branche hotfix/ depuis main, on corrige, on teste, puis on merge. Pourquoi ne pas corriger sur dev puis merger vers main ? Parce que dev contient des features en cours, pas encore prêtes pour la prod — on ne veut pas les embarquer avec le fix. Le hotfix part donc de main (état stable), puis on cherry-pick vers dev pour que la correction soit incluse dans la prochaine release. Ça garantit traçabilité et stabilité.

### Objectif

Corrigez les 3 bugs proprement selon le workflow DevOps :
1. Créez une branche hotfix depuis main
2. Corrigez les bugs
3. Mergez dans main
4. Cherry-pick vers dev

### Étapes

1. **Créez la branche hotfix** :
```bash
git checkout main
git checkout -b hotfix/fix-bugs
```

2. **Corrigez les 3 bugs** dans `app.py` — faites **un commit par bug** :
```bash
git add app.py
git commit -m "Fix: ..."   # répétez pour chaque bug
```

3. **Nettoyez l'historique** — avant de merger, regroupez vos 3 commits en un seul commit propre.

   *Indice : explorez `git reset`. Quelle différence entre `--soft`, `--mixed` et `--hard` ?*

   Le résultat attendu : un seul commit sur la branche, contenant les 3 corrections.

4. **Ouvrez une Pull Request** :
   - Sur GitHub, allez sur votre fork
   - Créez une Pull Request de `hotfix/fix-bugs` vers `main`
   - Décrivez les 3 bugs corrigés dans la description
   - Mergez la PR

5. **Cherry-pick vers dev** :
```bash
# Récupérez le hash du commit de merge (ou du commit de fix)
git checkout main
git pull origin main
git log --oneline -3

git checkout dev
git cherry-pick 
git push origin dev
```
---

## Exercice 5 : CI avec GitHub Actions (30 min)

### Objectif

Mettez en place une pipeline de tests automatiques qui tourne à chaque push.

> **C'est quoi GitHub Actions ?** C'est un système d'automatisation intégré à GitHub. Vous décrivez dans un fichier YAML quelles commandes exécuter (installer Python, lancer les tests…) et GitHub les exécute automatiquement sur ses serveurs à chaque push ou PR. C'est le principe de la **CI (Intégration Continue)** : on vérifie à chaque changement que rien n'est cassé.

### Étapes

1. **Créez le dossier tests** :
```bash
mkdir -p tests
touch tests/__init__.py
```

2. **Créez `tests/test_app.py`** :
Testez les 3 bugs que vous avez corrigés :
```python
import pandas as pd

def test_division_par_zero():
    """Vérifie que le calcul de moyenne gère le cas vide."""
    data = pd.DataFrame({"amount": []})
    total = data["amount"].sum()
    count = len(data)
    avg = total // count if count > 0 else 0  # Ne doit pas crasher
    assert avg == 0

def test_index_out_of_range():
    """Vérifie qu'on ne peut pas accéder à un index invalide."""
    data = pd.read_csv("data/sales.csv")
    row_index = 100
    assert row_index >= len(data) or data.iloc[row_index] is not None

def test_search_special_chars():
    """Vérifie que la recherche gère les caractères spéciaux."""
    data = pd.read_csv("data/sales.csv")
    search = "*"
    # Ne doit pas crasher avec regex=False
    result = data[data["product"].str.contains(search, regex=False)]
    assert result is not None
```

3. **Ajoutez pytest aux requirements** 

4. **Testez en local**

5. **Créez le workflow GitHub Actions** :
```bash
mkdir -p .github/workflows
```

Créez `.github/workflows/tests.yml` :
```yaml
name: Tests

on:
  push:
    branches: [main, dev]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      # TODO: Ajoutez les étapes manquantes
      # - Setup Python 3.11
      # - Installez les dépendances (requirements.txt + pytest)

      # exemple : comment run pytest
      - name: Run tests
        run: pytest -v
```

6. **Commit et push** :
```bash
git add .
git commit -m "Add CI pipeline with pytest"
git push origin main
```

7. **Vérifiez sur GitHub** : 
   - Allez dans l'onglet "Actions" de votre repo
   - Vous devriez voir le workflow tourner
   - ✅ Vert = tests passent, ❌ Rouge = échec


## Exercice 6 : Optimisation CI (15 min)

### Contexte

Votre pipeline CI tourne à chaque push, même si vous n'avez modifié que le README. C'est du gaspillage de ressources. En entreprise, les minutes CI coûtent cher et les queues peuvent être longues.

### Objectif

Configurez le workflow pour qu'il ne se déclenche que si des fichiers pertinents ont changé.

### Étapes

1. **Modifiez `.github/workflows/tests.yml`** :

Ajoutez un filtre pour que les tests ne se lancent que si `app.py`, `tests/` ou `requirements.txt` ont changé, ou en cas de pull requst sur main.

2. **Tester** :
   - Poussez un changement dans `README.md` → la CI ne doit **pas** se lancer
   - Poussez un changement dans `app.py` → la CI **doit** se lancer

### ✅ Checkpoint
- [ ] Modification du README ne déclenche pas la CI
- [ ] Modification de app.py déclenche la CI


### Bonus : Badge de statut

Ajoutez ce badge dans votre `README.md` (remplacez les valeurs) :
```markdown
![Tests](https://github.com/[VOTRE-USERNAME]/enstartup/actions/workflows/tests.yml/badge.svg)
```

### ✅ Checkpoint
- [ ] Tests passent en local (`pytest`)
- [ ] Workflow visible dans GitHub Actions
- [ ] Badge ajouté au README (bonus)

---

## Rendu

Créez `RENDU.md` dans votre repo :

```markdown
# Rendu TD1 - [Votre Nom]

## Exercice 1
- Lien PR : [URL]
- Pourquoi crée-t-on une branche plutôt que de committer directement sur main ? [1-2 phrases]

## Exercice 2 - Capture The Bug

### Bug 1
- Déclencheur : [comment reproduire]
- Commit fautif : [hash]
- Auteur : [nom]
- Ligne problématique : [code]
- Correction appliquée : [expliquez ce que vous avez changé et pourquoi]

### Bug 2
- Déclencheur : [comment reproduire]
- Commit fautif : [hash]
- Auteur : [nom]
- Ligne problématique : [code]
- Correction appliquée : [expliquez ce que vous avez changé et pourquoi]

### Bug 3
- Déclencheur : [comment reproduire]
- Commit fautif : [hash]
- Auteur : [nom]
- Ligne problématique : [code]
- Correction appliquée : [expliquez ce que vous avez changé et pourquoi]

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
```

---

## Ressources

- [Learn Git Branching](https://learngitbranching.js.org/)
- [Oh Shit, Git!?!](https://ohshitgit.com/)
- Cheatsheet fournie : `cheatsheet-git.md`
