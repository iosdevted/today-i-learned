# Préparation de partiel - Clisp

## 2021 Epreuve 1

Soit une liste de doublets contenant des villes et un nombre de vaccins nécessaire pour chaque ville, construire récursivement une liste ne contenant que le nombre de vaccins et en faire le total.

```lisp
((ville . vaccins) (ville . vaccins) ... (ville . vaccins))
=> (vaccins vaccins vaccins vaccins) 
=> total
```

```lisp
(defun calculate-vaccines (lst &optional (result))
  (cond
    ((null lst) (print result) (apply '+ result))
    ((equal result nil) (setq result (list (cdar lst))) (calculate-vaccines (cdr lst) result))
    (t (setf result (append result (list (cdar lst))))
      (calculate-vaccines (cdr lst) result) ) ) )
    
(defun calculate-vaccines (lst &optional (result))
  (cond
    ((null lst) (print result) (apply '+ result))
    ((equal result nil) (calculate-vaccines (cdr lst) (list (cdar lst))))
    (t (calculate-vaccines (cdr lst) (append result (list (cdar lst))))) ) )

(setq vacines '((ville . 1) (ville . 2) (ville . 3)))
(calculate-vaccines vacines)
=> (1 2 3)
=> 6

;only have a value of list
(defun calculate-vaccines (lst)
  (cond
    ((null lst) lst)
    ((atom lst) (list lst))
    (t (append (calculate-vaccines (cdar lst)) (calculate-vaccines (cdr lst)))) ) )

(setq vacines '((ville . 1) (ville . 2) (ville . 3)))
(calculate-vaccines vacines)
=> (1 2 3)
```

## 2021 Epreuve 2 

A partir d'un arbre (liste quelconque mais contenant exclusivement des atomes, pas de nombres ni de chaînes), supprimer les atomes ayant moins de 3 caractères, à l'aide d'une fonction chirurgicale récursive.

Astuce 1 : Pour convertir en chaîne, utilisez la fonction string, et pour calculer la longueur, la fonction length.

Astuce 2 : Commencez par écrire une fonction non chirurgicale (qui construit sans modifier).

Vous avez le droit de faire des fonctions auxiliaires si ça vous facilite la tâche.

```lisp
(defun atomes (lst)
  (cond 
    ((null lst) nil)
    ((atom (car lst)) (atomes (cdr lst)) (atomes (cddr lst)))
    ((and 
        (atom (caar lst)) 
        (< (length (string (caar lst))) 3) )
      (rplaca (car lst) nil)
      (atomes (cdar lst)) )
    ((and 
        (atom (caar lst)) 
        (>= (length (string (caar lst))) 3) )
      (atomes (cdar lst)) ) ) )
    
(setq tree '(how (ac (acww (hos (hoa)))) (hua (ho))))
(print (atomes tree))
(print tree)
=> (HOW (NIL (ACWW (HOS (HOA)))) (HUA (NIL))) 
```

## 2019 Epreuve 3 

Dans les différents exercices, vous pouvez, si besoin est, écrire des sous-fonctions pour vous simplifier le travail.

1. Construire à la main une liste de mots en français avec leurs traductions en anglais (il doit y avoir au moins un mot en français avec plusieurs traductions en anglais), de la forme :

`Traductions : (anglais (mot1 (motA motB)) ... (motN (motX)))`

Faire une fonction qui ramène la traduction d'un mot à partir de cette liste :

`(traduction mot Traductions) => la liste des traductions du mot (line route cable)` 

```lisp
(defun traduction (word lst)
  (cond 
    ((null lst) lst)
    ((atom lst) nil)
    ((and 
        (listp lst) 
        (equal word (car lst)) )
      (cadr lst) )
    (t (append (traduction word (car lst)) (traduction word (cdr lst)))) ) )

(setq traductions '(anglais (mot (word)) (online (line route cable))))
(traduction 'online traductions)
=> (line route cable)
```

2. Faire une fonction qui récupère tous les mots en anglais de Traductions en supprimant les doublons.

```lisp
(defun traduction (lst)
  (cond 
    ((null lst) lst)
    ((atom lst) nil)
    ((equal 'anglais (car lst)) 
      (append 
        (traduction (car lst)) 
        (traduction (cdr lst)) ) )
    ((and
        (atom (car lst))
        (listp (cdr lst)) ) 
      (cadr lst) )
    (t (append (traduction (car lst)) (traduction (cdr lst)))) ) )

(setq traductions '(anglais (mot (word)) (online (line route cable))))
(traduction traductions)
=> (WORD LINE ROUTE CABLE)
```

## 2019 Epreuve 4

A partir de Traductions, fabriquez une liste dans laquelle il n'y a qu'une seule traduction pour chaque mot, à l'aide d'une fonction chirurgicale récursive.

La liste Traductions doit devenir de la forme :
`Traductions : (anglais (mot1 . motA) ... (motN . motX))`

Astuce : Commencez par écrire une fonction non chirurgicale (qui construit sans modifier)

```lisp

(defun modifier (lst)
   (cond
      ((atom lst) nil)
      ((equal (car lst) 'anglais) (modifier (cdr lst)))
      (t
        (rplacd
          (car lst)
          (caadar lst) )
          (modifier (cdr lst)) ) ) )
          
(setq lst '(anglais (mot (word)) (online (line route cable))))
(print (modifier lst))
(print lst)

```

## 2017 Epreuve 5

Dans les différents exercices, vous pouvez, si besoin est, écrire des sous-fonctions pour vous simplifier le travail.

1. À partir d'une liste associant des titres de films et leurs réalisateurs (certains réalisateurs ont fait plusieurs films) et d'une liste des budgets correspondants en euros :

ListeA : ((film1 réalisateur1) ... (filmN réalisateurM)) ListeB : (budget1 ... budgetN)

Faire une fonction récursive qui associe le budget au film :

(associe ListeA ListeB) => ((film1 réalisateur1 budget1) ... (filmN réalisateurM budgetN))

```lisp
(defun combine (lst1 lst2)
    (list (append (car lst1) (list (car lst2)))) )

(defun associe (lst1 lst2 &optional (result))
  (cond
    ((null lst1) result)
    ((setq 
        result 
        (append result (combine lst1 lst2)) )
      (associe (cdr lst1) (cdr lst2) result) ) ) )


;2 
(defun associe (lst1 lst2)
  (cond
    ((atom lst1) nil)
    (t (cons 
      (cons
        (caar lst1)
        (cons (cadar lst1) (cons (car lst2) nil)) )
      (associe (cdr lst1) (cdr lst2)) ) ) ) )

(setq films '((film1 réalisateur1) (film2 réalisateur2) (film3 réalisateur1)))
(setq budgets '(100 200 300))

(setq films '((film1 réalisateur1) (film2 réalisateur2) (film3 réalisateur1)))
(setq budgets '(100 200 300))

(associe films budgets)
=> ((FILM1 RÉALISATEUR1 100) (FILM2 RÉALISATEUR2 200) (FILM3 RÉALISATEUR1 300))
```

2. Faire une fonction récursive qui calcule le budget moyen à partir de la ListeB

`(budget_moyen ListeB) => bugdet_moyen`

```lisp
(defun budget_moyen (lst &optional (result 0) (len (list-length lst)))
  (cond
    ((null lst) (/ result len))
    (t (budget_moyen (cdr lst) (+ result (car lst)) len)) ) )

(setq budgets '(100 200 300))
(budget_moyen budgets)
=> 200
```

### list-length

Dans les différents exercices, vous pouvez, si besoin est, écrire des sous-fonctions pour vous simplifier le travail.

Soit la liste <étudiants>, ayant la forme :

`((étudiant1 (cours1 . note) (cours2 . note) ...) (étudiant2 (cours1 . note) ...) ... (étudiantn (cours1 . note) ...) )`

Construire récursivement :

1. la liste des étudiants : (étudiant1 étudiant2 ... étudiantn)

```lisp
(defun etudiant (lst)
  (cond
    ((null lst) lst)
    ((atom lst) nil)
    ((and
        (atom (car lst))
        (listp (cdr lst)) ) 
      (list (car lst)) )
    (t (append (etudiant (car lst)) (etudiant (cdr lst)))) ) )


(setq étudiants '((étudiant1 (cours1 . note) (cours2 . note)) (étudiant2 (cours1 . note)) (étudiant3 (cours1 . note) (cours2 . note))))
(etudiant étudiants)
=> (ÉTUDIANT1 ÉTUDIANT2 ÉTUDIANT3) 
```

2. la liste des cours suivi par un étudiant donné (sans les notes)

ex. (cours étudiant étudiants) => (cours1 cours2... )

```lisp
(defun cours-aux (lst)
  (cond
    ((null lst) lst)
    ((atom lst) (list lst))
    (t (append (cours-aux (caar lst)) (cours-aux (cdr lst)))) ) )

(defun cours (student lst)
  (cond
    ((null lst) lst)
    ((atom lst) nil)
    ((and 
        (listp lst) 
        (equal student (car lst)) )
      (cours-aux (cdr lst)) )
    (t (append (cours student (car lst)) (cours student (cdr lst)))) ) )
    
(setq étudiants '((étudiant1 (cours1 . note) (cours2 . note)) (étudiant2 (cours1 . note)) (étudiant3 (cours1 . note) (cours2 . note))))
(print (cours 'étudiant1 étudiants))
=> (COURS1 COURS2) 
```

3. la moyenne des notes obtenues par un étudiant donné

ex. (moyenne étudiant étudiants) => <somme des notes divisée par le nombre de cours ) 

```lisp
(defun moyenne-aux (lst length &optional (result 0))
  (cond
    ((null lst) (/ result (- length 1)))
    (t (moyenne-aux (cdr lst) length (+ result (cdar lst))))) )

(defun moyenne (student lst)
  (cond
    ((null lst) lst)
    ((atom lst) nil)
    ((and 
        (listp lst) 
        (equal student (car lst)) )
      (list (moyenne-aux (cdr lst) (list-length lst))) )
    (t (append (moyenne student (car lst)) (moyenne student (cdr lst)))) ) )
    
(setq étudiants '((étudiant1 (cours1 . 1) (cours2 . 3)) (étudiant2 (cours1 . 4)) (étudiant3 (cours1 . 5) (cours2 . 6))))
(moyenne 'étudiant1 étudiants)
=> (2)
```

4. la moyenne des notes obtenues pour un cours donné

ex. (moyenne cours étudiants) => <somme des notes divisée par le nombre d'étudiants )

## 2017 Epreuve 7

Faire une fonction qui modifie chirurgicalement la liste <étudiants> pour remplacer la liste des cours et des notes par leur moyenne. On doit aboutir à :

((étudiant1 . moyenne) (étudiant2 . moyenne)
...
(étudiantn . moyenne) )

```lisp
(defun moyenne-aux (lst length &optional (result 0))
  (cond
    ((null lst) (/ result length))
    (t (moyenne-aux (cdr lst) length (+ result (cdar lst))))) )
    
(defun change (student lst)
  (cond
    ((null lst) nil)
    ((equal student (caar lst))
      (rplacd (car lst) (cons (moyenne-aux (cdar lst) (list-length (cdar lst))) nil)) 
      (change student (cdr lst)) )
    (t (rplaca lst nil) (change student (cdr lst))) ) )


(setq étudiants '((étudiant1 (cours1 . 2) (cours2 . 2)) (étudiant2 (cours1 . 3)) (étudiant3 (cours1 . 4) (cours2 . 5))))
(print (moyenne-aux (cdar étudiants) (list-length (cdar étudiants))))

(trace change)
(change 'étudiant1 étudiants)
(print étudiants)
```

## 2016 Epreuve 8

1. Soient deux listes plates, la première étant une liste de produits et la seconde une liste de prix.

Liste_produits : (produit produit ... produit)

Liste_prix : (prix prix ... prix)

Créer une fonction "associer" qui construit récursivement une liste dont chaque élément est un doublet ; le premier élément de ce doublet est le produit et le deuxième le prix. Il faut définir la fonction associer :

(associer Liste_produits Liste_prix)
=> ((produit . prix) (produit . prix) ... (produit . prix))

On laisse tomber les produits qui n'ont pas de prix et les prix qui n'ont pas de produits.

```lisp
(defun combine (lst1 lst2)
  (list (rplacd (list (car lst1)) (car lst2))) )
  ;(list (cons (car lst1) (car lst2)))

(defun associer (lst1 lst2 &optional (result))
  (cond
    ((or (atom lst1) (atom lst2)) result)
    ((associer (cdr lst1) (cdr lst2) (append result (combine lst1 lst2))) ) ) )

(setq liste_produits '(a b c d))
(setq liste_prix '(1 2 3 4))
(associer liste_produits liste_prix)

;2 

(defun associer (Liste_produits Liste_prix)
   (cond 
      ((or (atom Liste_produits) (atom Liste_prix)) nil)
      (t 
         (cons  
            (cons (car Liste_produits) (car Liste_prix))
            (associer (cdr Liste_produits) (cdr Liste_prix))) ) ) )

(setq Liste_produits '(a b c d e))
(setq Liste_prix '(1 2 3 4 5))
(associer Liste_produits Liste_prix)

```

2. Créer une fonction chirurgicale récursive qui fait la même chose mais en modifiant directement Liste_produits :

(modifier Liste_produits Liste_prix)
=> ((produit . prix) (produit . prix) ... (produit . prix))

Liste_produits : ((produit . prix) (produit . prix) ... (produit . prix))

Astuce : utiliser rplaca.

```lisp
(defun combine (lst1 lst2)
  (cons (car lst1) (car lst2)) )

(defun modifier (lst1 lst2)
  (cond
    ((or (null lst1) (null lst2)) nil)
    ((rplaca lst1 (combine lst1 lst2))
      (modifier (cdr lst1) (cdr lst2)) ) ) )

(setq liste_produits '(a b c d))
(setq liste_prix '(1 2 3 4))

(print (modifier liste_produits liste_prix))
(print liste_produits)

```

## 2016 Epreuve 9

On part d'un arbre contenant des produits groupés par catégories et dont les feuilles contiennent des prix ; par exemple :

Arbre_produits :
`((fruit (pomme (golden 3.40) (boskop 2.5)) (raisin (blanc (muscat 4)... ) (noir (chasselas 5.5)))) (légume (carotte 2)...) )`

Créer une liste des produits dont le prix est inférieur à une certaine valeur :

(inférieur 4 Arbre_produits) => ((golden 3.40) (boskop 2.5) (carotte 2) ...)

```lisp

(defun F (num L)
   (cond
      ((atom L) nil)
      ((listp (car L))
         (append (F num (car  L)) (F num (cdr  L))) )
      ((and (numberp (cadr L)) (< (cadr L) num))
         (cons (cons (car L) (cons (cadr L) nil)) (F num (cdr  L))))
      ((F num (cdr  L))) ) )

(setq arbre '((fruit (pomme (golden 3.40) (boskop 2.5)) (raisin (blanc (muscat 4) ) (noir (chasselas 5.5)))) (légume (carotte 2)) ) )
(F 4 arbre)
```

## 2016 Epreuve 10

à partir d'une liste associant les ressources produites par un pays
donné et les quantités produites, d'une liste des cours des ressources
en euros (les ressources du pays sont inclues dans celles des cours,
mais l'inverse n'est pas vrai)

Pays : ((ressource1 quantité1) (ressource2...) ... (ressourceN...))
Cours : ((ressource1 cours1)... (ressourceM coursM))

faire une fonction récursive qui réunit ces informations pour une
ressource donnée :
(cours ressource Pays Cours) = (ressource quantité cours)

```lisp
(defun cours (ressource Pays Cours)
   (cond
      ((atom Pays) nil)
      ((equal
            ressource
            (caar Pays) )
         (cons
            (caar Pays)
            (cons
               (cadar Pays)
               (cons
                  (cadar Cours)
                  nil ) ) ) )
      (t (cours ressource (cdr Pays) (cdr Cours))) ) )

(setq Pays '((res1 10) (res2 20) (res3 30)))
(setq Cours '((res1 1000) (res2 2000) (res3 3000)))
(setq ressource 'res2)

(print (cours ressource Pays Cours))
=> (res2 2000)
```

faire une fonction récursive qui fait la même chose pour toutes les
ressources disponibles dans le pays :

(cours-ressources Pays Liste)
= ((ressource1 quantité1 cours1)... (ressourceN quantitéN))

```lisp
(setq Pays '((res1 10) (res2 20) (res3 30)))
(setq Cours '((res1 1000) (res2 2000) (res3 3000)))

(defun cours-ressources (Pays Cours)
   (cond
      ((atom Pays) nil)
      (t (cons
            (cons
               (caar Pays)
               (cons (cadar Pays) (cons (cadar Cours) nil)) )
            (cours-ressources (cdr Pays) (cdr Cours)) ) ) ) )

(cours-ressources Pays Cours)
=> ((RES1 10 1000) (RES2 20 2000) (RES3 30 3000)) 
```

## 2016 Epreuve 11

à l'aide d'une fonction chirurgicale récursive, modifiez la liste
obtenue en 1.2 pour remplacer les quantités et les cours de chaque
ressource par sa valeur (déterminée par le produit du cours par la
quantité)

(richesses liste) => ((ressource1 valeur1)... (ressourceN valeurN))

```lisp
(defun richesses (liste)
   (cond
      ((atom liste) nil)
      (t (rplacd
            (car liste)
            (cons (* (cadar liste) (caddar liste)) nil) )
         (richesses (cdr liste)) ) ) )

; (setq liste '((res1 10 1000) (res2 20 2000) (res3 30 3000)))
; (richesses liste)
; liste
```