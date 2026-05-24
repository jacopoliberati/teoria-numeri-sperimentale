# Esplorazioni Computazionali tra Strutture Additive e Moltiplicative: Dalle Partizioni ai Numeri Primi

![Python](https://shields.io) ![Math](https://shields.io) ![Status](https://shields.io)

---

## 1. Introduzione e Sintesi del Progetto
Questo progetto nasce da un approccio puramente sperimentale volto a esplorare le connessioni profonde tra le **strutture additive** degli interi (le partizioni) e le loro **proprietà moltiplicative** (i divisori e i numeri primi). 

Attraverso lo sviluppo autonomo di algoritmi in Python, la ricerca ha mappato in modo indipendente tre pietre miliari della teoria dei numeri:
1. La funzione di partizione classica $p(n)$.
2. La stabilizzazione dei coefficienti combinatori nelle partizioni a due variabili.
3. La funzione $\tau(n)$ (numero di divisori) e l'isolamento dei numeri primi tramite filtri ondulatori distruttivi.

---

## 2. Fase 1: Lo studio della Funzione di Partizione $p(n)$
Il primo obiettivo è stato mappare la funzione generatrice delle partizioni, intese come i modi in cui un intero può essere scomposto nella somma di interi positivi. 

### Il Codice di Partenza (Approccio Iterativo Polinomiale)
L'algoritmo sviluppato analizza i flussi e applica una derivazione discreta (`n1 - ff[-1]`) per estrarre con precisione i valori puri della serie $p(n)$, superando la naturale tendenza del sistema ad accumulare i dati in somme cumulative.

```python
def listapartizioni_p_pura(coll):
    ff = [0]
    gg = []
    s = range(0, coll + 1)
    for col in s:
        ln1 = []
        cont = 0
        n1 = 1
        a = range(0, col+1)
        for x in a:
            ln1 = [1] + ln1
        while cont + 1 <= col:
            ln2 = []
            cont = cont + 1
            for x in a:
                ln2 = [0] + ln2
            b = range(cont, col+1)
            for y in b:
                ln2[y] = ln2[y-cont] + ln1[y-1]
            n1 = n1 + ln2[-1]
            ln1 = ln2
        g1 = n1 - ff[-1]
        ff = ff + [n1]
        gg = gg + [g1]
    return gg

print("Primi 15 valori di p(n):", listapartizioni_p_pura(15))
```

### Rilevanza Teorica
L'algoritmo dimostra sperimentalmente come la derivata discreta applicata a una serie combinatoria possa isolare i coefficienti di espansione di Eulero, legati alla celebre funzione generatrice:

$$\sum_{n=0}^{\infty} p(n)x^n = \prod_{k=1}^{\infty} \frac{1}{1-x^k}$$

Producendo la sequenza classica: `1, 1, 2, 3, 5, 7, 11, 15, 22...`

---

## 3. Fase 2: La Scoperta della Stabilizzazione dei Coefficienti
Studiando il comportamento delle partizioni sotto restrizioni (mappando il numero di addendi dal più grande al più piccolo), l'indagine si è spostata su una matrice bidimensionale (catalogata storicamente nell'enciclopedia **OEIS come A259324**).

```python
def analizza_stabilizzazione(col):
    for hh in range(col+1):
        ln1, colf, cont, n1 = [], [], 0, 1
        a = range(0, hh+1)
        for x in a: ln1 = [1] + ln1
        while cont + 1 <= hh:
            ln2 = []
            cont = cont + 1
            for x in a: ln2 = [0] + ln2
            b = range(cont, hh+1)
            for y in b:
                ln2[y] = ln2[y-cont] + ln1[y-1]
            colf = colf + [ln2[-1]]
            ln1 = ln2
        print(f"hh = {hh:2d} -> {colf}")

analizza_stabilizzazione(6)
```

> [!NOTE]
> ### Fenomeno Osservato: Il Fronte Stabile
> Analizzando l'output generato dall'algoritmo da destra a sinistra (corrispondente all'analisi degli addendi dal più piccolo al più grande), si nota un eccezionale fenomeno di **cristallizzazione numerica**. Man mano che l'indice `hh` cresce, i primi termini della riga smettono di variare e rimangono congelati:
> * `hh = 4:` $[..., \mathbf{1, 1, 2, 3}, 5]$
> * `hh = 5:` $[..., \mathbf{1, 1, 2, 3, 5}, 7]$
> * `hh = 6:` $[..., \mathbf{1, 1, 2, 3, 5, 7}, 11]$
>
> **Conclusione Sperimentale:** I coefficienti limite che si stabilizzano convergono esattamente alla successione $p(n)$ originaria. La struttura bidimensionale collassa proiettando spontaneamente la serie fondamentale delle partizioni.

---

## 4. Fase 3: Inversione di Fase e Isolamento dei Numeri Primi
Introducendo un'interferenza distruttiva nell'algoritmo precedente (tramite l'inversione di segno `-ln2[y - cont]`), la matrice smette di accumulare dati crescenti e si trasforma in un filtro d'onda. La quasi totalità dei termini decade a zero, lasciando attivi solo i nodi corrispondenti ai **Numeri Pentagonali Generalizzati** determinati dalla legge:

$$g_k = \frac{k(3k - 1)}{2} \quad \text{per} \quad k = \pm 1, \pm 2, \pm 3 \dots$$

Sfruttando questa proprietà ondulatoria, è stato implementato un algoritmo in grado di calcolare la funzione $\sigma(n)$ (somma dei divisori) per via puramente additiva, isolando i **numeri primi** senza ricorrere a operazioni classiche di divisione testata.

```python
def setaccio_ondulatorio_primi(limite):
    sigma = [0] * (limite + 1)
    primi = []
    for n in range(1, limite + 1):
        somma_onda, k = 0, 1
        while True:
            g1 = k * (3 * k - 1) // 2
            g2 = -k * (3 * (-k) - 1) // 2
            segno = 1 if k % 2 != 0 else -1
            if n >= g1:
                somma_onda += segno * (n if (n - g1) == 0 else sigma[n - g1])
            if n >= g2:
                somma_onda += segno * (n if (n - g2) == 0 else sigma[n - g2])
            if n < g1 and n < g2: break
            k += 1
        sigma[n] = somma_onda
        if sigma[n] == n + 1: # Criterio di primalità aritmetica
            primi.append(n)
    return primi

print("Primi individuati con filtro ondulatorio:", setaccio_ondulatorio_primi(80))
```
### Evoluzione: L'Algoritmo Universale dei "Numeri Ennesimi"
Estendendo la classificazione combinatoria, è stato sviluppato un modello in grado di isolare le classi dei numeri non più in modo isolato, ma attraverso un parametro d'ordine dinamico. Definendo l'ordine $k$ (dove $k=1$ identifica i Primi, $k=2$ i Secondi, $k=3$ i Terzi), l'algoritmo interroga la densità della funzione $\tau(n)$ accoppiata al calcolo additivo di $\sigma(n)$, estraendo selettivamente le sequenze regolate da precise configurazioni di esponenti primari.


> [!IMPORTANT]
> ### Il Criterio di Primalità Ondulatoria
> L'algoritmo non esegue alcuna divisione per verificare la primalità. Sfrutta l'identità di ricorrenza dei numeri pentagonali applicata ai divisori storici del sistema. Un intero $n$ viene classificato come primo se e solo se l'interferenza risultante soddisfa perfettamente la condizione:
> $$\sigma(n) = n + 1$$

---

## 5. Fase 4: La Classificazione dei Divisori (I Numeri "Secondi" e "Terzi")
L'ultimo tassello espande la struttura moltiplicativa degli interi classificando i numeri in base al numero esatto di divisori (funzione $\tau(n)$ o **OEIS A000005**), formalizzando una gerarchia logica:
* **Numeri Primi:** 2 divisori complessivi ($1$ e se stesso).
* **Numeri Secondi:** 3 divisori complessivi (Quadrati perfetti di numeri primi, es: 4, 9, 25).
* **Numeri Terzi:** 4 divisori complessivi (Cubi di primi o prodotti di due primi distinti, es: 6, 8, 10).

L'algoritmo sviluppato esegue un setaccio autonomo ottimizzato limitando la computazione alla radice quadrata del target ($x^{0.5}$), per poi applicare la scomposizione combinatoria dei fattori primari.

```python
def classificazione_divisori_tau(fine):
    primi = [2]
    lista = range(2, fine + 1)
    e = 1
    for x in lista:
        f = x**0.5
        if f in primi: e = primi.index(f) + 1
        d = 0
        primi2 = primi[0:e]
        for y in primi2:
            if (x - (y * (x // y))) != 0: d = d + 1
            if d == e: primi = primi + [x]     

    f = [0]
    j = primi[-1]
    for x in lista:
        h = 1
        for y in primi:
            g = 1
            if x % y == 0:  
                while x % y == 0: 
                    x = x / y
                    g = g + 1
                h = h * g
            if x % y != 0 and y == j:
                f = f + [h-1]
    return f

print("Lista divisori extra (Tau - 1) da 2 a 20:", classificazione_divisori_tau(20))
```

---

## 6. Conclusioni e Sviluppi Futuri
Il progetto dimostra come la manipolazione di vettori dinamici in Python possa rivelare simmetrie matematiche profonde. Le partizioni additive contengono in nuce l'informazione strutturale necessaria a determinare la primalità dei numeri, evidenziando che l'addizione e la moltiplicazione sono collegate da stringenti leggi asintotiche.

I prossimi passi della ricerca mirano a ottimizzare la complessità computazionale del setaccio ondulatorio, al fine di testare la stabilità di queste strutture su scale numeriche di ordine crittografico.

