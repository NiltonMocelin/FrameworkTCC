## Topologia
* domínio 1: root1-c1, s1
* domínio 1: root2-c2, s2

[root1-c1]      [root2-c2]
    |               |
    s1 ------------s2
    /               \
 h1                 h4

 * Em cada teste fechar a topologia e abrir novamente:

# Para realizar as medições:
* Executar a topologia correspondente
`sudo python topo5_v2.py`

* Executar o script de configuração das filas (switchQueueConf2.sh)
`sh switchQueueConf2.sh`

* Um tcpdump na interface de h1
* Um tcpdump na interface de h4
* Um tcpdump na interface do controlador mais distante (último a processar)
* Executar os controladores semPrints e coletar os valores obtidos

`h1: python contrato_cli_v2.py 10.10.10.1 172.16.10.1 172.16.10.4 1000 2 2
`root1-c1: ryu-manager c1_v2_semPrints.py --ofp-tcp-listen-port 7000
`root2-c2: ryu-manager c2_v2_semPrints.py --ofp-tcp-listen-port 6699
`h1: ping -c 1 172.16.10.4`

* Espera expirar as regras dos switches

`h1: ping -c 1 172.16.10.4`

# Rodada 1:
* Tempo settar switches:
c1: 7ms
c2: 6ms

# Tempo para estabelecer um contrato entre h1-h4
* Tempo processamento c1:
server-host (receber contrato de h1+criar regras+icmp15):
- T1 = tempo consumido no tratamento do packet_in (para a volta da comunicacao controlador->host)
- T2 = tempo de processamento ouvindo a conexão e recebendo o contrato.
- TT = T1 + T2
- TT = 0 + 2 = 2ms 


pkt_in_icmp16 (envia contrato para c2): 
- TT = 1ms + 6ms = 7ms

* Tempo processamento c2:
pkt_in_icmp15 (receb icmp15 e respondido icmp16):
TT = 2ms

server-controlador (receber contrato de c1):
TT = 1ms

* Tempo Total de processamento:
(soma dos processamentos de c1 e c2)
TP = tproc_c1 + tproc_c2
TP = 2ms + 6ms + 2ms + 1ms = 11ms

* Tempo total para setup de contratos:
T1 = primeiro pacote saindo de h1
T2 =  tempo que o primeiro pacote de c1 chega em c2 + processamento para receber o contrato e criar as regras em c2 (server-control)/ 1000 (milliseconds to seconds)
TT = T2 - T1 

T1 = 11:30:06.319490
T2 = 11:30:06.343645 + 1/1000
TT = 6.343645 + 1/1000 - 6.319490 = 0.02515s = 25.15ms

# Tempo para enviar um pacote icmp entre h1-h4 com regras ativas
* Não tem processamento, pois não ocorre packet_in
(tempo do icmp chegar em h4 - tempo do icmp sair de h1)
T1 = tempo do icmp sair de h1
T2 = tempo do icmp chegar em h4
TT = T2 - T1
TT=  0.618259 - 0.618098 = 0.16ms


# Tempo para enviar um pacote icmp entre h1-h4 sem regras ativas (packet_in)
* Tempo processamento c1:
T1 = packet_in: tempo consumido no tratamento do packet_in, encontrando match, criando regras e enviando icmp 15:
TT = 2ms

* Tempo processamento c2:
T1 = pkt_in_icmp15:
T2 = tempo consumido no tratamento do packet_in, encontrando match e criando regras:
TT (soma) = 1ms + 1ms + 1ms = 3ms

Tempo Total de processamento:
TT = tproc_c1 + tproc_c2
TT = 3ms + 2ms = 5ms

* Tempo Total:
T1 = tempo quando o icmp sai de h1
T2 = tempo quando o icmp chega em h4
TT = T2 - T1
TT =  5.191604 - 5.180901 = 10.7ms

--------------------------------------------------------

# Rodada 2:
* Tempo settar switches:
c1 = 6ms
c2 = 6ms

# Tempo para estabelecer um contrato entre h1-h4
* Tempo processamento c1:
server-host (receber contrato de h1+criar regras+icmp15):
- T1 = tempo consumido no tratamento do packet_in (para a volta da comunicacao controlador->host)
- T2 = tempo de processamento ouvindo a conexão e recebendo o contrato.
- TT = T1 + T2
- TT = 0ms + 1ms = 1ms


pkt_in_icmp16 (envia contrato para c2): 
- TT = 2ms + 6ms = 8ms

* Tempo processamento c2:
pkt_in_icmp15 (receb icmp15 e respondido icmp16):
TT = 1ms

server-controlador (receber contrato de c1):
TT = 2ms

* Tempo Total de processamento:
(soma dos processamentos de c1 e c2)
TP = tproc_c1 + tproc_c2
TP = 1ms + 8ms + 1ms + 2ms = 12ms

* Tempo total para setup de contratos:
T1 = primeiro pacote saindo de h1
T2 =  tempo que o primeiro pacote de c1 chega em c2 + processamento para receber o contrato e criar as regras em c2 (server-control)/ 1000 (milliseconds to seconds)
TT = T2 - T1 

T1 = 4.252295
T2 = 4.274497 + 2/1000
TT = 4.274497 + 2/1000 - 4.252295 = 0.0242s = 24.20ms

# Tempo para enviar um pacote icmp entre h1-h4 com regras ativas
* Não tem processamento, pois não ocorre packet_in
(tempo do icmp chegar em h4 - tempo do icmp sair de h1)
T1 = tempo do icmp sair de h1
T2 = tempo do icmp chegar em h4
TT = T2 - T1
TT=  6.615034 - 6.614879 = 0.0001s = 0.15ms


# Tempo para enviar um pacote icmp entre h1-h4 sem regras ativas (packet_in)
* Tempo processamento c1:
T1 = packet_in: tempo consumido no tratamento do packet_in, encontrando match, criando regras e enviando icmp 15:
TT = 2ms

* Tempo processamento c2:
T1 = pkt_in_icmp15:
T2 = tempo consumido no tratamento do packet_in, encontrando match e criando regras:
TT (soma) = 1ms + 1ms = 2ms

Tempo Total de processamento:
TT = tproc_c1 + tproc_c2
TT = 4ms

* Tempo Total:
T1 = tempo quando o icmp sai de h1
T2 = tempo quando o icmp chega em h4
TT = T2 - T1
TT = 1.825296 - 1.813994 = 0.01130s = 11.3ms

--------------------------------------------------------

# Rodada 3: - eliminada - 
* Tempo settar switches:
c1 = 5ms
c2 = 5ms

# Tempo para estabelecer um contrato entre h1-h4
* Tempo processamento c1:
server-host (receber contrato de h1+criar regras+icmp15):
- T1 = tempo consumido no tratamento do packet_in (para a volta da comunicacao controlador->host)
- T2 = tempo de processamento ouvindo a conexão e recebendo o contrato.
- TT = T1 + T2
- TT = 1 + 61 = 62ms (?)


pkt_in_icmp16 (envia contrato para c2): 
- TT = 2 + 11 = 13ms

* Tempo processamento c2:
pkt_in_icmp15 (receb icmp15 e respondido icmp16):
TT = 2ms

server-controlador (receber contrato de c1):
TT = 1ms

* Tempo Total de processamento:
(soma dos processamentos de c1 e c2)
TP = tproc_c1 + tproc_c2
TP = 62ms + 13ms + 2ms + 1ms =78ms

* Tempo total para setup de contratos:
T1 = primeiro pacote saindo de h1
T2 =  tempo que o primeiro pacote de c1 chega em c2 + processamento para receber o contrato e criar as regras em c2 (server-control)/ 1000 (milliseconds to seconds)
TT = T2 - T1 

T1 = 8.744482
T2 = 8.832433 + 1/1000 
TT = 8.832433 + 1/1000 - 8.744482 = 0.08895s = 88.95ms

# Tempo para enviar um pacote icmp entre h1-h4 com regras ativas
* Não tem processamento, pois não ocorre packet_in
(tempo do icmp chegar em h4 - tempo do icmp sair de h1)
T1 = tempo do icmp sair de h1
T2 = tempo do icmp chegar em h4
TT = T2 - T1
TT=  3.808394 - 3.808227 = 0.0001s = 0.16ms


# Tempo para enviar um pacote icmp entre h1-h4 sem regras ativas (packet_in)
* Tempo processamento c1:
T1 = packet_in: tempo consumido no tratamento do packet_in, encontrando match, criando regras e enviando icmp 15:
TT = 0ms

* Tempo processamento c2:
T1 = pkt_in_icmp15:
T2 = tempo consumido no tratamento do packet_in, encontrando match e criando regras:
TT (soma) = 1ms + 2ms = 3ms

Tempo Total de processamento:
TT = tproc_c1 + tproc_c2
TT = 3ms

* Tempo Total:
T1 = tempo quando o icmp sai de h1
T2 = tempo quando o icmp chega em h4
TT = T2 - T1
TT = 0.828369 - 0.811664 = 0.0167s = 16.7ms

--------------------------------------------------------

# Rodada 4:
* Tempo settar switches:
c1 = 3ms
c2 = 6ms

# Tempo para estabelecer um contrato entre h1-h4
* Tempo processamento c1:
server-host (receber contrato de h1+criar regras+icmp15):
- T1 = tempo consumido no tratamento do packet_in (para a volta da comunicacao controlador->host)
- T2 = tempo de processamento ouvindo a conexão e recebendo o contrato.
- TT = T1 + T2
- TT = 1ms + 2ms = 3ms


pkt_in_icmp16 (envia contrato para c2): 
- TT = 3ms + 6ms = 9ms

* Tempo processamento c2:
pkt_in_icmp15 (receb icmp15 e respondido icmp16):
TT = 1ms

server-controlador (receber contrato de c1):
TT = 2ms

* Tempo Total de processamento:
(soma dos processamentos de c1 e c2)
TP = tproc_c1 + tproc_c2
TP = 3ms + 9ms +1ms + 2ms = 15ms

* Tempo total para setup de contratos:
T1 = primeiro pacote saindo de h1
T2 =  tempo que o primeiro pacote de c1 chega em c2 + processamento para receber o contrato e criar as regras em c2 (server-control)/ 1000 (milliseconds to seconds)
TT = T2 - T1 

T1 = 
T2 = 
TT = 1.063383 - 1.036308 + 2/1000 = 0.0290s = 29ms

# Tempo para enviar um pacote icmp entre h1-h4 com regras ativas
* Não tem processamento, pois não ocorre packet_in
(tempo do icmp chegar em h4 - tempo do icmp sair de h1)
T1 = tempo do icmp sair de h1
T2 = tempo do icmp chegar em h4
TT = T2 - T1
TT=  6.026082 - 6.025923 = 0.0001s = 0.15ms


# Tempo para enviar um pacote icmp entre h1-h4 sem regras ativas (packet_in)
* Tempo processamento c1:
T1 = packet_in: tempo consumido no tratamento do packet_in, encontrando match, criando regras e enviando icmp 15:
TT = 2ms

* Tempo processamento c2:
T1 = pkt_in_icmp15:
T2 = tempo consumido no tratamento do packet_in, encontrando match e criando regras:
TT (soma) = 1ms + 1ms = 2ms

Tempo Total de processamento:
TT = tproc_c1 + tproc_c2
TT = 4ms

* Tempo Total:
T1 = tempo quando o icmp sai de h1
T2 = tempo quando o icmp chega em h4
TT = T2 - T1
TT = 0.423993 - 0.412973 = 0.0110s = 11.02ms

--------------------------------------------------------

# Rodada 5:
* Tempo settar switches:
c1 = 9ms
c2 = 5ms

# Tempo para estabelecer um contrato entre h1-h4
* Tempo processamento c1:
server-host (receber contrato de h1+criar regras+icmp15):
- T1 = tempo consumido no tratamento do packet_in (para a volta da comunicacao controlador->host)
- T2 = tempo de processamento ouvindo a conexão e recebendo o contrato.
- TT = T1 + T2
- TT = 1ms + 4ms = 5ms


pkt_in_icmp16 (envia contrato para c2): 
- TT = 2ms + 9ms = 11ms

* Tempo processamento c2:
pkt_in_icmp15 (receb icmp15 e respondido icmp16):
TT = 1ms

server-controlador (receber contrato de c1):
TT = 7ms

* Tempo Total de processamento:
(soma dos processamentos de c1 e c2)
TP = tproc_c1 + tproc_c2
TP = 5ms + 11ms + 1ms + 7ms = 24ms

* Tempo total para setup de contratos:
T1 = primeiro pacote saindo de h1
T2 =  tempo que o primeiro pacote de c1 chega em c2 + processamento para receber o contrato e criar as regras em c2 (server-control)/ 1000 (milliseconds to seconds)
TT = T2 - T1 

T1 = 0.724735
T2 = 0.750708 + 7/1000s
TT = 0.750708 + 7/1000 - 0.724735 = 0.0329s = 32.97ms

# Tempo para enviar um pacote icmp entre h1-h4 com regras ativas
* Não tem processamento, pois não ocorre packet_in
(tempo do icmp chegar em h4 - tempo do icmp sair de h1)
T1 = tempo do icmp sair de h1
T2 = tempo do icmp chegar em h4
TT = T2 - T1
TT=  5.429545 - 5.429385 =0.0001s = 0.16ms


# Tempo para enviar um pacote icmp entre h1-h4 sem regras ativas (packet_in)
* Tempo processamento c1:
T1 = packet_in: tempo consumido no tratamento do packet_in, encontrando match, criando regras e enviando icmp 15:
TT = 1ms

* Tempo processamento c2:
T1 = pkt_in_icmp15:
T2 = tempo consumido no tratamento do packet_in, encontrando match e criando regras:
TT (soma) = 1ms + 1ms = 2ms

Tempo Total de processamento:
TT = tproc_c1 + tproc_c2
TT = 3ms

* Tempo Total:
T1 = tempo quando o icmp sai de h1
T2 = tempo quando o icmp chega em h4
TT = T2 - T1
TT =  4.219057 - 4.208477 = 0.0105s = 10.58ms

--------------------------------------------------------

# Rodada 6:
* Tempo settar switches:
c1 = 8ms
c2 = 9ms

# Tempo para estabelecer um contrato entre h1-h4
* Tempo processamento c1:
server-host (receber contrato de h1+criar regras+icmp15):
- T1 = tempo consumido no tratamento do packet_in (para a volta da comunicacao controlador->host)
- T2 = tempo de processamento ouvindo a conexão e recebendo o contrato.
- TT = T1 + T2
- TT = 1ms + 1ms = 2ms


pkt_in_icmp16 (envia contrato para c2):  (demorou um pouco para criar as regras que gerou novos packet_ins)
- TT = 1ms + 2ms + 2ms + 1ms + 1ms =  7ms

* Tempo processamento c2:
pkt_in_icmp15 (receb icmp15 e respondido icmp16):
TT = 1ms

server-controlador (receber contrato de c1):
TT = 1ms

* Tempo Total de processamento:
(soma dos processamentos de c1 e c2)
TP = tproc_c1 + tproc_c2
TP = 2ms + 7ms + 1ms + 1ms = 11ms

* Tempo total para setup de contratos:
T1 = primeiro pacote saindo de h1
T2 =  tempo que o primeiro pacote de c1 chega em c2 + processamento para receber o contrato e criar as regras em c2 (server-control)/ 1000 (milliseconds to seconds)
TT = T2 - T1 

T1 = 7.125654
T2 = 7.148997 + 1/1000s
TT = 7.148997 + 1/1000 - 7.125654 = 0.02434s = 24.34ms

# Tempo para enviar um pacote icmp entre h1-h4 com regras ativas
* Não tem processamento, pois não ocorre packet_in
(tempo do icmp chegar em h4 - tempo do icmp sair de h1)
T1 = tempo do icmp sair de h1
T2 = tempo do icmp chegar em h4
TT = T2 - T1
TT=  3.860597 - 3.860444 = 0.00015s = 0.15ms


# Tempo para enviar um pacote icmp entre h1-h4 sem regras ativas (packet_in)
* Tempo processamento c1:
T1 = packet_in: tempo consumido no tratamento do packet_in, encontrando match, criando regras e enviando icmp 15:
TT = 2ms

* Tempo processamento c2:
T1 = pkt_in_icmp15:
T2 = tempo consumido no tratamento do packet_in, encontrando match e criando regras:
TT (soma) = 1ms + 2ms = 3ms

Tempo Total de processamento:
TT = tproc_c1 + tproc_c2
TT = 5ms

* Tempo Total:
T1 = tempo quando o icmp sai de h1
T2 = tempo quando o icmp chega em h4
TT = T2 - T1
TT =  1.513418 - 1.501479 = 0.0119s = 11.93ms

--------------------------------------------------------

# Rodada 7:
* Tempo settar switches:
c1 = 7ms
c2 = 6ms

# Tempo para estabelecer um contrato entre h1-h4
* Tempo processamento c1:
server-host (receber contrato de h1+criar regras+icmp15):
- T1 = tempo consumido no tratamento do packet_in (para a volta da comunicacao controlador->host)
- T2 = tempo de processamento ouvindo a conexão e recebendo o contrato.
- TT = T1 + T2
- TT = 1ms + 2ms = 3ms


pkt_in_icmp16 (envia contrato para c2): 
- TT = 1ms + 7ms= 8ms

* Tempo processamento c2:
pkt_in_icmp15 (receb icmp15 e respondido icmp16):
TT = 1ms

server-controlador (receber contrato de c1):
TT = 1ms

* Tempo Total de processamento:
(soma dos processamentos de c1 e c2)
TP = tproc_c1 + tproc_c2
TP = 12ms

* Tempo total para setup de contratos:
T1 = primeiro pacote saindo de h1
T2 =  tempo que o primeiro pacote de c1 chega em c2 + processamento para receber o contrato e criar as regras em c2 (server-control)/ 1000 (milliseconds to seconds)
TT = T2 - T1 

T1 = 4.333282
T2 = 4.359829 + 1/1000s
TT = 4.359829 + 1/1000 - 4.333282 = 0.0275s = 27.54ms

# Tempo para enviar um pacote icmp entre h1-h4 com regras ativas
* Não tem processamento, pois não ocorre packet_in
(tempo do icmp chegar em h4 - tempo do icmp sair de h1)
T1 = tempo do icmp sair de h1
T2 = tempo do icmp chegar em h4
TT = T2 - T1
TT=  1.478309 - 1.478155 = 0.000153s = 0.15ms


# Tempo para enviar um pacote icmp entre h1-h4 sem regras ativas (packet_in)
* Tempo processamento c1:
T1 = packet_in: tempo consumido no tratamento do packet_in, encontrando match, criando regras e enviando icmp 15:
TT = 2ms

* Tempo processamento c2:
T1 = pkt_in_icmp15:
T2 = tempo consumido no tratamento do packet_in, encontrando match e criando regras:
TT (soma) = 1ms + 1ms = 2ms

Tempo Total de processamento:
TT = tproc_c1 + tproc_c2
TT = 4ms

* Tempo Total:
T1 = tempo quando o icmp sai de h1
T2 = tempo quando o icmp chega em h4
TT = T2 - T1
TT = 7.948714 - 7.936074 = 0.0126s = 12.64ms

--------------------------------------------------------

# Rodada 8:
* Tempo settar switches:
c1 = 9ms
c2 = 10ms

# Tempo para estabelecer um contrato entre h1-h4
* Tempo processamento c1:
server-host (receber contrato de h1+criar regras+icmp15):
- T1 = tempo consumido no tratamento do packet_in (para a volta da comunicacao controlador->host)
- T2 = tempo de processamento ouvindo a conexão e recebendo o contrato.
- TT = T1 + T2
- TT = 1ms + 1ms = 2ms


pkt_in_icmp16 (envia contrato para c2): 
- TT = 1ms + 2ms + 1ms + 1ms = 5ms (demorou para criar as regras e gerou novos pktins)

* Tempo processamento c2:
pkt_in_icmp15 (receb icmp15 e respondido icmp16):
TT = 2ms

server-controlador (receber contrato de c1):
TT = 3ms

* Tempo Total de processamento:
(soma dos processamentos de c1 e c2)
TP = tproc_c1 + tproc_c2
TP = 10ms

* Tempo total para setup de contratos:
T1 = primeiro pacote saindo de h1
T2 =  tempo que o primeiro pacote de c1 chega em c2 + processamento para receber o contrato e criar as regras em c2 (server-control)/ 1000 (milliseconds to seconds)
TT = T2 - T1 

T1 = 9.725778
T2 = 9.746824 +  3/1000s
TT = 9.746824 +  3/1000 - 9.725778 = 0.024s = 24ms

# Tempo para enviar um pacote icmp entre h1-h4 com regras ativas
* Não tem processamento, pois não ocorre packet_in
(tempo do icmp chegar em h4 - tempo do icmp sair de h1)
T1 = tempo do icmp sair de h1
T2 = tempo do icmp chegar em h4
TT = T2 - T1
TT=  4.269806 - 4.269649 = 0.000156s = 0.15ms


# Tempo para enviar um pacote icmp entre h1-h4 sem regras ativas (packet_in)
* Tempo processamento c1:
T1 = packet_in: tempo consumido no tratamento do packet_in, encontrando match, criando regras e enviando icmp 15:
TT = 1ms

* Tempo processamento c2:
T1 = pkt_in_icmp15:
T2 = tempo consumido no tratamento do packet_in, encontrando match e criando regras:
TT (soma) = 1ms + 1ms = 2ms

Tempo Total de processamento:
TT = tproc_c1 + tproc_c2
TT = 3ms

* Tempo Total:
T1 = tempo quando o icmp sai de h1
T2 = tempo quando o icmp chega em h4
TT = T2 - T1
TT =  8.359441 - 8.350651 = 0.00879s = 8.79ms

--------------------------------------------------------

# Rodada 9:
* Tempo settar switches:
c1 = 8ms
c2 = 9ms

# Tempo para estabelecer um contrato entre h1-h4
* Tempo processamento c1:
server-host (receber contrato de h1+criar regras+icmp15):
- T1 = tempo consumido no tratamento do packet_in (para a volta da comunicacao controlador->host)
- T2 = tempo de processamento ouvindo a conexão e recebendo o contrato.
- TT = T1 + T2
- TT = 1ms + 3ms = 4ms


pkt_in_icmp16 (envia contrato para c2): 
- TT = 2ms + 8ms = 10ms

* Tempo processamento c2:
pkt_in_icmp15 (receb icmp15 e respondido icmp16):
TT = 2ms

server-controlador (receber contrato de c1):
TT = 5ms

* Tempo Total de processamento:
(soma dos processamentos de c1 e c2)
TP = tproc_c1 + tproc_c2
TP = 4ms + 10ms + 2ms + 5ms = 21ms

* Tempo total para setup de contratos:
T1 = primeiro pacote saindo de h1
T2 =  tempo que o primeiro pacote de c1 chega em c2 + processamento para receber o contrato e criar as regras em c2 (server-control)/ 1000 (milliseconds to seconds)
TT = T2 - T1 

T1 = 8.228088
T2 = 8.256951  + 5/1000s
TT = 8.256951  + 5/1000 - 8.228088 = 0.03386s = 33.86ms

# Tempo para enviar um pacote icmp entre h1-h4 com regras ativas
* Não tem processamento, pois não ocorre packet_in
(tempo do icmp chegar em h4 - tempo do icmp sair de h1)
T1 = tempo do icmp sair de h1
T2 = tempo do icmp chegar em h4
TT = T2 - T1
TT=  2.197006 - 2.196828 = 0.00017s = 0.17ms


# Tempo para enviar um pacote icmp entre h1-h4 sem regras ativas (packet_in)
* Tempo processamento c1:
T1 = packet_in: tempo consumido no tratamento do packet_in, encontrando match, criando regras e enviando icmp 15:
TT = 2ms

* Tempo processamento c2:
T1 = pkt_in_icmp15:
T2 = tempo consumido no tratamento do packet_in, encontrando match e criando regras:
TT (soma) = 1ms + 2ms = 3ms

Tempo Total de processamento:
TT = tproc_c1 + tproc_c2
TT = 5ms

* Tempo Total:
T1 = tempo quando o icmp sai de h1
T2 = tempo quando o icmp chega em h4
TT = T2 - T1
TT =  5.356080 - 5.344955 = 0.01112s = 11.1ms

--------------------------------------------------------

# Rodada 10:
* Tempo settar switches:
c1 = 4ms
c2 = 6ms

# Tempo para estabelecer um contrato entre h1-h4
* Tempo processamento c1:
server-host (receber contrato de h1+criar regras+icmp15):
- T1 = tempo consumido no tratamento do packet_in (para a volta da comunicacao controlador->host)
- T2 = tempo de processamento ouvindo a conexão e recebendo o contrato.
- TT = T1 + T2
- TT = 1ms + 2ms = 3ms


pkt_in_icmp16 (envia contrato para c2): 
- TT = 1ms + 5ms = 6ms

* Tempo processamento c2:
pkt_in_icmp15 (receb icmp15 e respondido icmp16):
TT = 1ms

server-controlador (receber contrato de c1):
TT = 1ms

* Tempo Total de processamento:
(soma dos processamentos de c1 e c2)
TP = tproc_c1 + tproc_c2
TP = 3ms + 6ms + 1ms + 1ms = 11ms

* Tempo total para setup de contratos:
T1 = primeiro pacote saindo de h1
T2 =  tempo que o primeiro pacote de c1 chega em c2 + processamento para receber o contrato e criar as regras em c2 (server-control)/ 1000 (milliseconds to seconds)
TT = T2 - T1 

T1 = 8.828583
T2 = 8.861659 + 1/1000
TT = 8.861659 + 1/1000 - 8.828583 = 34ms

# Tempo para enviar um pacote icmp entre h1-h4 com regras ativas
* Não tem processamento, pois não ocorre packet_in
(tempo do icmp chegar em h4 - tempo do icmp sair de h1)
T1 = tempo do icmp sair de h1
T2 = tempo do icmp chegar em h4
TT = T2 - T1
TT=  5.062727 - 5.062572 = 0.15ms


# Tempo para enviar um pacote icmp entre h1-h4 sem regras ativas (packet_in)
* Tempo processamento c1:
T1 = packet_in: tempo consumido no tratamento do packet_in, encontrando match, criando regras e enviando icmp 15:
TT = 1ms

* Tempo processamento c2:
T1 = pkt_in_icmp15:
T2 = tempo consumido no tratamento do packet_in, encontrando match e criando regras:
TT (soma) = 1ms + 1ms = 2ms

Tempo Total de processamento:
TT = tproc_c1 + tproc_c2
TT = 3ms

* Tempo Total:
T1 = tempo quando o icmp sai de h1
T2 = tempo quando o icmp chega em h4
TT = T2 - T1
TT = 9.235039 - 9.223555 = 0.01148s = 11.4ms

--------------------------------------------------------