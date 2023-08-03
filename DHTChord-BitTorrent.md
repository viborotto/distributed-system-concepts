# Sistemas Distribuidos

## Capitulo - DHT-CHORD

Em sistemas distribuídos, uma overlay refere-se a uma rede virtual construída em cima da infraestrutura de rede física existente. Essa rede virtual é composta por nós (ou processos) distribuídos em diferentes máquinas, conectados por enlaces lógicos (ou links) que são independentes da topologia física da rede subjacente.

A criação da overlay é uma técnica utilizada para fornecer comunicação e serviços adicionais, muitas vezes para melhorar a escalabilidade, eficiência, resiliência ou funcionalidades específicas de um sistema distribuído. A overlay é construída em camadas acima da rede física e pode ser implementada por meio de software, usando protocolos de comunicação específicos para esse fim.

Existem vários tipos de overlays em sistemas distribuídos, alguns dos quais incluem:

Overlay de Rede: Neste tipo de overlay, os nós do sistema distribuído são conectados por meio de ligações lógicas que podem ser implementadas de várias maneiras, como anéis, árvores, grades ou redes peer-to-peer. Esse overlay é usado para melhorar a escalabilidade e a eficiência na comunicação entre os nós.



## Capitulo - BitTorrent

### Modelo Do Sistema BitTorrent

Problema: 
- A distribuicao de um conteudo estatico grande, a partir de uma fonte, para um grande numero de usuarios, tao rapido quanto possivel
- Usar somente a largura de banda de upload do emissor é muito caro e as vezes inviavel

Ideia de solucao:
- Utilizar a capacidade de upload dos que baixam(usuarios que fazem download)
- Criar oportunidades para intercambiar os dados entre os que baixam

*Componentes*
O sistema BitTorrent para distribuição de arquivos consiste em:


`Tracker`: responsavel por conhecer todos os nodes que estao compartilhando os arquivos. Conhecer o IP, arquivo que esta compartilhando.
- Utilizado para encontrar outros peers interessados no mesmo torrent
- Armazena as informações de contato do peers
- Utiliza o protocolo HTTP para comunicar-se com os peers
- É contatado quando o peer começa, atualiza, para ou baixa completamente o conteúdo
  - Notificacoes são realizadas a intervalos regulares pelos peers
  - O tracker possui um timeout para remover os peers que saíram
- Ponto único de falhas (Single Point of Failure – SPoF) - cliente-servidor
  - Alvo de ações judiciais (tracker do pirate bay foi eliminado)

`Servidor Web`: onde estao todos os arquivos de metadados .torrent, arquivo pequeno com informacoes que compoe o arquivo completo.
Sistema hibrido entre cliente-servidor e P2P
`Seeder`: Um peer que contém e publica o conteúdo (i.e.,original seed). Node que possui realmente o arquivo inteiro.  
`Leecher`: nodes que estao baixando e subindo informacoes(pecas) de arquivos, mas ainda nao possuem o arquivo completo.
Navegador Web para baixar os arquivos .torrent
`Uma aplicação` que permita gerenciar os arquivos torrent e
baixar o conteúdo na rede BitTorrent

*QUIZ:*
* Seeder: é um peer que possui o arquivo completo disponível, sua função é compartilhar com outros peers o arquivo, ou seja disponibilizar pecas do arquivo para upload. O leecher é um peer que ainda não possui o arquivo completo para compartilhar, apenas fazendo o download e upload do arquivo.
* Tracker: tem a função de apenas disponibilizar a lista de peers para a comunicação entre os peers em uma rede BitTorrent, além disso mantém informações sobre quais peers possuem partes do arquivo e auxilia na localização e conexão entre eles.
* Servidor web tem como função fornecer o arquivo de metadados .torrent para os peers, dessa forma os peers obtêm informações para se conectar aos trackers e fazerem o download do arquivo desejado.

*Funcionamento*

Arquivo Torrent: 
- Usado para encontrar o conteúdo (via tracker) e verificar a integridade
- Inclui hashes das peças: Conteúdo particionado em peças com os hash calculados
- Outras infos: tamanho das peças, nome do arquivo, URL do tracker, etc.

Protocolo para download:
- Os peers se comunicam utilizando o protocolo BitTorrent acima do TCP
- Os peers interessados no mesmo torrent formam uma rede overlay independente denominada swarm
- Uma vez que o peer baixa o conteúdo completo pode manter-se como seed (altruistic, leecher vira seeder e contribui para o compartilhamento) ou sair da rede (selfish) do swarm

*Estrategias do BitTorrent*

Tentar que a taxa de download de cada peer seja proporcional a sua taxa de upload(Total donwload = total upload)
- Ajuda a evitar os free-riders(os peers que somente querem baixar e nao fazer upload)
- Usar um método eficiente para a distribuição do arquivo (Tit-for-Tat)


*QUIZ:*
* *Tit-for-Tat*: serve para compartilhar dados com outros peers na mesma proporção em que recebe dados desses peers. Com isso há uma melhor colaboração entre os peers e equilíbrio de compartilhamento e recepção de dados.(reciprocidade)
- A melhor estratégia para o dilema do prisioneiro iterativo.
Basicamente o agente:
- a menos que seja provocado, coopera
- se provocado, retalia
- perdoa (esquece) rapidamente - _iterativo_
- sabe que terá várias chances de encontrar-se novamente com outros _por isso o “iterativo”_

*Funcionamento*
Divide o conteúdo em peças(cada peça é dividida em blocos), calcula os hashes para cada uma, e cria o arquivo torrent com os metadados
- Registra o torrent no tracker
- Inicia o aplicativo BitTorrent atuando como um original seed
- Publica o arquivo torrent em um servidor Web

Para registrar no Swarm
1. O peer encontra o arquivo de metadados torrent no servidor WWW
2. .torrent tem o tracker que tem a lista de peers no swarm - Corresponde ao Neighbor Set (vizinhos)
3. Note que o tracker é um componente (servidor) centralizado mas não está envolvido na transferência do conteúdo
4. Cada peer possui um neighbor set
   - Inicialmente obtido do tracker
   - Com tamanho máximo de 80
5. Cada peer abre uma conexão TCP com os vizinhos
   - Se quantidade de vizinhos < 20, pede ao tracker mais peers
   - Inicialmente abre 40 conexões
   - As outras 40 serão conexões advindas de outros peers

> É a melhor estrategia - Tit-for-tat?

* Rarest-first: nesta estratégia há um prioridade para o download das partes menos disponíveis de um arquivo, ou seja rara entre os peers, de forma a maximizar a distribuição dele na rede, aumentando a chance de obtê-lo por completo.


*Protocolo de mensagens BitTorrent*

_Informação da Peça (Bitfield)_
Após estabelecer a conexão, os peers realizam o shake hands e compartilham as peças que possuem através do BITFIELD. Peer A conhece tudo sobre o PeerB

_Conexões do peer_
- Para evitar o custo do handshake na abertura de uma conexão, o peer mantém a conexão aberta o máximo possível
- A mensagem KEEP_ALIVE é enviada cada 2 minutos
- Pela mesma conexao passar as pecas

- Após o intercâmbio de bitfield, ambos peers conhecem quais peças o outro possui, dois estados:
  - Peer A está interessado INTERESTED no peer B se A não tem peças de B
  - Peer A está NOT INTERESTED no peer B se A já tem as peças de B
- Quando um peer adquire uma nova peça, avisará a todos seus vizinhos enviando a mensagem HAVE, atualizando as infos no bitfield dos vizinhos

*Mecanismo de choking*
- Uma das ideias mais poderosas do BitTorrent é assegurar que os peers cooperem, eliminando os free-riders(TIT-FOR-TAT)
- Para isso, cada peer p sempre fará o unchoke de um número fixo de peers, NAO PODE SER TODOS OS PEERSM, PRECISA ESCOLHER, vai escolher o que mais cooperou, enviou mais pecas pra mim.
  - `Unchoke` é o permisso temporal de upload
  - `Choke` é a recusa temporal de upload
- O peer p fará o unchoke (upload) aos quatro leechers dos quais p baixou exitosamente peças
  - A decisão de choke/unchoke é realizada cada 10 segundos baseado no hanking de quem cooperou mais
  - Baseada na taxa de download avaliada nos últimos 30 segundos

- Na visão de um peer p - Choke Algorithm
- Passo 1: Peer p ordenar todos os peers interessados (INTERESTED) que fizeram o upload de ao menos 1 bloco nos últimos 30 segs.
  - Elimine os peers snubbed, i.e., que não enviaram nada durante os 30 segs.
- Passo 2: os três peers mais rápidos(que mais cooperaram) serão unchoked por p, permitir que baixem uma peca de mim peer p, e eu peer p faco o upload para eles.
  - Esses peers são denominados de Regular Unchoked (RU) peers
  - Envia a mensagem UNCHOKED para os RUs
- Passo 3: escolha aleatoriamente um peer que não está em RU para ser o peer optimistic unchoked (OU). Teste aleatorio a cada round
  - 3ª: se o OU está interessado, envie a mensagem UNCHOKED
  - Por que OU?
    -  Encontrar peers potencialmente mais rápidos
    -  Permitir aos peers que iniciaram (portanto com poucas peças) obter peças

> Ate agora vimos a estrategia de selecao do peer, agora veremos outra.

*Estrategia de selecao da peça:*
Conteudo de 1000 pecas, qual escolher?
- Um unchocked peer A envia uma mensagem REQUEST ao peer B por uma peça
  - O peer B envia uma mensagem PIECE com o bloco/peça como payload
  - Quando o peer A receber a peça, envia:
    - uma mensagem HAVE a todos os peers vizinhos para atualizar o bitfield
    - uma mensagem CANCEL a todos os peers vizinhos
  
> Tudo bem, mas qual peça o peer A deveria requisitar?

4 Estrategias:
1. Random first
   - No comeco o peer precisa de qualquer peca completa
   - Usado quanto tiver menos de 4 pecas completas
   - Importante para ter algo a ser usado no Tit-for-Tat
   - Seleciona aleatoriamente

2. Strict priority
   - Outros blocos da mesma peca e do mesmo peer(reuso do canal TCP), prioriza uma peca ate obte-la completa.
   - Ou seja, permite obter a peca completa o mais rapido possivel
   - Uma vez que um bloco for requisitado de uma peca, os que estao faltando sao requisitados com alta prioridade
  

3. Endgame mode
   - Broadcast por todos os blocos restantes
   - Usado no final do download do conteudo
   - quando esta faltando a ultima peca faz um broadcast
   - baixar as ultimas pecas o mais rapido possivel, procurando nos vizinhos

4. *Rarest first(MUITO IMPORTANTE)*
   - Obter pecas menos replicadas, deixando as mais comuns para depois
   - Menos baixada entre todos os vizinhos
   - Um peer conhece todas as pecas que foram baixadas pelos seus vizinhos, por meio de HAVE ou BITFIELD messages
   - O peer vai calcular quantas vezes foi baixada determinada peca
   - Considera a que foi menos baixada entre todas as pecas
   - Assuma a peça m com menor disponibilidade
     - Essa peça m será a rarest-piece
   - Ao pedir a peça m, prolonga-se a vida do conteúdo, reduzindo o risco que a peça vire extinta


> Qual é a capacidade e infraestrutura de um tracker?
Meta (anteriormente Facebook), apresentou em 2022 a modificação do protocolo BitTorrent para compartilhamento de arquivos entre seus servidores multimídia.

A ideia foi ter vários trackers, cada um responsável por um conjunto de peers, controlando exatamente o que cada peer deve baixar, quando baixar e de quem baixar.

A comunicação entre Tracker e Peer é realizada via RPC.

O sistema atende aproximadamente 10 milhões de clientes concorrentes e transfere 1 exabyte de informação por dia. Cada 

Tracker gerencia aproximadamente 100 mil peers.