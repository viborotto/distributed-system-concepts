# Sistemas Distribuidos

## Capitulo 1

### Conceitos Introdutorios
* [Definicao de Sistemas Distribuidos](#definicaoSD)
* [Overlay e Middleware]()
* [Objetivos de Sistemas Distribuidos]()
  * [Escalabilidade]()
  * [Problemas para obter escalabilidade]()
  * [Calculo back-of-the-envelope]()
* [Replicação e inconsistência]()
* Comunicação: 
  * [Síncrona]()
  * [Assíncrona]()
* Modelos de sistema
  * [Síncrono]()
  * [Assíncrono]()
  * [Parcialmente Síncrono]()

###  Conceitos Tipos de SD
* [Cluster, grade e nuvem]()
* [Transaçōes/ACID]()
* [Proxy]()
* [Sistemas móveis e rede de sensores]()


### Definicao de Sistemas Distribuidos

Um sistema distribuido é um **sistema de software** que garante uma `colecao de elementos de computacao autonomos` que sao vistos pelos usuarios como um sistema _unico e coerente_

def `colecao de elementos de computacao autonomos`: 
    - _Comportamento independente_ cada node e autonomo e portanto tem sua propria percepcao de tempo, <u>nao ha um relogio global</u>. 
    - Leva a problemas de sincronizacao e de coordenacao.
    - _Colecao de nos_: como gerenciar associacoes em grupos; como saber se voce realmente esta se comunicando com um nao membro autorizado do grupo, que seja confiavel em passar infos corretas e atualizadas.

#### Caracteristicas
* Elementos de computacao autonomos, chamados de nodos, seja dispositivos de hardware ou processos de software
* Sistema unico e coerente: ususarios ou aplicacoes veem um unico sistema(nodes colaboram entre si)


### Overlay e Middleware

#### Organizacao

**Redes de overlay**
Cada node na colecao se comunica apenas com nos do sistemas, seus vizinhos. O conjunto de vizinhos pode ser dinamico(saida e entrada de nos, dinamicamente) ou pode ser descoberto de forma implicita(nos ja comunicando, entra um nó que descobre a colecao por meio de um nó, e ai passa a fazer parte da colecao). `Nao necessariamente estao associados a estrutura fisica`, comunicacao logicamente entre os nos.

Esse overlay fica acima da camada fisica dos roteadores, sobreposta, podendo ser em formato de anel(Estruturadas) com nodes antecessores e sucessores.


**Tipos de overlay**
Um exemplo bem conhecido de redes de overlay: sistemas p2p. A falha de um peer nao compromete o funcionamento do sistema.

1. **Estruturada**
  Cada node tem um <u>conjunto bem definido</u> de vizinhos com os quais pode se comunicar(arvore, anel). Exemplo: Amazon Cloudfront - arvore

2. **Não Estruturada**
    Cada node tem referencias a um conjunto <u>aleatoriamente</u> selecionado de outros nos no sistema. Exemplo: Stremio


#### Sistema Coerente
A colecao de nos _opera sempre da mesma forma_, nao importando onde, quando ou como a interacao entre um usuario e o sistema acontece

- O usuario nao consegue dizer onde a computacao esta acontecendo(p.e quando vc usa o google vc nao sabe em qual maquina acontece, isso é transparente)
- Onde especificamente os dados estao armazenados deveria ser irrelevante para a aplicacao
- O dado ser ou nao replicado deveria estar completamente escondido
= `transparencia da distribuicao` = visao unica
- Exemplo ao conectar comm nos de um mesmo sistema nao pode ter uma informacao desartualizado, precisa estar coerente.

O problema: falhas parciais
É inevitavel que a qualquer momento _um pedaco do sistema distribuido falhe_. esconder essas falhas parciais e sua recuperacao normalmente é muito dificil(em geral, impossivel)

**Middleware: o SO dos SD**

`O que tem em um Middleware?`
Grosso modo, um conjunto de funções e componentes que não precisam ser reimplementados por cada aplicação separadamente.

* Ou seja uma camada que te prove servicos para aplicacoes independente do que essas fazem. Podendo ser servicos de seguranca, replicacao, essenciais para confiabilidade do sistema distribuido

### Objetivos de Sistemas Distribuidos

1. Disponibilizacao de recursos compartilhados
2. Transparencia de distribuicao: usuario nao quer saber onde esta
3. Abertura
4. Escalabilidade
   
#### 1. Disponibilizacao de recursos compartilhados

Exemplos:
- Compartilhamento de dados e arquivos na nuvem
- Streaming multimidia p2p - visualizar midia de forma sequencial - como se fosse YT
- Servicos de mensagens compartilhadas - Whatsapp
- Servicos de hospedagem web compartilhados - parecido com CloudFront Amazon
- Servicos de busca e processamento

#### 2. Transparencia de distribuicao

Um dos problemas mais complexos para desenvolvedores de SD.

**Tipos de Transparencia:**

* `Acesso`: Varios nodes, entao nao pode acontecer por exemplo de um node ter uma arvore de inteiro, e o outro uma arvore de float(o usuario nao precisa saber das diferencas entre as representacoes de dados, deve ser transparente). E nao interessa ao usuario, nao deve saber tambem COMO e ONDE esta sendo chamado os outros nodes, pois precisa ser um sistema unico(mecanismo de invocacao).
* `Localizacao`: Esconder onde o objeto esta localizado(exemplo URLm, vc nao sabe onde esta a pagina web)
* `Relocalizacao`: imagine 2 servidores e voce vai acessar os servidores utilizando o celular. Os servidores tem processos, por exemplo Processo1, Processo2, Processo3. E voce usa o processo3 do servidor, e comeca a ter problema de lentidao, ai vc consegue clonar o processo3 em outro servidor e de forma transparente o acesso ao cliente muda para o servidor novo com o mesmo processo trazendo mais velocidade de desempenho.(Esconde que um objeto pode ser movido para outra localidade enquanto esta sendo utilizado). Quem define é o SD /= de migracao
* `Migracao`: Esconder que um objeto pode ser movido para outra localidade. A diferenca entre Relocalizacao, é que aqui o cliente que define que o objeto deve ser migrado para outro local.
* `Replicacao`: Esconder uqe um objeto esta sendo replicado
* `Concorrencia`: Esconder que um objeto pode ser compartilhado entre diferentes usuarios independentes.
* `Falhas`: Esconder falhas e a possivel recuperacao de um objeto.


**Graus de transparencia**

Nao é possivel fazer um sistema totalmente transparente, pode ate ser um exagero as vezes. Exemplos:
- Usuarios podem estar localizados em continentes diferentes
- Esconder completamente as falhas da rede e dos nos é(teoricamente e na pratica) impossivel. Voce nao conseguira distinguir um computador lento de um que esta falhando. Voce nao consegue ter ctz que um servidor terminou de realizar uma operacao antes dele ter falhado.

<u>A transparencia completa tera um custo no desempenho, que ira expor a distribuicao do sistema.</u> 
* Manter caches web rigorosamente atualizados com o original
* Realizar flush das operacoes de escrita para garantir tolerancia a falhas

A transparencia pode ter algumas vantagens, _expor a distribuicao pode ser bom_:
- Fazer uso de servicos baseados na localizacao(encontrando os amigos mais proximos)
- Trabalhar com usuarios em diferentes time zones
- Quando é facil para um usuario entender o que esta acontecendo por exemplo falha no servidor pela conexao lenta

Podemos que a transparencia na distribuicao é um bom objetivo, mas atingir é uma questao bem diferente.

#### Abertura
Sistemas Distribuidos abertos sao capazes de interagir com outros sistemas abertos. Utilizando interfaces(possui o cabecalho somente, nao implementacao)

**Caracteristicas**
- devem respeitar interfaces bem definidas OK
- devem permitir a portabilidade de aplicacoes OK
- devem ser faceis de estender OK -> questoes de engenharia de software
- devem ser facilmente interoperaveis `NAO é facil de fazer`, temos o como fazer dos anteriores esse nao necessariamente

A abertuera dos sistemas distribuidos os tornam independentes de: hardware, plataformas, linguagens.

#### Escalabilidade

Obs: Muitos desenvolvedores de sistemas distribuídos modernos usam o adjetivo “escalável” sem deixar claro o porquê deles escalarem. 

| Manter o desempenho mesmo que o aumento de requisicoes seja grande.

1. **Tamanho, geografica, administrativa**
   
Escalabilidade se refere a pelo menos três componentes:
**a.** Número de usuários e/ou processos – **escalabilidade de tamanho**
**b.** Distância máxima entre nós, as vezes nao depende somente do numero de usuarios mas de onde eles se localizam – **escalabilidade geográfica**
**c.** Número de domínios administrativos, cada administrador tem suas politicas de acesso – **escalabilidade administrativa**

A maior parte dos servidores escalam apenas e ate certo ponto em tamanho, utilizando servidores poderosos (`scale-in`)

_Hoje em dia o maior desafio é conseguir a escalabilidade geografica e administrativa_


#### Problemas para obter escalabilidade

Por se tratar de um sistema descentralizado(SD), com nodes autonomos, acaba tendo a seguinte caracteristica:

- Nenhuma maquina tem informacao completa sobre o estado do sistema, informacao parcial
- Maquinas tomam decisoes baseadas apenas em informacao local/parcial
- Falhas em uma maquina nao devem arruinar a execucao do algortimo, ou seja mesmo cada maquina tendo uma informacao parcial se ha a replicacao quando falha uma todas as outras tem as informacoes replicadas
- Nao eh possivel assumir a existencia de um relogio global

  1. **Problemas na escalabilidade de tamanho**
    escalabilidade de tamanho: Numero de usuarios e/ou processos 

    * Capacidade computacional, limitada pelas CPUs
    * Capacidade de armazenamento e de transferência entre CPU e HD
    * Capacidade de largura de banda entre o usuário e o serviço(transferencia de arquivo)

    Se aumenta o numero de usuarios e atinge a maxima de CPU, da mesma forma o HD pode atingir 100% de uso.
    `Como estimar a capacidade?`

    **Back-of-the-Envelope estimation**
    Para estimar a capacidade e desempenho do sistema e entender quais serao os requisitos(vazao e armazenamento) a serem atendidos

    `Exemplo do Twitter para estimar`


  2. **Problemas na escalabilidade geografica**
    escalabilidade geográfica: Distância máxima entre nós

  * Não é possível só ir da LAN para WAN: muitos dos sistemas distribuídos assumem interações cliente-servidor com latências pequenas. WAN: tempos sao longos
  * Links das WAN são pouco confiáveis: e.g.mover dados de streaming de vídeo da LAN para WAN falhará.
  * O **broadcast**(LAN) da camada de rede não pode ser aplicado na WAN.


  3. **Problemas na escalabilidade administrativa**
   escalabilidade administrativa: Número de domínios administrativos
   * Políticas conflitantes a respeito do uso (e pagamento), gerenciamento e segurança.
   * Exemplos:
     * Compartilhamento de recursos caros entre diferentes domínios. -  Seguranca
     * Processamento de informações em diferentes lugares de forma confidencial.
     * Onde armazenar e para quem disponibilizar os dados mantendo políticas como LGPD.

2. **Tecnicas de Escalabilidade**
   Ideia geral: esconder latencia de comunicacao

   **a.** <u>Nao fique esperando por respostas, faça outra coisa</u>
    - use comunicacao _assincrona_ -> nao confundir modelo assincrono
    - use diferentes handlers para tratamento de mensagens(_multithread_)
    - `Problema`: nem toda aplicacao se encaixa nesse modelo

    **b.** <u>Particionamento de dados e computação em muitas máquinas</u>
    - Mova a computação para os clientes (ex: Javascript, Spark, etc.) Exemplo formulario verificacao do email, CPF feito no cliente, reduzindo o uso da CPU no servidor.
    - Serviços de nomes decentralizados (DNS) -  nome em endereco IP
    - Sistemas de informação decentralizados (WWW)
    - Microsserviços
    - Sharding (particionamento) de tabelas de bancos de dados ou de objetos DIFERENTE de replicacao/caching
    - `Escalabilidade Vertical/Horizontal(Scale-in/Scale-out)`

    **c.** <u>Replicacao/caching</u>
    Faça cópias dos dados e disponibilize-as em diferentes máquinas:
    - Bancos de dados e sistemas de arquivos replicados
    - Sites web “espelhados” - mesmo site em 2 maquinas
    - Caches web(nos navegadores)
    - Cache de arquivos(no servidor e nos clientes)
    - Cache de dados(nos servidores – memcached)


3. **Problemas Escalabilidade**
Aplicar técnicas para obtenção de escalabilidade é fácil, exceto por:
* Manter múltiplas cópias (em cache ou replicadas) leva a **inconsistências**: a modificação em uma cópia a torna diferente das demais durante um tempo
* Manter as cópias consistentes requer* *sincronização global** em cada modificação (mas não há um relógio global)
* Porem Sincronização global impossibilita soluções escaláveis


Porem, se pudermos tolerar inconsistências, poderíamos reduzir a dependência de sincronização globais, mas _tolerar inconsistências é algo que depende da aplicação_.

#### Modelos de Sistema

Não confundir com comunicação síncrona e assíncrona entre cliente-servidor.

1. **Sincrono**
- Os limites de tempo para transferir uma mensagem são conhecidos
- Os limites de tempo para processar uma ação são conhecidos
- `LAN/Datacenters`

2. **Assincrono**
- Sem limites de tempo para transferir uma mensagem
- Sem limites de tempo para processar uma ação
- `Internet`

3. **Parcialmente sincrono**
- Inicialmente o comportamento do sistema é assíncrono
- Eventualmente (sim ou sim) o comportamento será síncrono 
- `Internet`


#### Armadilhas no desenvolvimento de SDs
Muitos sistemas distribuídos se tornam desnecessariamente complexos por causa de “consertos” ao longo do tempo. Em geral, há muitas **hipóteses falsas**/nao presta atencao
- A rede é confiável
- A rede é segura
- A rede é homogênea
- A topologia da rede não muda
- A latência é zero
- Largura de banda é infinita
- O custo de transporte é zero
- A rede possui um administrador
  
#### Tipos de Sistemas Distribuidos

*Sistemas para computação distribuída de alto desempenho*

**a.** Cluster Computing (Aglomerados de computacao)      
Essencialmente um grupo de computadores de boa qualidade conectados via LAN, rede local.
  * Homogêneo: mesmo SO, hardware quase idêntico
  * Um unico node gerenciador do cluster(master node)
![Cluster Computing](URL da imagem)

**b.** Grade    
O próximo passo: vários nós vindos de todos os cantos.
  * Heterogeneos: diferente hardware, diferente SO
  * Espalhados entre diversas organizacoes ou pessoas/lugares
  * Normalmente formam uma rede de longa distancia(wide-area network) - WAN
  * Obs: Para permitir colaborações, grades normalmente usam _organizações virtuais_. Essencialmente, isso significa que os usuários (ou melhor, seus IDs) são organizados em grupos que possuem autorização para usar alguns recursos.
  ********[Imagem da arquitetura] - CAMADAS********

**c.** Computacao em Nuvem
-> Mistura de SD(grade, cluster), encapsula varios servidores virtuais que rodam por cima de infraestruturas reais. Cliente para acessar a nuvem usa o conceito de transparencia

* IaaS: cobre as camadas de hardware (escondida do cliente) e infraestrutura. 
* PaaS: cobre a camada de plataforma.
* SaaS: cobre a camada de aplicação.

Faz uma distincao entre quatro camadas
- `Hardware` processadores, roteadores, energia, sistemas de refrigeração - DATACENTERS
- `Infraestrutura` Utiliza técnicas de virtualização para alocação e gerenciamento de armazenamento e servidores virtuais - EC2
`Plataforma` Provê abstrações de alto nível para os serviços da plataforma. Ex: Amazon S3 para armazenamento de arquivos em buckets
`Aplicação` as aplicações propriamente ditas, tais como as suítes de aplicativos para escritórios. - Google Apps por exemplo

#### Sistemas de Informação Distribuídos

Uma quantidade enorme de sistemas em uso hoje em dia são formas de sistemas de informação tradicionais.
Geralmente são aplicações que se conectam à rede para intercambiar informações.

As palavras-chave que caracterizam os sistemas de informação distribuído são **transação** e **integração**. Sistemas transacionais que precisam ser integrados com outros nodes para compartilhar informacoes.

Nota: Transações formam uma <u>operação atômica</u>. Se cair em qualquer lugar: ABORT_TRANSACTION. Por exemplo venda casada hotel + passagem, se o hotel tem um banco de dados e passagem outro, e as informacoes nao tem integridade nas transacoes, ou seja nao batem, abortaria a transacao e retiraria de ambos os banco de dados.

**Transações**
Uma transação é um conjunto de operações sobre o estado de um objeto (banco de dados, composição de objetos, etc.) que satisfazem as seguintes propriedades (ACID):
- `Atomicidade` ou todas as operações são bem sucedidas, ou todas falham. Quando uma transação falha, o estado do objeto permanecerá inalterado. Depende do Framework ou sistema de gerenciamento do banco de dados.
- `Consistência` uma transação estabelece um estado de transição válido. Isto não exclui a existência de estados intermediários inválidos durante sua execução. Transacoes entre bancos, enquanto ta enviando um dinheiro, esse dinheiro esta invalido, ate ser processada a transacao. Depende do programador.
- `Isolamento` transações concorrentes não interferem entre si. Para uma transação T é como se as outrast ransações ocorressem ou antes de T, ou depois de T. Depende do Framework ou sistema de gerenciamento do banco de dados.
- `Durabilidade` Após o término de uma transação, seus efeitos são permanentes: mudanças de estado sobrevivem a falhas. Depende do Framework ou sistema de gerenciamento do banco de dados.

Tendo em vista isso como deveria ser feito o *ACID* em sistemas distribuidos?

_Monitor de Processamento de Transações_
Em muitos casos, o conjunto de dados envolvidos em uma transação está distribuído em vários servidores. Um `TP Monitor ` é responsável por _coordenar a execução de uma transação._

Imagine um cliente que quer usar um modelo transacional, uma transacao com varios outros servidores banco de dados. TP monitor, realmente monitora as requests.

| Application -Request-> TP monitor -Manda o Request-> Servidores(DB)

Esses servidores podem ser aplicacoes de diferentes empresas, por exemplop hotel e passagem

**Integracao de aplicacoes corporativas:**
Situacao: as organizacoes possuem diversas aplicacoes muitas delas sem interoperabilidade.

A solucao basica para isso seria o monitor comentado acima, que combinaria as requisicoes das aplicacoes em um servidor, quem realizara o envio e coleta das respostas, apresentando um resultado coerente para o cliente.

Mas as vezes precisamos fazer comunicacao diretas, sem um intermediario -> **Enterprise Application Integration** == MIDDLEWARE de comunicacao. Permitindo nao somenta a comunicacao de um cliente com os servidores, mas cliente entre cliente atraves do middleware. Exemplo: RPC, MOM(Middleware orientado a mensagens)

<u>Como integrar as apps?</u>

* Transferencia de arquivos: simples mas nao flexivel. Pode ter problema de consistencia, precisaria conhecer o formato do arquivo, propagacao e notificacoes de atualizacao.
* Banco de dados: bem mais flexivel, porem requer que haja um schema comum com riscos de gargalo.
* RPC: efetivo quando a execucao das acoes sao distribuidas, estando o requisitante e o requisitado online. Chamando o metodo diretamente, reutilizando o modelo de negocio.
********[IMAGEM RPC] - CAMADAS********

App -> metodo soma no import -> proxy(objeto tem o metodo soma dentro, mas sem implementar - casca de soma) -> acessa o computador que realmente tem o soma.
Sendo assim o cliente faz chamadas ao metodo como se estivesse sendo executado na maquina local, mas o metodo pode estar sendo executado em maquinas remotas.


#### Sistemas Pervasivos

Tendência em sistemas distribuídos; _nós são pequenos, móveis e normalmente embutidos em um sistema muito maior_. Associado com a Internet das coisas (IoT – Internet of Things).

Três tipos (com sobreposição, pode ter misturas), por exemplo o celular se encaixa nos 3 tipos:
1. <u>Sistemas ubíquos: continuamente presentes</u>
   **Requisitos:**
   -  `Mudança contextual`: o sistema é parte de um ambiente onde mudanças devem ser rapidamente levadas em consideração
   - `Composição ad hoc`: cada nó pode ser usado em diferentes maneiras, por diferentes usuários. Deve ser facilmente configurável.
   - `Compartilhar é o padrão`: nós vão e vêm, fornecendo serviços e informação compartilháveis. Pede simplicidade.
   - `Autonomia:` nós operam de forma autônoma sem intervenção humana.
   - `Inteligência:` pode emerger um comportamento inteligente dada a dinâmica e as interações dos nós.
   - Exemplos: sistemas domesticos, monitoramento de uma pessoa
  
2. <u>Sistemas móveis: inerentemente móvel</u>
Miríade de dispositivos móveis: smartphones, tablets, óculos AR/VR, etc.

**Caracteristicas**
- Comunicacao sem fio
- Mobilidade implica em que a localizacao do dispositivo mudara no tempo. DESCOBERTA
- A comunicacao pode ser dificil, pois nao ha uma rota estavel. Leva as redes tolerantes e disrupcoes

******video padroes de mobilidade slide 71***********
Existe algum relacionamento entre a disseminacao da informacao e a mobilidade das pessoa?
Ideia basica: posso fazer intercambio de informacoes por meio de enconstro(Pocket switched networks)
[?]

3. <u>Sistemas de sensores: sente e atúa no ambiente.</u>

**Caracteristicas redes de sensores**
- Os nodes aos quais os sensores estao presos sao:
  - Muitos sensores(10s - 1000s)
  - Simples(Pouca capacidade de memoria/computacao/comunicacao)
  - Normalmente necessitam de uma bateria
  


## Capitulo 2

### Conceitos
* [Arquitetura em camadas, objetos, eventos e recursos]()
* [Acoplamento na referência e no tempo]()
* [Arquitetura cliente-servidor]()
* [Arquitetura de 3 camadas]()
* [P2P (Peer-to-Peer)]()
* [Interceptor]()
* [Sistemas Distribuídos Autogerenciáveis]()


**Estilos Arquiteturais**
Um estilo é definido em termos de: componentes substituiveis com interfaces bem definidas; o modo como como os compoenentes sao conectados entre si; como os dados sao trocados entre componentes; como esses componentes e conectores sao configurados conjuntamente em um sistema.
`Conector`: um mecanismo que intermedia comunicacao, coordenacao ou cooperacao entre componentes. Exemplo: RPC, mensagem, streaming
1. Arquiteturas em camadas
2. Arquiteturas baseadas em objetos
3. Arquiteturas baseadas em eventos e dados
4. Arquiteturas centradas em recursos


#### Arquitetura em camadas, objetos, eventos e recursos.

1. **Arquiteturas em camadas**
- Ideia basica: organize os componentes `logicamente diferentes` e os distribua entre as maquinas disponiveis. Cada camada com sua responsabilidade
- Esse estilo é usado em sistemas cliente-servidor
- Outro exemplo poderia ser camada de IU, camada de processamento, camada de dados. Nao existe uma comunicacao direta entre IU e DB, interface bem definida que mantem uma ordem e organizacao das camadas.


2. **Arquiteturas baseadas em objetos**
- Ideia basica: organize os compodentes `logicamente diferentes` e os distribua entre as maquinas disponiveis.
- Estilo orientado a objetos usado em `sistemas de objetos distribuídos`.(Exemplo RMI)
- **Encapsulamento**: Os objetos ficam distribuídos pelo sistema (servidores). Apesar do usuário fazer chamadas que são equivalentes a chamadas locais, elas podem estar sendo feitas em objetos remotos(em outras maquinas)-> por meio de _Proxy_ que tem as informacoes basicas e necessarias para comunicacao com outra maquina.
- Comunicacao entre objetos por meio de conectores e interfaces bem definidas
- mais complexo que o de camadas que tem comunicacao unidirecional


3. **Arquiteturas baseadas em eventos e dados**
- Ideia basica: Desacoplar processos na referencia(anonimos, sem identificados especifico).
- Publish/subscribe[desacoplado na referencia], subscribes precisam estar online
- Uso de event bus que nao depende de IP e porta, tem seus proprios identificadores unicos.
- Espaço de dados compartilhados [desacoplado na ref. e tempo]


1. **Arquiteturas centradas em recursos - daos compartilhados**
Ideia basica: Desacoplar processos na referencia(anonimos, sem identificados especifico) e tempo(assincronos). Isso significa que a mensagem nao precisa que o subscribe esteja online, pois persiste o dado e aguarda no middleware ate que apareca alguem online.
Vê um sistema distribuído como uma coleção de recursos gerenciados individualmente por componentes. Recursos podem ser adicionados, removidos recuperados e modificado por aplicações remotas - REST.
- Recursos são identificados usando um único esquema de nomeação
- Todos os serviços oferecem a mesma interface
- Mensagens enviadas de ou para um serviço são auto-descritivas
- Após a execução de uma operação em um serviço,o componente esquece tudo sobre quem chamou a operação
- Operacoes basicas: PUT GET DELETE POST

| Operacao | Descricao |
|---|---|
| PUT | Cria um novo recurso |
| GET | recupera o estado de um recurso usando um tipo de representacao |
| DELETE | apaga um recurso |
| POST | modifica um recurso ao transferir um novo estado |

Exemplo: operacoes GET e PUT em um bucket S3

#### Arquiteturas de Software

1. Arquiteturas centralizadas
   **Caracteristicas modelo Cliente-Servidor**
   - Existem processos que oferecem servicos (`Servidores`)
   - Existem processos que usam esses servicos (`Clientes`)
   - Clientes e Servidores podem estar em maquinas diferentes
   - Clientes seguem um modelo de requisicao/resposta ao usar os servicos

2.  Arquiteturas multicamadas (multidivididas)
Visao tradicional em 3 camadas `logicas`
- A camada de apresentacao(user interface - UI) contem o necessario para a aplicacao poder interagir com o usuario
- A camada de negocio(application) contem as funcoes de uma aplicacao
- A camada de dados(database) contem os dados que o cliente quer manipular atraves dos componentes da aplicacao
- Estas camadas são encontradas em muitos sistemas de informação distribuídos, que usam tecnologias de bancos de dados tradicionais e suas aplicações auxiliares.

A configuracao mais tradicionar seria: 2 camadas fisicas(2 maquinas) + 3 logicas
* uma camada física: configurações de terminal burro/mainframe 
* duas camadas físicas: configuração cliente–servidor único.
* três camadas físicas: cada camada em uma máquina separada


3. Arquiteturas descentralizadas
- **P2P ESTRUTURADO**: os nós são organizados seguindo uma
estrutura de dados distribuída específica - Amazon DynamoDB
  - `ideia basica`: Organizar os nós em uma rede overlay estruturada, tal como um anel lógico, e fazer com que alguns nós se tornem responsáveis por alguns serviços baseado unicamente em seus Ids
  - Outra estrutura poderia ser nodes em um espaco d-dimensional e faca todos os nodes ficarem responsaveis por um dado em uma regiao especifica
  - O sistema provê uma operação _LOOKUP(key)_ que irá fazer o roteamento de uma requisição até o nó correspondente usando as conexões lógicas.
  - Node pode atuar tanto como cliente, quanto como servidor(chamado de peer)
  - [Exemplo na imagem do anel - lookup][?]


- **P2P NAO-ESTRUTURADO**: os nodes selecionam aleatoriamentes seus vizinhos - Skype
  - Muitos sistemas P2P não-estruturados tentam manter um `grafo aleatório`
  - Cada node deve contactar um outro node selecionado aleatoriamente:
    - Cada participante mantem uma visao parcial da rede consistindo de c outros nodes(denominados vizinhos)
    - Cada node P seleciona periodicamente um node Q de sua visao parcial [GOSSIP]
    - P e Q trocam informacoes e participantes de suas respectivas visoes parciais [GOSSIP]
    - Serve pra quando quer disseminar uma informacao, pois nao tem como buscar uma informacao de forma deterministica, mas sim aleatoria
  
  `Gossiping`: é um tipo de protocolo de comunicação em que os nós do sistema trocam informações entre si de forma aleatória e assíncrona. No entanto, o gossiping também pode ter algumas desvantagens, como a possibilidade de propagação de informações desnecessárias, o que pode aumentar o tráfego de rede. Portanto, é importante projetar cuidadosamente os algoritmos de gossiping e ajustar os parâmetros para equilibrar o desempenho e a eficiência do sistema. Além disso, é essencial garantir que as mensagens sejam transmitidas de forma confiável e que a consistência eventual seja alcançada para evitar problemas de sincronização e conflitos de dados.

  **Superpeers**
  - As vezes selecionar alguns nodes para realizar algum trabalho especifico pode ser util, responsabilidade maior. Exemplos:
    - Peers para manter um indice (para buscas)
    - Peers para monitoras o estado da rede
    - Peers capazes de configurar conexoes


4. Arquiteturas hibridas
   
- **P2P HIBRIDO**: alguns nós são designados, de forma organizada, para executar funções especiais - CloudFront, Stremio. Exemplo: Arquiteturas de servidores de borda (edge-servers), utilizados com frequência como Content Delivery Networks (redes de distribuição de conteúdo).
  - `Ideia basica`: Assim que um node indentifica de onde o arquivo sera baixado, ele se junta a uma swarm(multidao) de pessoas que, _em paralelo_, receberao pedacos do arquivo da fonte e redistribuirao esses pedacos entre os outros.

| Praticamente todos os casos são exemplos de redes overlay: dados são roteados usando conexões definidas pelos nós


#### Arquiteturas x Middleware

**Middleware:** é um tipo de software que atua como um intermediário ou "meio de campo" entre diferentes aplicativos ou sistemas. Sua principal função é facilitar a comunicação e a interação entre esses sistemas, permitindo que eles se conectem e troquem informações de maneira eficiente.

Os middlewares são amplamente utilizados em arquiteturas de software distribuído e sistemas distribuídos, onde vários componentes, serviços ou aplicativos precisam trabalhar juntos para cumprir uma determinada função ou oferecer um serviço.

Em muitos casos, arquiteturas/sistemas distribuídos são desenvolvidos de acordo com um estilo arquitetural específico. O estilo escolhido pode não ser o melhor em todos os casos ⇒ é necessário adaptar o comportamento da arquitetura usando um middleware (dinamicamente).

Dentro do middleware termos os Interceptors:

**Interceptors**
Interceptam o fluxo de controle normal quando um objeto remoto for invocado. 

App Cliente -Request method-> Object middleware -Request-> SO -Request-> Another machine
Ou seja nesse caso o middleware nao faz nada, mas poderia usar interceptors que vao modificar o comportamento adicionando por exemplo replicacao, seguranca. O interceptor _pode estar entre o cliente e o middleware, e tambem entre o middleware e o SO._

#### Sistemas Distribuídos autogerenciáveis

Sistemas e arquiteturas de software que consideram a **adaptacao automatica** do seu comportamento. Sistemas muito grandes, com muitos nodes. Impossivel intervencao humana de maneira eficiente, precisa de algo automatizado.
- Autoconfiguração
- Autogerenciamento
- Autocura
- Auto-otimização

*Modelo de regulacao por feedback*
Em muitos casos, sistemas auto-* são organizados como um sistema de regulação por feedback. Precisa de metricas para ajustar.

Por exemplo sistema com uma configuracao inicial, que passa para o sistema distribuido. Esse sistema tem uns ruidos incontrolaveis, e esse comportamento observado serve como metrica para estimar e analisar pelo proprio sistema.
Com isso tem-se estrategias dependendo do funcionamento.
*********Imagem de estrategias*****
[?]