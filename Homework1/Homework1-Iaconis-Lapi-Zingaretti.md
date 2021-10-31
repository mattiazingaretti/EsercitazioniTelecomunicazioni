Telecomunicazioni – A.A. 2021/22

Ing. Informatica e Automatica

Homework 1 (29/10/2021)

Gruppo Iaconis, Lapi, Zingaretti

1. Si dispone di un'insieme di indirizzi con netmask a 22 bit, dato che un blocco di indirizzi di classe C ha netmask a 24 bit abbiamo impiegato questi due bit di differenza (bit di supernetting per l'appunto) per individuare i 4 blocchi di indirizzi di classe C corrispondenti  [198.10.96.X/24 , 198.10.97.X/24 ,  198.10.98.X/24 , 198.10.99.X/24 ] . Ottenuti rispettivamente impostando i penultimi due bit del 3° ottetto con le configurazioni [00 , 01, 10 , 11].
   Con l'idea di effettuare SuperNetting su questi blocchi se necessario, abbiamo realizzato l'assegnazione dei prefissi alle varie LAN applicando la tecnica di subnetting. 

   | LAN                    |    Prefix     |     NetMask     | BroadCast Address | #Indirizzi Non utilizzati |
   | ---------------------- | :-----------: | :-------------: | ----------------- | ------------------------- |
   | LAN A                  |  198.10.99.0  |  255.255.255.0  |                   |                           |
   | LAN B                  | 198.10.98.64  | 255.255.255.192 |                   |                           |
   | LAN C                  | 198.10.98.128 | 255.255.255.128 |                   |                           |
   | LAN D                  |  198.10.97.0  |  255.255.255.0  |                   |                           |
   | LAN E                  | 198.10.96.192 | 255.255.255.224 |                   |                           |
   | LAN F                  | 198.10.96.224 | 255.255.255.240 |                   |                           |
   | LAN G                  | 198.10.98.32  | 255.255.255.224 |                   |                           |
   | LAN H                  |  198.10.98.0  | 255.255.255.252 |                   |                           |
   | LAN I                  | 198.10.96.248 | 255.255.255.252 |                   |                           |
   | LAN L                  | 198.10.96.240 | 255.255.255.252 |                   |                           |
   | LAN M (Emula Internet) | 198.10.100.0  | 255.255.255.252 |                   |                           |

   Per raggiungere l'obiettivo di minimizzare il numero di regole di instradamento nel router r1 abbiamo impiegato i blocchi .99 e .98 sul branch di sinistra (da r2 a seguire) in quanto condividono il 23° bit a 1 e ciò consente di ridurre di uno il numero di regole di instradamento su r1, scrivendo una sola regola con netmask di destinazione a 23 bit. La stessa logica è stata applicata sul branch di destra (da r3 a seguire) in quanto i blocchi .96 e .97 condividono il 23° bit a 0. 
   Inoltre abbiamo effettuato un assegnazione contigua di indirizzi alle varie LAN in modo da poter (nei limiti del possibile) riutilizzare o rendere disponibili gli indirizzi al variare della configurazione proposta lasciando meno "buchi" possibili.

   [TODO: ADD BLOCKS OF ADDRESSES AND TREE DIVISION OF SUBNETS]
   
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

   

4.

5.

6.