Ce dépôt héberge la version de travail d'un cours de Programmation Orientée
Objet en Python, [publié sur Zeste De Savoir](https://zestedesavoir.com/tutoriels/1253/la-programmation-orientee-objet-en-python/).

# Installation

Assurez-vous d'avoir Python et virtualenv installés sur votre machine.

    $ git clone git@github.com:ArnaudCalmettes/cours-python3-poo.git
    $ cd cours-python3-poo
    $ sudo apt-get install python3 python3-virtualenv
    $ virtualenv -p python3 venv
    $ source venv/bin/activate
    $ pip install -r requirements.txt

Pour compiler le tutoriel :

    $ make html

Ceci génère le tutoriel au format html dans le répertoire `_build/html`.
Pour la liste des formats supportés par Sphinx, tapez `make`.

## Licence

Cours sous licence [CC BY-SA](https://creativecommons.org/licenses/by-sa/4.0/deed.fr).
