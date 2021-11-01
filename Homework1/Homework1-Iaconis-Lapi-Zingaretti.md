Telecomunicazioni – A.A. 2021/22

Ing. Informatica e Automatica

Homework 1 (29/10/2021)

Gruppo Iaconis, Lapi, Zingaretti

1. Si dispone di un'insieme di indirizzi con netmask a 22 bit, dato che un blocco di indirizzi di classe C ha netmask a 24 bit abbiamo impiegato questi due bit di differenza (bit di supernetting ) per individuare i 4 blocchi di indirizzi di classe C corrispondenti  [198.10.96.X/24 , 198.10.97.X/24 ,  198.10.98.X/24 , 198.10.99.X/24 ] . Ottenuti rispettivamente impostando i penultimi due bit del 3° ottetto con le configurazioni [00 , 01, 10 , 11].
   Con l'idea di effettuare SuperNetting su questi blocchi, se necessario, abbiamo realizzato l'assegnazione dei prefissi alle varie LAN applicando la tecnica di subnetting. 

   | LAN                    |    Prefix     |     NetMask     | BroadCast Address | #Indirizzi Non utilizzati |
   | ---------------------- | :-----------: | :-------------: | ----------------- | ------------------------- |
   | LAN A                  |  198.10.99.0  |  255.255.255.0  | 198.10.99.255     |                           |
   | LAN B                  | 198.10.98.64  | 255.255.255.192 | 198.10.98.128     |                           |
   | LAN C                  | 198.10.98.128 | 255.255.255.128 | 198.10.98.255     |                           |
   | LAN D                  |  198.10.97.0  |  255.255.255.0  | 198.10.96.255     |                           |
   | LAN E                  | 198.10.96.192 | 255.255.255.224 | 198.10.97.224     |                           |
   | LAN F                  | 198.10.96.224 | 255.255.255.240 | 198.10.97.240     |                           |
   | LAN G                  | 198.10.98.32  | 255.255.255.224 | 198.10.98.64      |                           |
   | LAN H                  |  198.10.98.0  | 255.255.255.252 | 198.10.98.3       |                           |
   | LAN I                  | 198.10.96.248 | 255.255.255.252 | 198.10.97.252     |                           |
   | LAN L                  | 198.10.96.240 | 255.255.255.252 | 198.10.97.244     |                           |
   | LAN M (Emula Internet) | 198.10.100.0  | 255.255.255.252 | 198.10.100.4ƒ     |                           |

   Per raggiungere l'obiettivo di minimizzare il numero di regole di instradamento nel router r1 abbiamo impiegato i blocchi .99 e .98 sul branch di sinistra (da r2 a seguire) in quanto condividono il 23° bit a 1 e ciò consente di ridurre di uno il numero di regole di instradamento su r1, scrivendo una sola regola con netmask di destinazione a 23 bit. La stessa logica è stata applicata sul branch di destra (da r3 a seguire) in quanto i blocchi .96 e .97 condividono il 23° bit a 0. 
   Inoltre abbiamo effettuato un'assegnazione contigua di indirizzi alle varie LAN in modo da poter (nei limiti del possibile) riutilizzare o rendere disponibili gli indirizzi al variare della configurazione proposta lasciando meno "buchi" possibili.

   **[TODO: ADD BLOCKS OF ADDRESSES AND TREE DIVISION OF SUBNETS]**
   
2. Le tabelle sono l'output del comando qui indicato, eseguito nei rispettivi router.

   ```shell
   $ route -n 
   ```

   Tabella di routing di r0:

   ```bash
   Kernel IP routing table
   Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
   198.10.96.0     198.10.96.242   255.255.254.0   UG    0      0        0 eth0
   198.10.96.240   0.0.0.0         255.255.255.252 U     0      0        0 eth0
   198.10.98.0     198.10.96.242   255.255.254.0   UG    0      0        0 eth0
   198.10.100.0    0.0.0.0         255.255.255.252 U     0      0        0 eth1
   ```

   Tabella di routing di r1:

   ```bash
   Kernel IP routing table
   Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
   0.0.0.0         198.10.96.241   0.0.0.0         UG    0      0        0 eth2
   198.10.96.0     198.10.96.250   255.255.254.0   UG    0      0        0 eth1
   198.10.96.240   0.0.0.0         255.255.255.252 U     0      0        0 eth2
   198.10.96.248   0.0.0.0         255.255.255.252 U     0      0        0 eth1
   198.10.98.0     0.0.0.0         255.255.255.252 U     0      0        0 eth0
   198.10.98.0     198.10.98.2     255.255.254.0   UG    0      0        0 eth0
   ```

   Tabella di routing di r2:

   ```bash
   Kernel IP routing table
   Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
   0.0.0.0         198.10.98.1     0.0.0.0         UG    0      0        0 eth2
   198.10.98.0     0.0.0.0         255.255.255.252 U     0      0        0 eth2
   198.10.98.32    0.0.0.0         255.255.255.224 U     0      0        0 eth1
   198.10.98.64    198.10.98.35    255.255.255.192 UG    0      0        0 eth1
   198.10.98.128   198.10.98.35    255.255.255.128 UG    0      0        0 eth1
   198.10.99.0     0.0.0.0         255.255.255.0   U     0      0        0 eth0
   ```

   Tabella di routing di r3:

   ```bash
   Kernel IP routing table
   Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
   0.0.0.0         198.10.96.249   0.0.0.0         UG    0      0        0 eth2
   198.10.96.192   0.0.0.0         255.255.255.224 U     0      0        0 eth1
   198.10.96.224   0.0.0.0         255.255.255.240 U     0      0        0 eth0
   198.10.96.248   0.0.0.0         255.255.255.252 U     0      0        0 eth2
   198.10.97.0     198.10.96.227   255.255.255.0   UG    0      0        0 eth0
   ```

   Tabella di routing di r4:

   ```bash
   Kernel IP routing table
   Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
   0.0.0.0         198.10.98.34    0.0.0.0         UG    0      0        0 eth2
   198.10.98.32    0.0.0.0         255.255.255.224 U     0      0        0 eth2
   198.10.98.64    0.0.0.0         255.255.255.192 U     0      0        0 eth0
   198.10.98.128   0.0.0.0         255.255.255.128 U     0      0        0 eth1
   ```

   Tabella di routing di r5:

   ```bash
   Kernel IP routing table
   Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
   0.0.0.0         198.10.96.194   0.0.0.0         UG    0      0        0 eth2
   198.10.96.192   0.0.0.0         255.255.255.224 U     0      0        0 eth2
   198.10.96.224   0.0.0.0         255.255.255.240 U     0      0        0 eth1
   198.10.97.0     0.0.0.0         255.255.255.0   U     0      0        0 eth0
   ```

   

3. Output del traceroute eseguito dal router r5 ad un host della LAN M (che emula Internet), ottenuto dall'esecuzione del seguente comando:

   ```shell
   $ traceroute 198.10.100.2
   ```

   ```bash
   traceroute to 198.10.100.2 (198.10.100.2), 64 hops max
     1   198.10.96.194  0.041ms  0.040ms  0.040ms
     2   198.10.96.249  0.069ms  0.034ms  0.041ms
     3   198.10.96.241  0.034ms  0.062ms  0.044ms
     4   198.10.100.2  0.035ms  0.036ms  0.039ms
   ```

   La regola di instradamento inserita nel router r5 è una regola di instradamento di default che vincola il traffico diretto verso la nostra LAN M a passare per l'interfaccia "eth1" del router r3.

   ```bash
   route add default gw 198.10.96.194 dev eth2 #for external  ip's and internet
   ```




4. Consideriamo la seguente configurazione di rete di h1 e r2 ottenuta tramite il comando:

   ```bash
   $ ifconfig
   ```

    Per h1 (omessa Loopback interface):

   ```bash
   eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
           inet 198.10.99.1  netmask 255.255.255.0  broadcast 198.10.99.255
           ether ba:58:9d:46:10:0a  txqueuelen 1000  (Ethernet)
           RX packets 18  bytes 1436 (1.4 KiB)
           RX errors 0  dropped 0  overruns 0  frame 0
           TX packets 0  bytes 0 (0.0 B)
           TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
   ```

   E per r2 (omessa interfaccia eth2 e lo ) :

   ```bash
   eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
           inet 198.10.99.2  netmask 255.255.255.0  broadcast 198.10.99.255
           ether 1a:10:8e:a7:04:2d  txqueuelen 1000  (Ethernet)
           RX packets 18  bytes 1436 (1.4 KiB)
           RX errors 0  dropped 0  overruns 0  frame 0
           TX packets 0  bytes 0 (0.0 B)
           TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
   
   eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
           inet 198.10.98.34  netmask 255.255.255.224  broadcast 198.10.98.63
           ether 66:5b:55:7f:06:99  txqueuelen 1000  (Ethernet)
           RX packets 18  bytes 1436 (1.4 KiB)
           RX errors 0  dropped 0  overruns 0  frame 0
           TX packets 0  bytes 0 (0.0 B)
           TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
   ```

   Effettuiamo poi un ping tra h1 e h2 attraverso il comando (eseguito su h1). :

   ```bash
   $ ping 198.10.98.65
   ```

   Osservando che le tabelle arp di h1 e r2 sono inizialmente vuote, catturiamo poi il traffico in r2 attraverso il comando:

   ```bash
   $ tcpdump -i eth0 -w r2Cap.pcap
   ```

   Riportiamo quindi i pacchetti catturati da Wireshark.

   ![
   ](/Users/mattiazingaretti/Library/Application Support/typora-user-images/Screenshot 2021-11-01 at 12.01.56.png)

   In seguito alla comunicazione le ARP table di h1 e r2 risultano così configurate:

   ARP table h1:

   ```bash
   Address                  HWtype  HWaddress           Flags Mask            Iface
   198.10.99.2              ether   1a:10:8e:a7:04:2d   C                     eth0
   ```

   ARP table r2:

   ```bash
   Address                  HWtype  HWaddress           Flags Mask            Iface
   198.10.99.1              ether   ba:58:9d:46:10:0a   C                     eth0
   198.10.98.35             ether   c2:2d:3b:3b:da:2a   C                     eth1
   ```

   Come si evince dal pacchetto numero 3, l'host h1 (con indirizzo MAC aa:bb:cc:dd:ee:0a ) invia in broadcast ,a livello 2, una ARP request per venire a conoscenza dell'indirizzo MAC del suo gateway (r2) che risponde in unicast con una ARP reply (pacchetto numero 4 sulla cattura di Wireshark ) contenente il suo indirizzo MAC ( aa:bb:cc:dd:ee:2d ).  Una volta terminata la procedura, la ARP table di h1 conterrà un nuovo record con IP di r2 sull'interfaccia eth0 e indirzzo MAc di r2. In questo modo r2 potrà indirizzare corretamente l'Echo request, secondo le sue regole di instradamento ,e consegnare il pacchetto a h2 che risponderà (dopo aver terminato se necessario la sua procedura ARP ) con una Echo reply.

5. 
   

   [TODO: add graph on OSI stack]

6.