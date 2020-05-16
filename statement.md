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

def millerTest(a,d,n,r):
    # test de Miller pour un témoin a
    # Retourne faux si n est composé et vrai si n est probablement premier
    # d et r doivent vérifier n = 2^r * d + 1 avec d impair   
           
    x = pow(a, d, n) # Calcule a^d % n   
    if (x == 1  or x == n-1): 
       return True

    for _ in range(r):    
        x = (x * x) % n 
        if (x == 1):
            return False 
        if (x == n-1):
            return True    
   
    return False 

def isPrime(n, k=25): 
    # Test de primalité de Miller Rabin  
    # Si faux alors n est composé et si vrai alors n est probablement premier 
    # k determine le niveau de certitude : P(erreur) < 1/4**k
    
    if (n <= 1 or n == 4):
        return False 
    if (n <= 5):
        return True   
    
    # Trouver d et r tels que n = 2^r * d + 1 avec d impair 
    d = n - 1 
    r = 0
    while (d&1 == 0): 
        d  >>= 1 
        r += 1 
    
    # Effectuer k tests de Miller
    for i in range(k):
        a = randint(2,n-2) 
        if (not millerTest(a, d, n, r)):
              return False  
    return True

def nextPrime(n):
    # premier suivant n
    while not isPrime(n):
        n += 1
    return n

def pollard(n,a=1,c=3):
    # Recherche un diviseur de n
    # Cet algorithme est un mélange des méthodes rho et p-1 de Pollard
    # Echec,si la valeur retournée est n 
    if n&1==0: #pair
        return 2
    b,g=a,1   
    while g==1:
        a = (a*a+1)%n
        b = (b*b+1)%n
        b = (b*b+1)%n
        c = pow(c,a,n)
        g = gcd(n, ((c-1)*abs(b-a))%n)
    
    #print('Polard rho et p-1 : n iter', i, 'g=', g)
    return g

def rho(n,a=2):
    # Recherche un diviseur de n
    # Cet algorithme est une variante de la méthodes rho de Pollard
    # Echec,si la valeur retournée est n   
    x=a  
    g=k=p=1
    i=0    
    while g==1:
        a = (a*a+1)%n
        f = abs(a-x)
        if f==0 or i%k==0:
            g = gcd(n,p)
            if f==0:
                break
            k<<=1
            x = a
               
        i += 1
        p *= f
        p %=n
        if p==0 and g==1:
            g= gcd(f,n)
                
    #print('Rho : n iter', i, 'g=', g)
    return g

def factor(n, k=25, div=rho):
    # Décompose n en facteurs probablement premiers
    # div = pollard ou rho
    comp = pow(2,n,n)!=2 # n est composé si vrai
    if n==2 or (n<561 and not comp): # n est un premier < 561 
            return [n]
    if not comp and isPrime(n,k): # n est probablement premier
        return [n]
    if n&1==0: # n est pair
        return [2]+factor(n>>1)
    for p in [3,5,7,11,13,17,19]:
        if n%p==0: #n est divisible par un petit premier
            return [p]+factor(n//p)
    f=div(n) # f est un facteur de n   
    while f==n: # tant qu'on n'a pas de facteur strict
        # on essaye avec d'autres valeurs
        f=div(n,a=randint(0,n-1))                   
    return factor(f)+factor(n//f)

    
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

