# Pip, pipenv, poetry et PEP517/518

## Le point sur le packaging, la gestion des dépendances et leur évolution


---
@snap[span-100]
## Thierry Chappuis [@css[text-primary](thierry@chappuis.net)]
@snapend

Haute Ecole d'Ingénierie et d'Architecture - Fribourg

---
@snap[north span-100]
# Vue d'ensemble
@snapend

- Comment en sommes-nous arrivés là?
- Un éco-système foisonnant
- Le processus de packaging
- Qu'est-ce qui cloche avec setup.py?
- Le format wheel
- La PEP 518 et pyproject.toml
- La PEP 517
- Poetry, pip, pipenv, pipx

---
@snap[north span-100]
# Comment en sommes-nous arrivés là?
@snapend

Once upon a time, Python 2.0 was released

---
@snap[north span-100]
# Un éco-système foisonnant
@snapend

distutils, setups, easy_install, pip, wheel, twine, setup.py, setup.cfg, requirements.txt, MANIFEST.in

---
@snap[north span-100]
# Le processus
@snapend

![Fonctionnement de pypi](https://gyazo.com/960e074617c7b6fe9ca744589b8e7c8b.png)

---
@snap[north span-100]
# Le processus
@snapend

Le format de chaque fichier est décrit dans la PEP 427
```
/Users/tchappui/Library/Python/3.7/lib/python/site-packages/zevents
├── __init__.py
├── __pycache__
│   ├── __init__.cpython-37.pyc
│   └── logic.cpython-37.pyc
└── un-module.py

/Users/tchappui/Library/Python/3.7/lib/python/site-packages/zevents-0.0.1.dist-info
├── INSTALLER
├── LICENSE.txt
├── METADATA
├── RECORD
├── WHEEL
├── top_level.txt
└── zip-safe
```

---
@snap[north span-100]
# Le processus
@snapend

```python
from setuptools import setup, find_packages

# ...

setup(
    author="Thierry Chappuis",
    author_email='thierry@chappuis.io',
    classifiers=[
        'Programming Language :: Python :: 3.7',
    ],
    description="Easy to use and generic events system.",
    install_requires=requirements,
    license="MIT license",
    long_description=readme + '\n\n' + history,
    include_package_data=True,
    keywords='zevents',
    name='zevents',
    packages=find_packages(include=['zevents']),
    setup_requires=setup_requirements,
    tests_require=test_requirements,
    url='https://github.com/tchappui/zevents',
    version='0.2.9',
    zip_safe=False,
)
```

---
@snap[north span-100]
# Le problème avec setup.py
@snapend

setup.py s'exécute sur le système cible

---
@snap[north span-100]
# Le problème avec setup.py
@snapend

L'utilisateur doit installer les outils pour la construction

```
File "setup_build.py", line 99, in run
        from Cython.Build import cythonize
    ImportError: No module named Cython.Build

---
@snap[north span-100]
# Le problème avec setup.py
@snapend

Difficile de changer d'outils de packaging<br>
setup.py imposé par pip

---
@snap[north span-100]
# Le problème avec setup.py
@snapend

Dépendances résolues à l'exécution

---?image=gitpitch/assets/img/coffee.jpg

@css[text-primary](Création d'un pipenv.lock ou poetry.lock)

---
@snap[north span-100]
# Le problème avec setup.py
@snapend

```
POUR chaque packet et chaque dépendance (directe et indirecte) FAIRE
    Découvrir le paquet
    Télécharger la distribution source et l'extraire
    Exécuter python setup.py install dans le répertoire extrait (build+install)
FIN POUR
```

---
@snap[north span-100]
# Le format wheel
@snapend

![fonctionnement avec wheel](https://gyazo.com/70fd66e6b5b3f3ef8cc6261ac0226bbc.png)

---
@snap[north span-100]
# Le format wheel
@snapend

Pas d'opération de construction (compilation) sur la machine de l'utilisateur.

---
@snap[north span-100]
# Vers PyPI avec cookiecutter-pyproject
@snapend

```
$ cookiecutter gh:audreyr/cookiecutter-pypackage
````

```
$ cookiecutter gh:audreyr/cookiecutter-pypackage                                                                       
You've downloaded /Users/tchappui/.cookiecutters/cookiecutter-pypackage before. Is it okay to delete and re-download it? [yes]:
full_name [Audrey Roy Greenfeld]: Thierry Chappuis
email [audreyr@example.com]: thierry@chappuis.io
github_username [audreyr]: tchappui
project_name [Python Boilerplate]: pyconfr
project_slug [pyconfr]:
project_short_description [Python Boilerplate contains all the boilerplate you need to create a Python package.]:
pypi_username [tchappui]:
version [0.1.0]:
use_pytest [n]: y
use_pypi_deployment_with_travis [y]:
...
```

---
@snap[north span-100]
# Vers PyPI avec cookiecutter-pyproject
@snapend

```
$ tree                                                                                                               
pyconfr
├── AUTHORS.rst
├── CONTRIBUTING.rst
├── HISTORY.rst
├── LICENSE
├── MANIFEST.in
├── Makefile
├── README.rst
├── docs
│   ├── Makefile
│   ├── authors.rst
│   ├── conf.py
│   ├── contributing.rst
│   ├── history.rst
```
---
```
│   ├── index.rst
│   ├── installation.rst
│   ├── make.bat
│   ├── readme.rst
│   └── usage.rst
├── pyconfr
│   ├── __init__.py
│   └── pyconfr.py
├── requirements_dev.txt
├── setup.cfg
├── setup.py
├── tests
│   ├── __init__.py
│   └── test_pyconfr.py
└── tox.ini

3 directories, 25 files
```

---
@snap[north span-100]
# PEP 518 et le format pyproject.toml
@snapend

```console
[build-system]
requires = [
    "setuptools >= 40.8.0",
    "wheel >= 0.30.0",
    "cython >= 0.29.4",
]
```

---
@snap[north span-100]
# PEP 518 et le format pyproject.toml
@snapend

`python setup.py install` devient:

- répertoire temporaire
- création d'un environnement isolé
- installation des bibliothèques de construction
- `python_isolated setup.py bdist_wheel`
- extraction du wheel par python 

---?image=gitpitch/assets/img/deception.jpg

@css[text-primary](setup.py is so 2000!)

---

@css[text-primary](setup.py:) un mécanisme qui a 20 ans

---

Flit, Poetry, etc. ?

---?image=gitpitch/assets/img/idee.jpg

@css[text-primary](Faire de setup.py l'exception plutôt que la règle)

---
@snap[north span-100]
# PEP 517: back-end et front-end pour la construction
@snapend

```
[build-system]
requires = ["flit"]
build-backend = "flit.buildapi"
```

```
[build-system]
requires = ["poetry>=0.12"]
build-backend = "poetry.masonry.api"
```

---
@snap[north span-100]
# PEP 517: back-end et front-end pour la construction
@snapend


```python
import flit.buildapi
backend = flit.buildapi

# build wheel via 
backend.build_wheel()

# build source distribution via
backend.build_sdist()
```

---
@snap[north span-100]
# PEP 517: back-end et front-end pour la construction
@snapend

Les outils de construction ne sont plus liés aux décisions de distutils

---
@snap[north span-100]
# PEP 517 et 518
@snapend

Supportés depuis pip 19.0 (2019)

---
@snap[north span-100]
# Des builds isolés avec tox
@snapend

Dès que pyproject.toml spécifie un requires et un build-backend:
```
[tox]
isolated_build = True
```

---
@snap[north span-100]
# pyproject.toml  pour les autres outils
@snapend

```
[build-system]
requires = [ "setuptools >= 35.0.2", "wheel >= 0.29.0"]
build-backend = "setuptools.build_meta"

[tool.tox]
legacy_tox_ini = """
[tox]
isolated_build = True
envlist = py27,py36

[testenv]
deps = pytest >= 3.0.0, <4
commands = pytest
"""
```

---
@snap[north span-100]
# Front-ends
@snapend

pip et poetry

---
@snap[north span-100]
# pip et le problème des dépendances
@snapend

Pas de résolution des dépendances


---?image=https://gyazo.com/c1123195aeedabc0f1e506c32122bd52.png


---?image=gitpitch/assets/img/dette.jpg

@css[text-primary](Dette technique et refactoring difficile)

---
@snap[north span-100]
# En attendant une vraie résolution des dépendances pour pip
@snapend

---?image=https://gyazo.com/ec5d9461865f242eb994bda7d754a0f6.png

---
@snap[north span-100]
# pipenv: managed venv + deps
@snapend

```
$ pipenv install django --python 3.7.4
$ pipenv run django-admin monsite . # l'installation force un venv
$ pipenv shell # pour activer l'environnement
```

---
@snap[north span-100]
# pipenv:  managed venv + deps
@snapend

- Création et gestion facilitée d'un environnement virtuel
- Gestion avancée des dépendances et génération d'un fichier de lock
- Visibilité via [packaging.python.org](https://packaging.python.org/tutorials/managing-dependencies/)
- Nécessite `setup.py` ou `pyproject.toml` pour pypi.org
- Introduction d'un fichier Pipfile :(
- Locking lent

---
@snap[north span-100]
# pipx: inspiré de npx pour les outils node.js
@snapend

Un pipenv pour vos outils

```
$ pip install pipx
$ pipx ensurepath
```

```
$ pipx run pipenv
$ pipx run black
$ pipx run flake8
```

---
@snap[north span-100]
# Poetry: un outils très complet
@snapend

- génération de projet: `poetry new my-project`
- construction d'un package: `poetry build`
- upload sur pypi.org: `poetry publish`
- dépendances spécifiées directement dans un **pyproject.toml**
- résolution avancée des dépendance et lock

---
@snap[north span-100]
# Poetry: un outils très complet
@snapend

```
$ poetry new my-super-project
```

```
$ tree
my-super-project
├── pyproject.toml
├── README.rst
├── my_super_project
│   └── __init__.py
└── tests
    ├── __init__.py
    └── test_my_super_project.py
```

```
$ poetry add django # l'installation force un venv
$ poetry run django-admin startproject pyconfr .
```

---
@snap[north span-100]
# Merci à vous
@snapend

...et à tous ceux qui travaillent pour rendre notre vie plus facile!