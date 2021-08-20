# Préparation de partiel - Linux

## Question

1. A quoi sert le fichier .bashrc ?

> Le fichier .bashrc permet à l’utilisateur de personnaliser son shell.
Il ne concerne pas le login shell, c’est-a`-dire qu’il intervient lors du lancement d’un xterm en mode graphique par exemple. On peut tout de mˆeme l’associer au login shell en cr ́eant un lien symbolique avec le bash profile.
[ref](http://julien.marmin.free.fr/download/bashrc/bashrc.pdf)

## Basic Command

```bash
cd / -> Absolute Path
> replace
>> appending

Pour me déplacer dans mon répertoire personnel : 
$ cd ~

Pour créer un dossier nommé tp10 : 
$ mkdir tp10

Pour `créer un chemin` tp10/script/data/tmp : 
$ mkdir -p tp10/script/data/tmp

Pour créer les fichiers un, deux et trois :
$ touch un deux trois

Pour supprimer le fichier trois :
$ rm trois

Pour revenir dans le répertoire parent : 
$ cd ..

Pour savoir où je suis : 
$ pwd

Pour lister les fichiers du répertoire courant en affichant les fichiers cachés :
$ ls -a

`Pour supprimer le répertoire tmp qui contient des fichiers` :
$ rm -r tmp

`Pour effacer le répertoire vide script/data` :
$ rmdir script/data

Pour créer un fichier nommé "mon texte" :
$ > "mon texte" 

Pour renommer le fichier "a" en "b" :
$ mv "a" "b"

`Pour visualiser le contenu du fichier texte ~/.bashrc` :
$ cat ~/.bashrc 
$ less ~/.bashrc 

Pour connaître le type du fichier bin/date : 
$ file bin/date

Pour comparer deux fichiers version1.py et version2.py : 
$ diff version1.py version2.py

Listez tous les fichiers et envoyer le résultat dans le fichier Exercice :   
$ ls -a > Exercice

Ajoutez la date du jour dans Exercice :  
$ date +%d >> Exercice

#Ajoutez l'heure dans Exercice :   
$ date +%H >> Exercice

Affichez le contenu de Exercice : 
$ cat Exercice
                                                                                                                               
Afficher les droits de Exercice :     
$ ls -l Exercice
$ stat Exercice   

Déplacez le fichier Messages pour le placer 2 niveaux plus haut (dans Exercice) avec un chemin relatif :
$ mv message Exercice/Message

Triez le contenu du fichier Messages et enlevez les doublons :
$ sort Messages | uniq

Le script retourne des erreurs, lancez le avec un chemin relatif cette fois et en envoyant les erreurs dans un fichier log :
$ visiteurs/bin/script.sh 2> erreur.log

#En consultant les erreurs vous constatez que le programme a besoin de votre nom d'utilisateur dans une variable nommée NAME de l'environnement. Ajouter cette variable avec la bonne valeur dans l'environnement :
$ export export NAME=Nicolas

#Vérifier que la variable est bien dans l'environnement :
$ printenv NAME

Dans le répertoire courant il y a un fichier nombre, relancez le script en lui passant ce fichier en entrée :
$ script.sh nombre

#Le script prend du temps à s'exécuter, comment le passer en arrière plan ?
$ script.sh nombre &

Le processus numéro 1234 consomme trop de mémoire et de processeur, tuez le violemment :
$ kill -9 1234
```

[linux date command](https://www.thegeekstuff.com/2013/05/date-command-examples/)

## Permission

[ubuntu français](https://doc.ubuntu-fr.org/permissions)

```
drwxr-xr-x
```

Il se traduit de la manière suivante :

- d : c'est un répertoire.
- rwx pour le 1er groupe de 3 symboles : son propriétaire peut lire, écrire et exécuter.
- r-x pour le 2e groupe de 3 symboles : le groupe peut uniquement lire et exécuter le fichier, sans pouvoir le modifier.
- r-x pour le 3ème groupe de 3 symboles : le reste du monde peut uniquement lire et exécuter le fichier, sans pouvoir le modifier.

```
-rw-r--r--
```

- Le 1er caractère est - ⇒ c'est un fichier.
- Le premier groupe de 3 caractères est rw- ⇒ le propriétaire a le droit de lecture et écriture (mais pas d'exécution) sur le fichier.
- Les 2 groupes suivants sont r-- ⇒ Les utilisateurs du groupe et les autres n'ont que le droit de lecture (pas d'écriture, ni d'exécution).

### Command

La commande chown `(change owner, changer le propriétaire)` permet de changer le propriétaire du fichier. Seuls le super-utilisateur ou le propriétaire actuel d'un fichier peut utiliser chown.

```
소유자 명 변경
$ chown [소유자] [파일 이름]

소유자와 소유 그룹을 동시에 변경 
$ chown [소유자].[소유 그룹] [파일이름] 또는 chown [소유자]:[소유 그룹] [파일이름]

해당 소유자의 소유그룹으로 변경
$ chown [소유자]. [파일 이름]

소유 그룹 변경
$ chown .[소유 그룹] [파일 이름]
```

L'outil chmod `(change mode, changer les permissions)` permet de modifier les permissions sur un fichier.
En gérant chaque droit séparément

De cette façon, on va choisir :

À qui s'applique le changement

- u (user, utilisateur) représente la catégorie "propriétaire" ;
- g (group, groupe) représente la catégorie "groupe propriétaire" ;
- o (others, autres) représente la catégorie "reste du monde" ;
- a (all, tous) représente l'ensemble des trois catégories.

La modification que l'on veut faire

```
+ : ajouter
- : supprimer
= : affectation
Le droit que l'on veut modifier
r : read ⇒ lecture
w : write ⇒ écriture
x : execute ⇒ exécution
X : eXecute ⇒ exécution, concerne uniquement les répertoires (qu'ils aient déjà une autorisation d'exécution ou pas) et les fichiers qui ont déjà une autorisation d'exécution pour l'une des catégories d'utilisateurs. Nous allons voir plus bas dans la partie des traitements récursifs l'intérêt du X.
```

```bash
Donnez la propriété de Exercice à Dupond et au groupe Users :    
$ chown Dupond:Users Exercice    

La commande affiche :  
-rwx-wx-w-  1 Dupond  Users  0  20 ma 10:20 Exercice

Qui peut lire ce fichier ?  
$ Dupond

Qui peut modifier ce fichier ?    
$ Dupond et Users

Qui peut exécuter ce fichier ?    
$ Dupond, Users, All (Dupond les membres du groupe Users et les autres)

Ajoutez le droit de lire le fichier aux utilisateurs autre que Dupond et les membres du groupe Users :                      
$ chmod a+r fichier     

Le répertoire  /home/visiteur/bin contient un texte nommé `test.txt` qui vous appartient, modifiez les droits de ce fichier, donnez vous les droits de lecture, écriture, exécution et donnez juste le droit d exécution à tous les autres:
$ chmod u=rwx,g=x,o=x /home/visiteur/bin/test.txt
```

### ETC

```bash
Complétez la boucle suivante pour envoyer 5 fois les messages "Bonjour" dans le fichier Messages :

for i in {1 2 3 4 5};
    do echo "Bonjour" > Messages
done

for i in {1..5}
do
    echo "HelloWorld"
done
```