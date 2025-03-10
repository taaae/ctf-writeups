# Sniping
**Category:** Crypto


<em>Steps:</em>\
<em>1. Google 'Coppersmith Attack'</em>\
<em>2. Profit!</em>

------------------------
### Description
<em>Is it enough ?</em>\
\
**`chall.py`**
```python
from Crypto.Util.number import *
flag=b"42HN{fake_flag}"
p = getPrime(512)
q = getPrime(512)
n = p*q
e=0x10001
c=pow(bytes_to_long(flag),e,n)

l = p % 2**420
print(f"n={n}\nc={c}\ne={e}\nl={l}")

#n=101812582570690800519192394713254551378111891796067265105819283896267289321245563904008008731127828513937127415528981620343374728168340568011738475655366742371069770617562409490904455318826750786399423721025615162128604999075265684722645154369827406821568206012905792300331624693020826395562540254077742740113
#c=17570828610060300776198001024407161762462100941259649360108337756017917528611464812184044459865276076184080855407956406696256195987472843420204248647756807657810959355095240424469866427593036276256435367188964402920220107303156481527556118096936776323654451231941366431013541160451268748610675786502113397402
#e=65537
#l=1939024888685079985050162492338049570241915769774700912942871310336836123420337712535971177443227862419040036094963732416104245
```

---------------------
### Solution
This is just a normal <span style="color:lime">**RSA**</span>, but we also have 420 right bits of <span style="color:lime">**P**</span>.\
Alright, lets ask ChatGPT if such an attack exists.\
\
<img width="600" alt="Screenshot 2025-03-02 at 20 42 20" src="https://github.com/user-attachments/assets/3fdb7c05-6541-4b48-a3df-149cbd4fa548" />

Cool! Now, lets find a <span style="color:lime">**Coppersmith**'s</span> attack implementation.
I used [this one](https://github.com/jvdsn/crypto-attacks/blob/master/attacks/factorization/coppersmith.py).\
By running the script we can recover <span style="color:lime">**P**</span> and <span style="color:lime">**Q**</span>:
```python
p, q = factorize_p(n, PartialInteger.lsb_of(l, 512, 420), m=6, t=6)
print(f"p={p}\nq={q}")
```
```python
p=10501574063276160405580832434192526608028764821796046523217117923466308226745476655027710250348929485124838651857425989961890544509057387075300064506480437
q=9694983052752806173990498360338339107170189004903988619042377405334000252898215954126965807852628132355664578274159913335030870560367712245485001933815149
```
Now we can easily decrypt the message:
```python
phi = (p - 1) * (q - 1)
d = pow(e, -1, phi)
m = pow(c, d, n)
long_to_bytes(m)
```
<em><span style="color:lime">b'42HN{C0pp3r5m1th_LsB}'<span style="color:lime">

GG!
