<div align="center">Telecomunicazioni – A.A. 2021/22</div>
<div align="center">Ing. Informatica e Automatica</div>
<div align="center">Homework 2 (9/12/2021)</div>
<div align="center">Gruppo: Iaconis, Lapi, Zingaretti</div>

<img src="/Users/mattiazingaretti/Downloads/IMG_1584.jpg" alt="IMG_1584" style="zoom:24%;" />



- Per rispettare la specifica di indipendenza delle tre zone di rete abbiamo impostato su ciascun router di frontiera (r5 ed r6) due configurazioni di OSPF e RIP, ognuna riferita ad una coppia di interfacce, in modo che zebra/quagga invocasse due demoni riferiti ognuno all'area corretta. 

- Abbiamo verificato che non venissero rilanciati messaggi dei protocolli di routing sulle LAN A,B,C,D tramite catture di traffico analizzate con WireShark. Non riportiamo le analisi di traffico per le LAN B e D poichè analoghe a quelle mostrate. 
  Catture riferite ad un ping da r4 ad h1: si nota che i messaggi del protocollo RIP arrivano all'interfaccia eth0 di r1 e non all'host h1 della LAN A.

  | <img src="/Users/mattiazingaretti/Library/Application Support/typora-user-images/image-20211211192055904.png" alt="IMG_1584" style="" /> | <img src="/Users/mattiazingaretti/Library/Application Support/typora-user-images/image-20211211192139732.png" alt="IMG_1584"/> |
  | ------------------------------------------------------------ | :----------------------------------------------------------: |
  | <img src="/Users/mattiazingaretti/Library/Application Support/typora-user-images/image-20211212172247128.png" alt="image-20211212172247128"  /> |                                                              |

  
  
  
  
  
  

  Catture riferite ad un ping da r4 ad h2: si nota che i messaggi del protocollo OSPF arrivano all'interfaccia eth1 di r7 e non all'host h2 della LAN C.
  
  | <img src="/Users/mattiazingaretti/Library/Application Support/typora-user-images/image-20211211193203024.png" alt="image-20211211193203024" /> | <br /><br /><img src="/Users/mattiazingaretti/Library/Application Support/typora-user-images/image-20211211193245100.png" alt="image-20211211193245100" /> |
  | :----------------------------------------------------------: | :----------------------------------------------------------: |
  
- Tramite l'uso del comando `redistribute ospf` nella configurazione del protocollo RIP nel router r5, le regole di instradamento dell'area OSPF vengono rese visibili ai router dell'area RIP1.
  Tramite l'uso del comando `redistribute rip` nella configurazione del protocollo OSPF nel router r5, le regole di instradamento dell'area RIP1 vengono rese visibili ai router dell'area OSPF (i). 
  Con queste scelte di configurazione dei protocolli il percorso seguito da r1 all'interfaccia eth0 di r7 (internet) è quello richiesto in quanto OSPF sceglie il cammino di costo minimo: 

  ```bash
  traceroute to 10.2.0.1 (10.2.0.1), 64 hops max
    1   10.1.0.6  0.054ms  0.060ms  0.057ms 
    2   10.2.0.1  0.058ms  0.068ms  0.046ms 
  ```

- Con le stesse scelte di configurazione dei protocolli (i) applicate su r6, il percorso seguito da r3 all'interfaccia eth0 di r8 (internet) è quello richiesto in quanto OSPF sceglie il cammino di costo minimo: 

  ```bash
  traceroute to 10.2.0.5 (10.2.0.5), 64 hops max
    1   10.1.0.26  0.043ms  0.044ms  0.041ms 
    2   10.2.0.5  0.041ms  0.036ms  0.034ms
  ```

- La comunicazione tra le zone RIP1 e RIP2 tramite il link r5-r6 è possibile: il percorso alternativo r5-r7-r8-r6 ha costo complessivo pari a 25 (su nostra scelta progettuale r7-r8 ha costo 5), quindi il protocollo di routing OSPF sceglie r5-r6 che ha costo pari a 10. In caso di guasto sul percorso a costo minimo, l'altro risulterà disponibile.

  ```bash
  traceroute to 10.1.0.29 (10.1.0.29), 64 hops max
    1   10.1.0.6  0.044ms  0.042ms  0.041ms 
    2   10.1.0.14  0.054ms  0.039ms  0.041ms 
    3   10.1.0.29  0.030ms  0.035ms  0.030ms 
  ```

- Gli host della zona RIP1 raggiungono la LAN C attraverso il percorso r5-r7 poichè il protocollo OSPF lo sceglie come percorso di costo minore:

  ```bash
  traceroute to 10.1.0.66 (10.1.0.66), 64 hops max
    1   10.1.0.6  0.044ms  0.036ms  0.035ms 
    2   10.1.0.18  0.041ms  0.061ms  0.059ms 
    3   10.1.0.66  0.035ms  0.033ms  0.035ms 
  ```

  

  

  

  
  
  
  
- Gli host della zona RIP1 raggiungono la LAN D attraverso il percorso r5-r7-r8 poichè il protocollo OSPF lo sceglie come percorso di costo minore dal momento che, per forzare il traffico nella direzione richiesta, abbiamo diminuito il costo del link r7-r8 rispettivamente sulle interfacce eth1 di r7 e eth3 di r8 (ii):

  ```bash
  traceroute to 10.1.0.130 (10.1.0.130), 64 hops max
    1   10.1.0.6  0.041ms  0.074ms  0.078ms 
    2   10.1.0.18  0.038ms  0.036ms  0.037ms 
    3   10.1.0.38  0.036ms  0.033ms  0.034ms 
    4   10.1.0.130  0.038ms  0.083ms  0.030ms 
  ```

- Gli host della zona RIP2 raggiungono la LAN C attraverso il percorso r6-r8-r7 poiché il protocollo OSPF lo sceglie come percorso di costo minore rispetto a quello passante per r6-r5-r7 grazie alla stessa modifica specificata in (ii): 

  ```bash
  traceroute to 10.1.0.66 (10.1.0.66), 64 hops max
    1   10.1.0.26  0.039ms  0.042ms  0.041ms 
    2   10.1.0.22  0.040ms  0.041ms  0.049ms 
    3   10.1.0.37  0.030ms  0.046ms  0.035ms 
    4   10.1.0.66  0.037ms  0.032ms  0.028ms 
  ```

- Gli host della zona RIP2 raggiungono la LAN D attraverso il percorso r6-r8 poichè il protocollo OSPF lo sceglie come percorso di costo minore:

  ```bash
  traceroute to 10.1.0.130 (10.1.0.130), 64 hops max
    1   10.1.0.26  0.050ms  0.164ms  0.146ms 
    2   10.1.0.22  0.095ms  0.070ms  0.041ms 
    3   10.1.0.130  0.040ms  0.038ms  0.045ms 
  ```

- Il DR per la LAN 10.1.0.36/30 è r8; mostriamo l'output del comando `show ip ospf interface` eseguito sulla shell vty del daemon ospfd di r8 per l'eth3. Abbiamo eseguito lo stesso comando per il daemon ospfd di r7 e per eth1 risulta di conseguenza State Backup.
  
  | <img src="/Users/mattiazingaretti/Library/Application Support/typora-user-images/Schermata 2021-12-12 alle 16.27.56.png" alt="Schermata 2021-12-12 alle 16.27.56" /> | <img src="/Users/mattiazingaretti/Library/Application Support/typora-user-images/Schermata 2021-12-12 alle 16.31.19.png" alt="Schermata 2021-12-12 alle 16.31.19"  /> |
  | :----------------------------------------------------------: | :----------------------------------------------------------: |
  
  
  
- Abbiamo ricostruito la topologia della zona OSPF tramite i comandi `show ip ospf database` e `show ip ospf database router` eseguiti sul daemon ospfd di r5 sulla shell vty condivisa. Il primo comando mostra due tabelle, Router Link State e Net Link State, tramite le quali possiamo riconoscere i router presenti (ognuno identificato da un indirizzo Link ID) e le reti (di cui viene indicato il Designated Router). Il secondo comando mostra un elenco di associazioni tra le interfacce dei router e le sottoreti di cui fanno parte (identificate tramite l'interfaccia del DR). Sulla base di queste informazioni siamo in grado di ricostruire la topologia di questa sottorete: 

  <img src="/Users/mattiazingaretti/Library/Application Support/typora-user-images/Schermata 2021-12-12 alle 16.40.55.png" alt="Schermata 2021-12-12 alle 16.40.55" style="zoom:40%;" />

- Riportiamo la cattura di traffico su h1 eseguita in contemporanea alla simulazione di un guasto sul link r1-r5 tramite comando `ifconfig eth3 down` eseguito su r5. Si evidenzia che nell'istante di tempo 28.97 simuliamo la link failure che rende la destinazione del ping (h2) non raggiungibile fino all'istante 56.60 in cui h1 riceve la ICMP reply da h2 inoltrata tramite un nuovo percorso ricalcolato con RIP. Tempo di ripristino: 27.63
  
  | <img src="/Users/mattiazingaretti/Library/Application Support/typora-user-images/image-20211212114010169.png" alt="image-20211212114010169"  /> | <img src="/Users/mattiazingaretti/Library/Application Support/typora-user-images/image-20211212114052969.png" alt="image-20211212114052969"  /> |
  | :----------------------------------------------------------: | :----------------------------------------------------------: |
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  Riportiamo le interfacce attive di r5 al momento del guasto e la rotta ricalcolata dinamicamente dal  protocolllo RIP:
  
  <img src="/Users/mattiazingaretti/Library/Application Support/typora-user-images/Schermata 2021-12-12 alle 15.45.50.png" alt="Schermata 2021-12-12 alle 15.45.50" style="zoom:22%;" />
  
- Riportiamo la cattura di traffico su h1 osservata in contemporanea alla simulazione di un guasto del router r7 ottenuta tramite comando `docker stop containerid` (`containerid` è un placeholder per una stringa che identifica il container r7 ottenuta tramite `docker ps` )  eseguito sulla macchina che ha in esecuzione il lab. 
  Si evidenzia che dall'istante di tempo 102.51 h1 smette di ricevere ICMP reply; dall'istante 110.70 la destinazione diventa non raggiungibile fino all'istante 139.35 in cui h1 riceve nuovamente una reply da h3 inoltrata tramite un nuovo percorso calcolato con OSPF. Tempo di ripristino: 36.84
  
  | <img src="/Users/mattiazingaretti/Library/Application Support/typora-user-images/image-20211212115135949.png" alt="image-20211212115135949"/> | <img src="/Users/mattiazingaretti/Library/Application Support/typora-user-images/image-20211212115225235.png" alt="image-20211212115225235" /> |
  | :----------------------------------------------------------: | :----------------------------------------------------------: |
  
  

