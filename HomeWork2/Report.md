<div align="center">Telecomunicazioni – A.A. 2021/22</div>
<div align="center">Ing. Informatica e Automatica</div>
<div align="center">Homework 2 (9/12/2021)</div>
<div align="center">Gruppo: Iaconis, Lapi, Zingaretti</div>

<img src="/Users/mattiazingaretti/Downloads/IMG_1584.jpg" alt="IMG_1584" style="zoom:33%;" />



- Per rispettare la specifica di indipendenza delle tre zone di rete abbiamo impostato su ciascun router di frontiera (r5 ed r6) due configurazioni di OSPF e RIP, ognuna riferita ad una coppia di interfacce, in modo che zebra/quagga invocasse due demoni riferiti ognuno all'area corretta. 

- Abbiamo verificato che non venissero rilanciati messaggi dei protocolli di routing sulle LAN A,B,C,D tramite catture di traffico analizzate con WireShark. Non riportiamo le analisi di traffico per le LAN B e D poichè analoghe a quelle mostrate: 
  Catture riferite ad un ping da r4 ad h1: si nota che i messaggi del protocollo RIP arrivano all'interfaccia eth0 di r1 e non all'host h1 della LAN A.

  ![image-20211211192055904](/Users/mattiazingaretti/Library/Application Support/typora-user-images/image-20211211192055904.png)

  ![image-20211211192139732](/Users/mattiazingaretti/Library/Application Support/typora-user-images/image-20211211192139732.png)

  ![image-20211211192247958](/Users/mattiazingaretti/Library/Application Support/typora-user-images/image-20211211192247958.png)
  Catture riferite ad un ping da r4 ad h2: si nota che i messaggi del protocollo OSPF arrivano all'interfaccia eth1 di r7 e non all'host h2 della LAN C.

  ![image-20211211193203024](/Users/mattiazingaretti/Library/Application Support/typora-user-images/image-20211211193203024.png)

  ![image-20211211193245100](/Users/mattiazingaretti/Library/Application Support/typora-user-images/image-20211211193245100.png)

- Tramite l'uso del comando `redistribute ospf` nella configurazione del protocollo RIP nel router r5, le regole di instradamento dell'area OSPF vengono rese visibili ai router dell'area RIP1.
  Tramite l'uso del comando `redistribute rip` nella configurazione del protocollo OSPF nel router r5, le regole di instradamento dell'area RIP1 vengono rese visibili ai router dell'area OSPF. (i)
  Con queste scelte di configurazione dei protocolli il percorso seguito da r1 all'interfaccia eth0 di r7 (internet) è quello richiesto in quanto ospf sceglie il cammino di costo minimo: 

  ```bash
  traceroute to 10.2.0.1 (10.2.0.1), 64 hops max
    1   10.1.0.6  0.054ms  0.060ms  0.057ms 
    2   10.2.0.1  0.058ms  0.068ms  0.046ms 
  ```

- Con le stesse scelte di configurazione dei protocolli (i) applicate su r6, il percorso seguito da r3 all'interfaccia eth0 di r8 (internet) è quello richiesto in quanto ospf sceglie il cammino di costo minimo: 

  ```bash
  traceroute to 10.2.0.5 (10.2.0.5), 64 hops max
    1   10.1.0.26  0.043ms  0.044ms  0.041ms 
    2   10.2.0.5  0.041ms  0.036ms  0.034ms 
  ```

  

