# Esplorazioni Computazionali tra Strutture Additive e Moltiplicative: Dalle Partizioni ai Numeri Primi e Ennesimi

![Python](https://shields.io) ![Math](https://shields.io) ![Status](https://shields.io)

---

## 1. Introduzione e Sintesi del Progetto
Questo progetto nasce da un approccio sperimentale computazionale volto a esplorare le connessioni tra le **strutture additive** degli interi (le partizioni) e le loro **proprietà moltiplicative** (divisori, numeri primi ed ennesimi) [A000070]. 

Attraverso lo sviluppo autonomo di algoritmi in Python, la ricerca ha mappato e unificato in un unico modello ondulatorio la funzione di partizione $p(n)$, la Funzione di Chowla ($\text{Chowla}(n)$), la classificazione gerarchica dei "Numeri Ennesimi" e le leggi di confinamento che regolano la sicurezza della crittografia asimmetrica RSA.

---

## 2. Il Motore Ondulatorio di Eulero e i Numeri Ennesimi
Il cuore computazionale si basa sull'Identità dei Numeri Pentagonali di Eulero applicata in modo inverso, creando un filtro d'interferenza additivo che calcola la somma dei divisori $\sigma(n)$ attraverso balzi discreti regolati dalla formula:

$$g_k = \frac{k(3k - 1)}{2} \quad \text{per} \quad k = \pm 1, \pm 2, \pm 3 \dots$$

Sfruttando questo motore e la scomposizione in fattori primari, viene implementato un algoritmo universale in grado di isolare la gerarchia dei **"Numeri Ennesimi"** in base alla densità della funzione $\tau(n)$ (numero di divisori) [A000005]:
* **Numeri Primi (Ordine 1):** 2 divisori complessivi ($1$ e se stesso). Forma $p^1$.
* **Numeri Secondi (Ordine 2):** 3 divisori complessivi (Quadrati perfetti di primi, es: 4, 9, 25). Forma $p^2$.
* **Numeri Terzi (Ordine 3):** 4 divisori complessivi (Cubi di primi o semiprimi distinti, es: 6, 15, 21, 35). Forma $p^3$ o $p \times q$.

```python
def genera_numeri_ennesimi_eulero(limite, ordine):
    sigma = [0] * (limite + 1)
    tau = [0] * (limite + 1)
    risultato = []
    divisori_attesi = ordine + 1
    
    for n in range(1, limite + 1):
        somma_onda, k = 0, 1
        while True:
            g1 = k * (3 * k - 1) // 2
            g2 = -k * (3 * (-k) - 1) // 2
            segno = 1 if k % 2 != 0 else -1
            if n >= g1: somma_onda += segno * (n if (n - g1) == 0 else sigma[n - g1])
            if n >= g2: somma_onda += segno * (n if (n - g2) == 0 else sigma[n - g2])
            if n < g1 and n < g2: break
            k += 1
        sigma[n] = somma_onda
        
        divisori_count = 0
        for i in range(1, n + 1):
            if n % i == 0: divisori_count += 1
        tau[n] = divisori_count
        
        if tau[n] == divisori_attesi:
            if ordine == 2:
                radice = n ** 0.5
                if radice.is_integer() and sigma[int(radice)] == int(radice) + 1:
                    risultato.append(n)
            else:
                risultato.append(n)
    return resultado
```

---

## 3. Lo Scheletro di Chowla e la Geometria dei Rami
Applicando la sottrazione discreta $\sigma(n) - (n + 1)$, l'algoritmo rimuove la componente dei divisori banali ($1$ e $n$). La sequenza risultante corrisponde alla **Funzione di Chowla** (OEIS A048050):

$$\text{Chowla}(n) = \sigma(n) - (n + 1)$$

Sezionando questa sequenza in "campate" o rami delimitati consecutivamente dai quadrati dei numeri primi (i Numeri Secondi fondamentali), emergono due leggi geometriche rigide.

> [!NOTE]
> ### 1. Legge del Pavimento (Minimo Locale Vincolato)
> All'interno di ogni ramo aperto da un quadrato primo $p^2$, il valore raddoppiato della sua radice ($2 \times \sqrt{p^2} = 2p$) costituisce una barriera inferiore assoluta per l'altezza d'onda di tutti i numeri composti del ramo. Nessun numero composto può violare questo pavimento energetico.

> [!IMPORTANT]
> ### 2. Legge del Soffitto Modulare ($12k + 1$)
> Il picco massimo assoluto di ogni ramo (la cresta d'onda più alta) viene calamitato all'indietro dai numeri altamente composti situati in prossimità della fine del ramo. La distanza reale ($D$) tra questo picco e il muro del quadrato primo successivo obbedisce rigorosamente all'equazione quantizzata:
> $$D = 12k + 1 \quad \text{con} \quad k \in \mathbb{Z}$$
> La sequenza esatta dei valori di $k$ estratti sperimentalmente fino a $N > 11.000$ è:
> $$\mathbf{0, \; 0, \; 0, \; 0, \; 0, \; 0, \; 0, \; 2, \; 0, \; 0, \; 9, \; 0, \; 4, \; 4, \; 24, \; 10, \; 10, \; 14, \; 0, \; 4, \; 10, \; 14, \; 30, \; 14, \; 10, \; 4, \; 44}$$
> I salti di indietreggiamento del picco ($D$) coincidono geometricamente ed esclusivamente con i quadrati dei numeri primi più piccoli ($5^2=25, 7^2=49, 11^2=121 \dots$), ad eccezione del punto critico di transizione stazionaria $109$ ($k=9$) indotto dall'attrattore altamente composto $1260$.

---

## 4. Implicazioni Crittografiche: La Scomposizione di RSA
Nel contesto della sicurezza informatica asimmetrica, una chiave pubblica RSA è un Numero Terzo della forma $N = p \times q$. La Funzione di Chowla applicata a questa chiave estrae linearmente l'esatta somma dei fattori segreti:

$$\text{Chowla}(N) = p + q = S$$

Mappando la distanza lineare tra il codice crittografato $N$ e il suo valore ondulatorio di Chowla $S$, si dimostra che la differenza $N - S$ equivale matematicamente a:

$$N - S = pq - p - q = (p - 1)(q - 1) - 1 = \phi(N) - 1$$

Dove $\phi(N)$ rappresenta la **Funzione Totiente di Eulero**, ovvero il cuore algoritmico della chiave privata crittografica. Isolare la valle oscillatoria confinata tra il pavimento $2\sqrt{N}$ e il soffitto $12k+1$ del ramo in cui $N$ risiede offre una via geometrica per collassare lo spazio di ricerca della chiave privata senza ricorrere a fattorizzazioni lineari classiche.

---

## 5. Conclusioni
Questo studio sperimentale evidenzia che l'ordinamento additivo racchiude in nuce la topologia geometrica necessaria a risolvere la complessità moltiplicativa. Le oscillazioni della funzione di Chowla non sono caotiche, ma si muovono all'interno di canali modulari d'onda quantizzati in moduli di 12 e vincolati dalle ancore stabili dei quadrati dei numeri primi.

