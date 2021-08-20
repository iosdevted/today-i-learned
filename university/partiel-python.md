# Préparation de partiel - Python

## How to save and read from a file

```python
def lire_adn(filename):
    with open(filename, 'r') as lecture:
        return(lecture.read())

print(lire_adn("ncov-s.txt"))
```

```python
f = open("demofile3.txt", "w")
f.write("Woops! I have deleted the content!")

f.write("{} : {}".format(key, temp))
f.write('\n')

f.close()
```

```python
durees = []

fp = open('trains.txt', 'r')
lines = fp.readlines()
for i in range(0, len(lines), 3):
    title = lines[i].strip()
    dt1 = lines[i+1].strip()
    dt2 = lines[i+2].strip()
    delta = calc_diff(dt2, dt1)
    durees.append((title, delta))
fp.close()

fp = open('durees.txt', 'w')
for title, secs in durees:
    fp.write("%s: %02d:%02d\n" % (title, secs // 3600, (secs % 3600) // 60))
fp.close()

```

```python
write = open("result.txt", "w")
with open("cryptogramme.txt") as f:
    text = f.read()
    write.write(dechiffre_cesar(text, 11))
    write.close()

with open("cryptogramme.txt", "r") as f :
    with open("fichier_de_mon_choix.txt", "w") as sortie :
        print(dechiffre_cesar(f.read(), 11), file=sortie)
```

## Replace only specific characters

```python
def transcrire_adn(seq):
    adn = list(seq)
    for i in range(0, len(adn)):
        if adn[i] == 'T':
            adn[i] = 'U'
    return("".join(adn))
    
transcrire_adn('AATGTCCAGTCAG')
# 'AAUGUCCAGUCAG'
```

## Rearrange the order of letters

```python
def decalage(distance):
    ALPHABET = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
    return ALPHABET[distance:] + ALPHABET[:distance]

print(decalage(1))
print(decalage(6))

#BCDEFGHIJKLMNOPQRSTUVWXYZA
#GHIJKLMNOPQRSTUVWXYZABCDEF
```

## Break the list by a certain number

```python
def arn_vers_codons(seq):
    codons = []
    for i in range(0, int(len(seq)/3)):
        codons.append(seq[i*3:i*3+3])
    return codons

arn_vers_codons('AAUGUCCAGUCAGUU')
#['AAU', 'GUC', 'CAG', 'UCA', 'GUU']
```

## Read and merge two lists at once

```python
def table_cesar(distance):
    ALPHABET = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
    ALPHABET2 = ALPHABET[distance:] + ALPHABET[:distance]
    result = {}
    for i, j in zip(ALPHABET, ALPHABET2):
        result[i] = j
    return result
```

## Find certain word in Dictionary

```python
def traduire_arn(seq):
    arn = arn_vers_codons(seq)
    result = []
    
    for i in arn:
        if codage.get(i) != None:
            result.append(codage[i])
        else:
            break
    return result
```

get을 사용하면 key에 해당하는 value 값을 찾을 수 있다.

## Dictionary

```python
inverse = {}

for key in codage:
    if codage[key] not in inverse:
        inverse[codage[key]] = [key]
    else:
        inverse[codage[key]].append(key)

for result in inverse:
    print("{} : {}".format(result, ", ".join(inverse[result])))

for key in inverse:
    temp = "" 
    for value in inverse[key]:
        temp = temp + value + ", "
    temp = temp[:-2]
    print("{} : {}".format(key, temp))
```

```python
dictionary[key] = value 
=> {key : value}
```

```python
dictionary[key].append(value2) 
=> {key : [value, value2]}
```

```python
def encode(txt, table):
    result = str()
    for char in txt :                         
        result += table.get(char, char)
        print(table)
    return result

encode('12 34', {'1': 'A', '2': 'B', '3': 'C', '4': 'D'})
```

get을 사용할 경우, 원하는 값을 찾지 못 하였을 경우, 다른 값을 넣어줄 수 있다.

## Python print format

```python
print("{} : {}".format(result, ", ".join(result2)))
print("clé", distance, ":", dechiffre_cesar(pl, distance))
```

### map

```python
def iso_to_tuple(dt):
    d, t = dt.split(' ')
    return tuple(map(int, d.split('-') + t.split(':')))

iso_to_tuple("2017-09-15 10:00:00") 
#(2017, 9, 15, 10, 0, 0)
```

```python
def myfunc(a):
  return len(a)

x = map(myfunc, ('apple', 'banana', 'cherry'))
#convert the map into a list, for readability:
print(list(x))
=> [5, 6, 6]
```

## Create Matrix Automatically

```python
for i in range(3):
    M.append(list(map(lambda x: 4 * i + x, range(1, 5))))

print(M)
M = [[1,  2,  3,  4],
     [5,  6,  7,  8],
     [9, 10, 11, 12]]
```

## Find a word in a matrix

```python
#search the word
def col(mat, j):
    result = []
    for i in range(len(mat)):
        result.append(mat[i][j])
    return result

print(col(M, 1))
```

## Matrice creuse

```python
# matrice creuse
def write_sparse_matrix(mat, filename):
    fp = open(filename, 'w')
    # lit les lignes de la matrice
    for row in mat:
        # formate les valeurs des colonnes differentes de zero
        line = ["%d:%g" % (j, val)  # ou : str(j)+':'+str(val)
                for j, val in enumerate(row)
                if val != 0]
        # ecrit le resultat
        fp.write(' '.join(line) + '\n')
    fp.close()

sM = [   # matrice creuse pour le test
 [8, 0, 5, 0, 0, 0, 9, 0, 0, 8],
 [0, 0, 9, 0, 6, 0, 0, 0, 2, 0],
 [0, 0, 0, 0, 0, 0, 0, 0, 0, 4],
 [1, 8, 0, 2, 1, 0, 0, 0, 0, 0],
 [0, 7, 0, 0, 0, 3, 0, 0, 0, 0]
]

# ecrit sM dans le fichier "sparse.mat"
write_sparse_matrix(sM, 'sparse.txt')
```

## Flip matrices

```python
M = [[1, 4, 7, 5], [8, 2, 3, 0], [9, 7, 5, 4]]

def transpose(mat):
    m = len(mat)    # nombre de lignes
    n = len(mat[0]) # nombre de colonnes
    # transpose les valeurs
    mt = [[mat[i][j] for i in range(m)]
          for j in range(n)]
    return mt

transpose(M)
#[[1, 8, 9], [4, 2, 7], [7, 3, 5], [5, 0, 4]]
```

## Find time difference

```python 
def calc_diff(dt1, dt2):
    t1 = iso_to_tuple(dt1)
    t2 = iso_to_tuple(dt2)
    s1 = t1[2] * 86400 + t1[3] * 3600 + t1[4] * 60 + t1[5]
    s2 = t2[2] * 86400 + t2[3] * 3600 + t2[4] * 60 + t2[5]
    return s1 - s2

calc_diff("2012-10-02 20:10:00", "2012-10-05 15:20:00")
```

[% format](https://python-reference.readthedocs.io/en/latest/docs/str/formatting.html)

## Flip natural numbers to form an array

```python
'''
자연수 n을 뒤집어 각 자리 숫자를 원소로 가지는 배열 형태로 리턴해주세요. 예를들어 n이 12345이면 [5,4,3,2,1]을 리턴합니다.

n		return
12345	[5,4,3,2,1]

'''

def digit_reverse(n):
    return list(map(int, reversed(str(n))))

print("result : {}".format(digit_reverse(12345)))
```

## Addition of matrices

```python
'''
행렬의 덧셈은 행과 열의 크기가 같은 두 행렬의 같은 행, 같은 열의 값을 서로 더한 결과가 됩니다.
 2개의 행렬 arr1과 arr2를 입력받아, 행렬 덧셈의 결과를 반환하는 함수, solution을 완성해주세요.



arr1			          arr2			          return
[[1,2],[2,3]]	   [[3,4],[5,6]]	   [[4,6],[7,9]]
[[1],[2]]		      [[3],[4]]		      [[4],[6]]
'''
def solution1(arr1, arr2):
    answer = [[c + d for c, d in zip(a, b)] for a, b in zip(A,B)]
    return answer

def solution2(arr1, arr2):
    answer = [[arr1[i][j] + arr2[i][j] for j in range(len(arr1[0]))] for i in range(len(arr1))]
    return answer
```

## Max, Min

```python
'''
문자열 s에는 공백으로 구분된 숫자들이 저장되어 있습니다.
 str에 나타나는 숫자 중 최소값과 최대값을 찾아 이를 (최소값) (최대값)형태의 문자열을 반환하는 함수, solution을 완성하세요.
예를들어 s가 1 2 3 4라면 1 4를 리턴하고, -1 -2 -3 -4라면 -4 -1을 리턴하면 됩니다.


s				return
1 2 3 4			1 4
-1 -2 -3 -4		-4 -1
-1 -1			-1 -1
'''

def solution(s):
    s=list(map(int,s.split(" ")))
    answer = "{} {}".format(min(s),max(s))
    return answer

```