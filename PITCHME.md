@title[pytest]

## Réflexions autour des tests fonctionnels

---
@title[État des lieux]

### État des lieux

**Points positifs :**

* Passage à pytest

**Points à améliorer/revoir**

* Tests chronophages
* Monopolisent la plateforme pendant le test
* Difficulté de rejouer un test (syntaxe non-intuitive)
* Découpage très sommaire

---
@title[..]

### Exploration des pistes

* Fonctionnalités de base fournies par pytest
* Plugins pytest
* Adaptation des tests (découpage, restructuration)
* Autres

---
@title[..]

# Features de base

---
@title[..]


### Fixtures

* Définition pytest : *« The purpose of test fixtures is to provide a fixed baseline upon which tests can reliably and repeatedly execute. pytest fixtures offer dramatic improvements over the classic xUnit style of setup/teardown functions »*
* Interprétation perso : Une fixture permet de gérer une dépendance commune à plusieurs tests permettant de factoriser le l'écriture des tests

---
@title[..]

### Fixtures : Exemple 1

    import pytest

    @pytest.fixture
    def http_client():
        return …..

    def test_toto(client):
        response, msg = client.fetch(‘mon_url’)
        assert response.code == 200


---
@title[..]

### Fixtures : Exemple 2


    import pytest
    import smtplib

    @pytest.fixture(scope="module",
                    params=["smtp.gmail.com", "mail.python.org"])
    def smtp(request):
        smtp = smtplib.SMTP(request.param, 587, timeout=5)
        yield smtp
        print ("finalizing %s" % smtp)
        smtp.close()


---
@title[.]

### Avantages

* Une fixture utilise un mécanisme d’injection de dépendances (paramètre à ajouter dans la fonction de test)
* Une fixture peut être injectée sans import
* On peut injecter des fixtures dans des fixtures
* Bouclage sur des paramètres (exemple2)

---
@title[.]

### Failures/Error

* La distinction Failure/Error existe toujours dans pytest mais  comportement différent
* Toute exception est une failure (non limité aux assertion errors)
* Une erreur est une exception non catchée dans une fixture

<br>

**Application pratique** :  **Failure** = erreur de résultat contre **Erreur fixture** = erreur dans le protocol de test

---
@title[.]

### Test paramétrés

**Exemple :**

    @pytest.mark.parametrize(('x', 'y'), [(1, 1), (1, 0), (0, 1)])
    def test_simple_assume(x, y):
       Assert x==y

<br>

* Permet de boucler sur l'ensemble de paramètres
* Evite de constituer des compteurs avec assertion derrière

---
@title[..]

# Plugins intéressants
---

@title[..]

### pytest-sugar

* Sortie plus structurée (et plus colorée)
* Ne nécessite pas de paramètre additionnel

---
@title[..]

### pytest-assume

Permet de vérifier plusieurs assertions dans un même test :

    def test_simple_assume(x, y):
        pytest.assume(x == y)
        pytest.assume(True)
        pytest.assume(False)

---
@title[..]

### pytest-cagoule

* Permet de collecter les résultats des tests à un moment donné (`py.test –cagoule-capture`)
* Une fois le code modifié, on relance le test pour un fichier modifié  (`py.test –cagoule-select=path/to/file.py`)
* En s'interfaçant avec git, on peut afficher les tests qui sont affectés (`py.test –diff`)
* Beaucoup plus utile en CI qu’en usage dév (pour du reporting continu)

---
@title[..]

### pytest-concurrent

Permet de gérer la parallélisation des test en :

* Multi-threading
* Multi-processing
* Asyncnet (utilisation de gevent)

<br>
A voir en faisant des benchs

---

@title[..]

### pytest-random

Permet de randomizer l’ordre d’exécution des tests

    py.test . --random

---
@title[.]

### pytest-regtest

Ne teste pas si l’API / le code renvoie le résultat attendu mais seulement si il y a eu régression ou pas;

<br>
Code générant un résultat :

    def test_squares_up_to_ten(regtest):
        result = [i*i for i in range(10)]
        # one way to record output:
        print(result, file=regtest)
        # alternative method to record output:
        regtest.write("done")

---
@title[.]

L'exécution du test échoue (diff avec donnée nulle)

    py.test
    ...
    def test_squares_up_to_ten(regtest):
    E
    >       Regression test failed
    >
    >       --- is
    >       +++ tobe
    >       @@ -1,2 +1 @@
    >       -[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
    >       -done
    >       +

---
@title[.]

On assume que le résultat est correct :

    py.test --regtest-reset
    
On refait le test :

    py.test

---

@title[.]

### pytest-report

* Crée un rapport JSON compatible avec le format atom.IO
* On s’en fout du format atom.IO du moment que c’est pas du XML

---

@title[.]

### pytest-browser

Une image vaut mieux que mille mots.

---

@title[.]

![Texte alternatif]([h4qBYDH.png] "Screenshot")


---
@title[.]

# Autres

---

@title[.]

### Image docker routemm

Image docker prête à l'emploi

* Étape 1 : build `docker build -t routemm .` 
* Etape 2: enjoy `./docker-run` 

---

@title[.]

### Avantages

* Déploiement de la branche en cours dans un environnement totalement isolé
* Exécution des tests dans l'env docker
* On peut faire autre chose en attendant

---

@title[.]

# Conclusion

---

@title[.]

# Y a de quoi faire

---

@title[.]

# Ou pas !

---

@title[.]

# Des questions ?
