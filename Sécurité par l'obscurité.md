# Sécurité par l'obscurité
## Sommaire
- Qu'est ce que la sécurité par l'obscurité
- La sécurité par l'obscurité est elle une bonne pratique ?
  - Exemple de mauvaise utilisation
  - Exemple de bonne utilisation
- L'exemple du port-knocking

## Qu'est ce que la sécurité par l'obscurité
Désigne des méthodes de sécurisation des systèmes qui se basent sur le faite de cacher des informations.

Par exemple on va prendre l'exemple d'un éditeur de logiciel. Il ne souhaite pas que n'importe qui puisse lire et comprendre facilement le code de celui-ci.

C'est pourquoi il va chercher à obfusquer le programme pour rendre sa rétro ingénieurie plus difficile.

Il va par exemple pouvoir rajouter des instructions inutile / chiffrer une partie du programme. Bref rendre son fonctionnement plus complexe à comprendre.

Dans tout les cas ces méthodes de sécurité ne vise qu'a décourager et faire perdre du temps à l'attaquant. Et c'est justement ça qui lui est reproché.

## La sécurité par l'obscurité est elle une bonne pratique ?
La sécurité par l'obscurité est souvant utilisé comme un terme péjoratif. En effet le risque c'est de donner l'illusion de la sécurité.

#### Exemple de mauvaise utilisation
Par exemple admettons que vous faire le choix de crée un système d'authentification sur votre site web coté front-end en java-script.
```js
var pseudo=document.login.pseudo.value;
var password=document.login.password.value;
if (pseudo=="admin" && password=="monSUPERmotdepasse") {
  alert("Bravo vous êtes admin");
} else { 
  alert("Accès non autorisé"); 
}
```

C'est une très mauvaise pratique et n'importe qui pourra se connecter en tant qu'admin.

La fausse bonne idée serait de rajouter une couche d'obfuscation pour se sentir sécurisé.

```js
var pseudo=document.login.pseudo.value;
var password=document.login.password.value;
if (pseudo=="admin" && btoa(password)=="bW9uU1VQRVJtb3RkZXBhc3Nl") {
  alert("Bravo vous êtes admin");
} else { 
  alert("Accès non autorisé"); 
}
```

Le problème c'est que ce n'est qu'une question de temps avant que quelqu'un parvienne à désobfusquer le code. D'autant plus avec l'arrivé des IA qui s'en chargeront pour vous.

Ici par exemple le mot de passe était juste encodé en base64.

#### Exemple de bonne utilisation
Ce qu'il faut comprendre c'est que la sécurité par l'obscurité ne peu pas se substituer à une vrai statégie de sécurité robuste.

Cependant ça reste une très bonne idée de l'utilisé. Admettons que vous avez un serveur ssh ultra sécurisé qui tourne sur l'un de vos serveurs exposé à internet.

Le problème c'est qu'il tourne sur le port par defaut de ssh à savoir le 22. Vous allez donc recevoir enormement de fausse alert de bot qui essayerons en boucle de se connecter avec des mots de passe faible.

Non seulement ça peu ralentir le système mais surtout dans vous allez avoir du mal à détecter les tentatives d'attaque sérieuse.

La bonne pratique dans ces cas là pourrait être d'utiliser un autre port que le 22. Ainsi vous vous éconnomiserez du traffic.
Et dans le cas ou il y a une tentative d'attaque, vous saurez que l'attaquant n'est pas juste un bot et donc représente une vrai menance à gérer.

#### L'exemple du port-knocking
Tout ça c'est super mais est ce qu'on pourrait pas faire mieux pour encore plus rendre notre serveur ssh indetectable ?

C'est ici qu'intervien le port-knocking (pour moi l'une des meilleurs technique de sécurité par l'obscurité).

L'idée c'est que votre serveur va tout simplement fermer ssh jusqu'a recevoir une suite de requête sur une combinaison de ports que vous aurez choisie.

```
mechantHacker -- scan --> serveur (aucun port ouvert)
bob -- SYN port 4596 --> serveur
bob -- SYN port 8899 --> serveur
bob -- SYN port 7589 --> serveur
bob -- SYN port 1111 --> serveur (ouvre le service ssh pour l'ip de bob)
bob  -- auth ssh ------> serveur
```

Enfaite il va y avoir un service par exemple knockd qui va écouter tout les port de la machine et si il reçoi dans l'odre et sur une durée de temps limité la combinaison de port il va autorisé dans le firewall la connexion pour l'adresse IP.

Attention il y a deux risques avec ça :
- Il faut choisir une combinaison de port qui ne soit pas que croissante.
Parce que par exemple si vous choisisez 4568, 5555, 9999. Il suffit que l'attaquant face un scan de tout les port (-p- sur nmap) que que finalement il envoie la combinaison car il va envoyer une requête sur les port de 0 à 65 535.

- Il faut aussi de préférence avoir un accès autre à la machine par exemple physique
Car si le ssh est votre seul accès au serveur et que pour une raison X ou Y votre service de port knocking ne fonctionne plus, vous êtes bloqué et n'avez plus accès au serveur.

##### Comment mettre ça en place ?




