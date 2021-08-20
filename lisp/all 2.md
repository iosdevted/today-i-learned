# Advanced Grammar

## Quote

[When to use ' (or quote) in Lisp?](https://stackoverflow.com/questions/134887/when-to-use-or-quote-in-lisp)

## Macro

[10. 매크로 - ANSI Common Lisp 번역](http://onlisp.blogspot.com/2007/12/10-ansi-common-lisp.html)

[Chapter 3 - Essential Lisp in Twelve Lessons](https://netpyoung.github.io/external/successful_lisp_ch03_kor/)

## assoc (dictionnaire)

```lisp
(assoc <clé> <liste d'associations>) => <la sous-liste commençant par la clé>
```

S’il y a plusieurs sous-listes commençant par la même clé, `assoc` ramène la première : une liste d’associations est un cas particulier de pile.
La fonction acons permet d’y ajouter une clé avec sa valeur :

```lisp
(acons <clé> <valeur> <liste d'associations>) => <la liste complétée>
```

```lisp
(setq dico-anglais 
  '( (tea thé)
    (coffee café)
    (love aimer amour)
    (cloud nuage assombrir) 
    (turkey dinde)
    (book livre)
    (specially spécialement) ) )

(assoc 'cloud dico-anglais) => (cloud nuage assombrir)

(setq dico-anglais
  '( (tea (nom thé))
    (coffee (nom café))
    (love (verbe aimer) (nom amour)) 
    (cloud (nom nuage) (verbe assombrir))
    (take (verbe prendre))
    (specially (adverbe spécialement)) ) )

(cdr (assoc 'verbe (cdr (assoc 'cloud dico-anglais)))) => (assombrir)
```