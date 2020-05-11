# Bonjour!

Ce programme perment de décomposer un nombre naturel en facteurs premiers

```python runnable
from time import time
from random import randint


def gcd(a,b):
    # Calcule le PGCD de a et b
    while b>0:
        a,b=b,a%b      
    return a
    
def pollard(n,a=1,c=3):
    # Recherche un diviseur de n
    # Cet algorithme est un mélange des méthodes rho et p-1 de Pollard
    # n est probablement premier si la valeur retournée est n
    if n&1==0: #pair
        return 2
    b,g=a,1   
    while g==1:
        a = (a*a+1)%n
        b = (b*b+1)%n
        b = (b*b+1)%n
        c = pow(c,a,n)
        g = gcd(n, ((c-1)*abs(b-a))%n)
    return g

def factor(n,k=25):
    # Décompose n en facteurs probablement premiers
    a=pollard(n)
    if a==n: # pas de factorisation     
        comp = pow(2,n,n)!=2 # n est composé
        if a==2 or (a<500 and not comp): # les premiers < 500 
            return [a]
        i=0
        while not comp and i<k: # on recherche d'autres composés 
            i += 1
            c=randint(2,n-1)
            if pow(c,n-1,n) !=1: #critère de Lucas
                comp=True
                break
        if comp: # on sait que n n'est pas premier
            while a==n: # tant qu'on n'a pas de facteur
                a=pollard(n,a=randint(0,n-1),c=randint(2,n-1)) # on essaye avec d'autres valeurs
        else: # n est probablement premier
            return [a]
    return factor(a)+factor(n//a)

def isPrime(n):
    # test de primalité
    return len(factor(n))==1
    
def nextPrime(n):
    # premier suivant n
    while not isPrime(n):
        n += 1
    return n
    
a=24769492052181606629
t=time()
f=factor(a)
t=time()-t
print('Les facteurs de ', a, 'sont :', f)
print('temps :',t) 
for n in f:
    if isPrime(n):
        print(n, 'est premier')
    else:     
        print(n, "n'est pas premier") 
```

# Exercice

Factoriser a=2001734177509214969467

