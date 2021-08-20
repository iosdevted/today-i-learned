# Préparation de partiel - BDD

1. Le modèle Entité/Association

    a. Ne contient que des associations binaires `(Association n-aire est aussi possible)`

    b. Ne doit pas contenir de `cardinalités` supérieurs à 1(1:1 / 1:M / N:M)

    c. `N’est pas unique.`

2. Le modèle Entité / Association

    a. Contient des informations complètes qu’il n’est pas possible de mettre à jour (possible)
    
    b. Est le modèle relationnel

    c. `Peut contenir des associations ternaires`

3. Le modèle relationnel

    a. Doit être établi avant le modèle Entité/Association(Après) 

    `(N’est pas la première étape dans le processus de modélisation)`

    b. Ne doit pas contenir de clés (Devoir contenir)

    c. Possède autant d’associations que de relations `(non, association -> Relation en anglais)`

    d. `Contient des relations`

5. Dans le domaine des bases de données relationnelles, le MCD veut dire;

    a. `Modèle Conceptuel de Données` (E/A -> MCD)

7. En algèbre relationnelle

    a. `Le résultat d’une jointure de deux relations est un sous ensemble de leur produit Cartésien`
    
    b. Le résultat d’un produit Cartésien est un sous ensemble de leur jointure naturelle

    c. Le résultat d’une jointure de deux relations est forcément vide

8. En algèbre relationnelle

    a. Une projection vaut une sélection

    b. `Une projection s’intéresse à un sous ensemble de colonnes d’une relation`
    
    c. Une projection s’opère obligatoirement sur deux relations `(Une projection s’opère sur une seule relation)`

9. En algèbre relationnelle

    a. Une sélection vaut une projection

    b. `Une sélection s’intéresse à un sous ensemble de lignes d’une relation`
    
    c. Une sélection s’opère obligatoirement sur deux relations `(une relation)`

10. Une case appartenant à une colonne faisant partie de la clé d’une relation

    a. Peut être vide

    b. `Doit absolument être renseignée`

    c. Peut contenir plusieurs valeurs

11. le mot clé Having dans une requête SQL :

    a. `Ne peut figurer qu’en présence du mot clé Group By`

    b. Est indépendant de tout autre mon clé

    c. Ne peut être utilisé que lorsque au minimum trois tables relationnelles sont impliquées

12. Une vue en SQL est forcément :

    a. Le résultat d’une jointure

    b. `Peut être le résultat d’une requête sur une seule table`

    c. `Peut être manipulée exactement comme une table relationnelle`

13. On considère le déclencheur DEC. Pour le supprimer sous oracle, on utilise :

    a. revoke trigger DEC;

    b. `drop trigger DEC;`

    c. delete trigger DEC;

14. Pour faire des modifications sur une table relationnelle quelconque en utilisant un bloc PL/SQL, la connaissance du type de tous les attributs de la table :

    a. Est obligatoire

    b. `N’est pas nécessaire`
    
    c. `Permet d’utiliser des instructions simples`

11. Le mot clé where dans une requête SQL :

    b. `Ne peut figurer qu’en présence du mot clé From`

    c. `Ne peut être utilisé que lorsque au minimum une table relationnelle est impliquée`

12. Une vue en SQL est forcément :

    b. `Peut être le résultat d’une requête sur deux tables`

    c. `Ne peut être manipulée comme une table relationnelle`

13. On considère la procédure PROC. Pour la supprimer sous oracle, on utilise :

    `a. drop Procedure PROC;`

14. Une relation normalisée

    a. Possède une clé atomique dans tous les cas

    b. `Peut avoir une clé atomique`

15. Une relation ayant une clé atomique (consituée d’un seul attribut) et dont tous les attributs sont atomiques :

    a. `Est forcément en première forme normale`

    b. `Est forcément en deuxième forme normale`
    
    c. Est forcément en troisième forme normale

20. Une relation n’ayant aucune dépendance fonctionnelle et dont tous les attributs sont atomiques :

    a. `Est forcément en première forme normale`

    b. `Est forcément en deuxième forme normale`
    
    c. `Est forcément en troisième forme normale`


## ETC

SGBD - Système de Gestion de Bases de Données

LMID - langage de manipulation et d’interrogation des donn ées

SQL - Langage de Requêtes Structure

MLD - Modèle Logique des Données

Une projection s’intéresse à un sous ensemble de colonnes d’une relation ; πA1 ,A3 (R)

Une sélection s’intéresse à un sous ensemble de lignes d’une relation ; la sélection σ4<A<10(R)

mysql -

Système de Gestion de Base de Données issues d'Oracle O Système de Gestion de Base de Données

[Entity Relation Model](https://victorydntmd.tistory.com/126)

[DB (1:1 / 1:M / N:M)](https://victorydntmd.tistory.com/30)


1NF - Une relation est dite normalisée (ou en première forme normale 1NF), si seulement si elle possède une clé et que chaque valeur de colonne est atomique (toutes les cases de la table relationnelle contiennent au plus une seule valeur).

2NF - Une relation est en deuxième forme normale (2NF) si et seulement si elle est 1NF et que chaque colonne ∈/ `cl ́e dépend élémentairement de la clé primaire.` Dans le cas où plusieurs clés candidates sont possibles sur une relation donnée, il faut en choisir une pour définir la clé primaire. (une seule DF) -> avoir la clé atomique

3NF - Une relation est en troisième forme normale 3NF si et seulement si elle est 2NF et qu’il n’y a aucune DF entre attributs n’appartenant pas à la clé. -> 기본 키(primary key)가 아닌 속성(Attribute)들은 기본 키에만 의존해야 한다.