## Comment veux-tu que je t'encapsule ?

### Au commencement étaient les invariants

Les différents attributs de notre objet forment un état de cet objet, normalement stable. Ils sont en effet liés les uns aux autres, la modification d'un attribut pouvant avoir des conséquences sur un autre.
Les invariants correspondent aux relations qui lient ces différents attributs.

Imaginons que nos objets `User` soient dotés d'un attribut contenant une évaluation du mot de passe (savoir si ce mot de passe est assez sécurisé ou non), il doit alors être mis à jour chaque fois que nous modifions l'attribut `password` d'un objet `User`.

Dans le cas contraire, le mot de passe et l'évaluation ne seraient plus corrélés, et notre objet `User` ne serait alors plus dans un état stable.
Il est donc important de veiller à ces invariants pour assurer la stabilité de nos objets.

### Protège-moi

Au sein d'un objet, les attributs peuvent avoir des sémantiques différentes.
Certains attributs vont représenter des propriétés de l'objet et faire partie de son interface (tels que le prénom et le nom de nos objets `User`). Ils pourront alors être lus et modifiés depuis l'extérieur de l'objet, on parle dans ce cas d'attributs publics.

D'autres vont contenir des données internes à l'objet, n'ayant pas vocation à être accessibles depuis l'extérieur.
Nous allons sécuriser notre stockage du mot de passe en ajoutant une méthode pour le *hasher*[^hasher] (à l'aide du module `crypt`), afin de ne pas stocker d'informations sensibles dans l'objet.
Ce condensat du mot de passe ne devrait pas être accessible de l'extérieur, et encore moins modifié (ce qui en altérerait la sécurité).

[^hasher]: Le *hashage* d'un mot de passe correspond à une opération non-réversible qui permet de calculer un condensat (*hash*) du mot de passe. Ce condensat peut-être utilisé pour vérifier la validité d'un mot de passe, mais ne permet pas de retrouver le mot de passe d'origine.

De la même manière que pour les attributs, certaines méthodes vont avoir une portée publique et d'autres privée (on peut imaginer une méthode interne de la classe pour générer notre identifiant unique).
On nomme encapsulation cette notion de protection des attributs et méthodes d'un objet, dans le respect de ses invariants.

Certains langages[^encapsulation_languages] implémentent dans leur syntaxe des outils pour gérer la visibilité des attributs et méthodes, mais il n'y a rien de tel en Python.
Il existe à la place des conventions, qui indiquent aux développeurs quels attributs/méthodes sont publics ou privés.
Quand vous voyez un nom d'attribut ou méthode débuter par un `_` au sein d'un objet, il indique quelque chose d'interne à l'objet (privé), dont la modification peut avoir des conséquences graves sur la stabilité.

[^encapsulation_languages]: C++, Java, Ruby, etc.

```python
import crypt

class User:
    def __init__(self, id, name, password):
        self.id = id
        self.name = name
        self._salt = crypt.mksalt() # sel utilisé pour le hash du mot de passe
        self._password = self._crypt_pwd(password)

    def _crypt_pwd(self, password):
        return crypt.crypt(password, self._salt)

    def check_pwd(self, password):
        return self._password == self._crypt_pwd(password)
```

```python
>>> john = User(1, 'john', '12345')
>>> john.check_pwd('12345')
True
```

On note toutefois qu'il ne s'agit que d'une convention, l'attribut `_password` étant parfaitement visible depuis l'extérieur.

```python
>>> john._password
'$6$DwdvE5H8sT71Huf/$9a.H/VIK4fdwIFdLJYL34yml/QC3KZ7'
```

Il reste possible de masquer un peu plus l'attribut à l'aide du préfixe `__`. Ce préfixe a pour effet de renommer l'attribut en y insérant le nom de la classe courante.

```python
class User:
    def __init__(self, id, name, password):
        self.id = id
        self.name = name
        self.__salt = crypt.mksalt()
        self.__password = self.__crypt_pwd(password)

    def __crypt_pwd(self, password):
        return crypt.crypt(password, self.__salt)

    def check_pwd(self, password):
        return self.__password == self.__crypt_pwd(password)
```

```python
>>> john = User(1, 'john', '12345')
>>> john.__password
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'User' object has no attribute '__password'
>>> john._User__password
'$6$kjwoqPPHRQAamRHT$591frrNfNNb3.RdLXYiB/bgdCC4Z0p.B'
```

Ce comportement pourra surtout être utile pour éviter des conflits de noms entre attributs internes de plusieurs classes sur un même objet, que nous verrons lors de l'héritage.
