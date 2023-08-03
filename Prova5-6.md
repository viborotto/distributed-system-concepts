# Sistemas Distribuidos

## Capitulo 5 - Naming

### Conceitos
* [Definicoes: nomes, identificadores e endereços]()
* [Resolucoes de nomes]()
  * [Plano]()
    * [Flooding]
    * [DHT e fingertables]
  * [Estruturado]()
    * [DNS]
  * [Atributo]()





### Definicoes: nomes, identificadores e endereços


*Nomes*: sao usados para denotar entidades(endereco de rede, servico web,..) em um sistema distribuido. Para realizar operacoes em uma entidade é preciso ter acesso a ela por meio de um `ponto de acesso`(_endereco_, por exemplo IP e porta). Nomes sao independentes de onde a entidade esta localizada, p.e URL


*Identificadores*
_Nomes puros ou planos_: sao nomes que nao tem significado proprio, sao strings aleatorias. Nomes puros podem ser usados apenas para comparacao.

_Propriedades do Identificador_: 
- Cada identificador se refere a no maximo uma entidade. ID literalmente
- Cada entidade é referenciada por no maximo um identificador
- Um identificador sempre se refere a mesma entidade(nao ha reutilizacao de identificadores)
- Obs: Um identificador nao necessariamente precisa ser um nome puro(pode ter conteudo) - nao precisaria ser aleatorio

### Resolucoes de nomes

Como procurar o acesso ao recurso, dado um nome plano?
#### Plano
Problema: Dado um nome nao estruturado(ex: um identificador), como localizar seu ponto de acesso?
1. Solucao Simples(broadcasting/flooding/ponteiros)
   - *Broadcasting*: fazer o broadcast(todos os nos da rede) do ID, requisitando que a entidade devolva seu endereco
     - Nao escala para alem de _redes locais_
     - Requer que todos os processos escutem e processem os pedido de localizacao
     - ARP(Address Resolution Protocol): para encontrar o endereco MAC associado a um IP, faz o broadcast da consulta "quem tem esse endereco IP?"

   -  *Flooding*: um node X envia requisicao R por um arquivo A a alguns vizinhos V. O node V recebe e realiza o seguinte processo
   -  Problema de Escalabilidade pode chegar a mesma informacao de nodes diferentes(de tamanho e geográfica), pois pode demorar se estiver em outro pais TTL. Para tentar conter isso o ideal seria usar o flooding com um limite usando o TTL para a mensagem, baseado em saltos, ou seja "so pode saltar 2", 2 nodes
```python
      -  Se V ja viu a req antes, a ignora
      -  V procura localmente por A
      -  Se V nao tem o arquiv:
         -  Se o TTL da requisicao > 0
            -  Encaminha R para um dos seus vizinhos com TTL-1
      - Se V tem o arquivo:
        - Devolve diretamente para X ou devolve para quem faz(encaminhou) a rquisicao
```
GNUTELLA - FLOODING
```python
    Se V nao tem o arquivo:
        Encaminha para 7 vizinhos com TTL = 10
    Se V tem o arquivo:
        responde de forma inversa pelo mesmo caminho
    # nao ha loop nas requisicoes, pois sabe por quais nodes passou
    # transferencias sao realizadas diretamente entre nodes via HTTP
    # MESMO TENDO UMA AREA LIMITADA, FUNCIONAVA, baseando na popularidade dos arquivos, replicando dentro da area
         
```

   -  *Ponteiros*: quando uma entidade se mover ela deixa pra tras um ponteiro para sua nova localizacao
      -  O dereferenciamento pode ser automatico e invisivel para o cliente, basta seguir a sequencia de ponteiros
      -  Atualize a referencia do ponteiro quando o local atual for encontrado
      -  Problema de escalabilidade geografica(que podem requerer um mecanismo separada para reducao da sequencia)
         -  Sequencias longas nao sao tolerantes a falha, ou seja se um node morrer, ja era o caminho de referencia
         -  Maior latencia de rede devido ao processo de dereferenciamento


2. Abordagens baseadas em um local pre-determinado(home-based) - procurar em local pre-determinado
- Faca com que um local fixo sempre saiba onde a entidade esta
- Endereco pre-determinado é registrado em um servico de nomes
- Endereco mantem um registro do endereco externo da entidade
- O cliente primeiro contata o endereco pre-determinado e depois continua para seu endereco externo(atual)
- Abordagem em dois niveis:
  - Mantenha um registro das entidade que foram visitadas: verifique o endereco local primeiro, se falhar so entao va para o endereco pre-determinado
- Problemas:
  - O endereco pre-determinado deve existir enquanto a entidade existir 
  - O endereco fixo pode se tornar desnecessario se a entidade se mover permanentemente -> SOLUCAO DNS
  - Escalabilidade geografica ruim(entidade pode estar do lado do cliente ou nao, e a home pode ser longe, e sempre deve passar pela home,delay)


3. Tabelas de hash distribuidas(P2P Estruturado) - DHT - chave/valor
- `Chord`: considere que os nodes estejam em forma de anel logico, sucessor e predecessor
  - A cada nó é atribuído um identificador aleatório de m-bits. Cada node um numero
  - A cada entidade é atribuída uma única chave de m-bits
  - Entidades com chave k estão sob a jurisdição do nó com o menor
id ≥ k (seu sucessor)
Exemplo: arquivo a.mp4
hash(a.mp4) = 7 = k
No anel vai armazenar  com vase na regra id >= k

- _Busca por uma informacao: solucao ruim_
Faca com que cada node mantenha o registro de seus vizinhos e faca uma busca linear ao longo do anel (SUCESSO PARA SUCESSOR)
- _NOTACAO_: chamamos de node P o node cujo identificador é P
- Para melhorar o desempenho da busca: `SHORTCUTS` = `FINGERTABLES` diferente em cada node
  - Esses shortcuts sao ligacoes com outros nodes nao necessariamente sequenciais no anel
   - Cada node P mantem um array shortcut FTp [] com no maximo m entradas
   - [Formula FTp[i]] => FTp [i] = succ(p + 2^i−1)
   - FTp[i] aponta para o primeiro node que sucede P com distancia de pelo menos 2^i-1 posicoes (distancia 2^1, 2^2 ....)
   - Para procurar por uma chave k, o nó P encaminha o pedido para o
nó com índice j tal que: q = FTp [j] ≤ k < FTp [j + 1]
   - Se p < k < FTp [1], a requisição é encaminhada para FTp [1]
     - Entre os nodes
- DHTs: Exploracao de proximidade da rede:
  - Problema: a organizacao logica dos nodes em uma rede overlay pode levar a transferencia de mensagens de forma erratica na internet: os nodes K e succ(k+1) podem estar muito longes um do outro
  - Solucoes:
    - Atribuicao ciente da rede: ao atribuir um ID ao node, assegure-se de que os nodes proximos no espaco de enderecamento estao proximos na rede real. Pode ser muito complicado, por muitas vezes o SD nao ter acesso a camada fisica
    - Roteamente de proximidade: mantenha mais de um sucessor possivel e encaminhe a mensagem para o mais proximo. Exemplo no Chord , FTp[i] aponta para o primeiro node em INT = [p + 2^i-1, p+2^i - 1]. O node p pode guardar tambem ponteiros para outros nodes em INT
    - Selecao de vizinho por proximidade: quando houver uma escolha para determinar quem sera seus vizinho, escolha o mais proximo

4. Servicos hierarquico de nomes(HLS)
Arvores distribuidas
Ideia: Construir uma arvore de busca em larga escala
onde a rede é dividida em domínios hierárquicos.
Cada domínio é representado por um diretório de nós separado.

So tem um caminho pra chegar, mas se um node morre morre toda uma estrutura. Podemos criar subdominios dentro do dominio, identificadores aleatorios. Subdominios podem ser criados usando areas geograficas, para ter conexoes rapidas.

*HLS: organização em árvores*
- _Invariantes_: endereco da entidade E é armazenado em uma folha ou node intermediario
- odes intermediarios contem um ponteiro para um filho se a subarvore cuja raiz e o filho contenha o endereco da entidade 
- A raiz conhece todas as entidades

*HLS: consulta*
- Comece a busca pelo node folha local
- Se esse node souber sobre E(recurso que deseja achar), seguir o ponteiro para baixo, se nao, continue a subir
- Continue subindo ate encontrar a raiz

*HLS: insercao*
- Um pedido de insercao é encaminhado ao node folha que conhece a entidade E
- Primeiro verifica se existe E em alguma parte da estrutura, nao pode simplesmente inserir


#### Estruturado

Nome estruturado, nao é uma string aleatoria, ou seja possivel de leitura por pessoas. www.google.com.br

- Geralmente representado como um grafo no qual um node folha representa uma entidade que tem um nome.
- Diretorio de nodes é uma entidade que se refere a outros nodes
- hierarquia de nomes na arvore(diferenca do anterior)
- A resolucao de um nome é um mecanismo para recuperar a entidade que possui o nome
- `Problema`: Para resolver um nome precisamos de um diretorio. Como encontrar esse node raiz inicialmente? 
  - Mecanismo de closure: inicia em um servidor DNS, cmcc.ufabc.edu.br -> raiz

*DNS: Implementacao de espacos de nomes*
Problema: Distribuir o processo de resolução de nomes, bem como o gerenciamento do espaço de nomes, em várias máquinas, de forma a distribuir o grafo de nomes para garantir escalabilidade e disponibilidade.
- Um dos primeiros SDs implementados - Nome a um IP

*Distribuicao*
_Tres niveis distintos_
1. `Nível global`: consiste de um diretório de nós de alto nível. Suas principais características são que os nós do diretório devem ser gerenciados em conjunto por diferentes administradores e que há uma relativa estabilidade nos nomes, ou seja, as entradas do diretório são modificadas muito raramente
2.  `Nível de administração`: nós de diretório de nível intermediário. Podem ser agrupados e cada grupo pode ser responsabilidade de um administrador diferente. Apesar de estáveis, mudam com mais frequência do que entradas no nível global(normalmente por cada pais)
3. `Nível gerencial`: nós de nível inferior que pertencem a um único
administrador. O problema principal é mapear os nós de diretório aos
servidores de nomes local. Mudanças regulares ocorrem neste nível.(por exemplo .ufabc)

*Comparacao*

|Item|Global|Administracao|Gerencial |
| ------- | ------- | ------- |------- |
| Escala geografica| Mundial | Organizacao | Departamento|
| Nos | Poucos | Muitos | Qunatidades enormes|
| Responsividade | Segundos | Milissegundos | Imediato | 
| Propagacao de atualizacoes | Tardio | Imediato | Imediato |
| Replicas | Muitos | Nenhum ou poucos | nenhum | 
| Cache no lado do cliente | Sim | Sim | As vezes |


*DNS: Resolucao de nomes ITERATIVA*
Camada por camada ate encontrar o nome.
- Cliente envia resolve(dir,[name1,.., namek=K]) para ServerRaiz responsavel por dir. = Procure no diretorio
- Server0 resolve(dir, name1) -> dir1, ddevolve a identificacao(IP) de Server1 que contem dir1.
- Cliente envia resolve(dir1,[name2,.., namek=K]) para Server1, etc

*DNS: Resolucao de nomes RECURSIVA*
Ao inves de retornar e acumular as referencias ate achar totalmente as informacoes, faz uma busca recursiva. Qualquer duvida olhar desenho da aula em video. Manda a informacao para o node raiz e ai o node raiz se vira ate obter todas as referencias do endereco completo e retornar uma unica vez ao cliente.
- Cliente envia resolve(dir,[name1,.., namek=K]) para ServerRaiz responsavel por dir. = Procure no diretorio
- Server0 resolve(dir, name1) -> dir1, e envia resolve(dir1,[name2,.., namek=K]) para Server1, que contem dir1
- Server0 espera pelo resultado de Server1 e devolve para o cliente

*DNS: Problema de Escalabilidade*

_Escalabilidade de tamanho_: Devemos garantir que os servidores possam lidar com um grande numero de requisicoes por unidade de tempo. Servidores alto nivel podem estar com problemas serios.
`Solucao`: Assuma (pelo menos nos níveis global e de administração) que os conteúdos dos nós mudam muito pouco. Assim podemos aplicar replicação extensivamente, mapeando nós a múltiplos servidores e começar a resolução de nomes no servidor mais próximo.
_OBS_: Um atributo importante em muitos nodes é o endereco(onde a entidade pode ser localizada). Replicacao faz com que servidores de nomes em larga escala sejam inadequados para localizar entidades moveis.

_Escalabilidade Geografica_: Precisamos garantir que a resolucao de nomes escale mesmo em grandes distancias geograficas. Provavelmente seria bom utilizar a resolucao recursiva nesse caso por estar no mesmo dominio, proximos geograficamente.
- Porém:
Ao mapear nós em servidores que podem ser localizados em qualquer lugar, nós acabamos introduzindo uma dependência de localização implícita. Pode nao ser tao trivial saber a localizacao para tratar isso.


#### Nomeacao baseada em atributos

Em muitos casos, é mais conveniente nomear e procurar entidades pelos atributos => servicos tradicionais de diretorios(ex: paginas amarelas)
- Problema: Operações de consulta globais podem ser muito caras(nao poderia ser feito, muito custoso), já que necessitam que os valores dos atributos procurados correspondam aos valores reais das entidades. Em princípio, teríamos que inspecionar todas as entidades.
`Solucao`: Implementar serviços de diretórios básicos locais (tais como bancos de
dados) e combiná-los com os sistemas de nomes estruturados tradicionais.


*Implementando um servico de diretorio*
Solucao para uma busca escalavel: Implementar um servico com diversos bancos de dados locais, combinando-o com o DNS

*LDAP -  Uma das implementacoes*
Cada entrada do diretório consiste em um par (atributo, valor), unicamente nomeado para facilitar as buscas. Organizados de forma hierarquica

## Capitulo 6

### Conceitos - Coordenacao
* [Exclusao mutua]()
  * [Permissao]
    * [Centralizado]
    * [distribuido] 
  * [token]
  * [decentralizado]
* [Eleicao]()
  * Anel
  * Bully

Problemas que devem ser tratados em sistemas distribuidos
### Exclusao mutua
Varios dispositivos que precisam acessar um recurso, mas somente um pode acessar.
`Problema`: Alguns processos em um sistema distribuido querem acesso exclusivo a algum recurso

_Solucoes_
- *Baseado em permissao*: um processo que quiser entrar na secao critica(ou acessar um recurso) precisa da permissao de outros processos

1. *Permissao: Centralizado*
Node responsavel por dar a permissao: _coordenador_
  - Processo P1 pede permissao ao coordenador para acessar o recurso compartilhado. Verificara na fila em sua memoria para verificar se alguem ja pediu, fila vazia -> permissao concedida
  - Processo P2 tambem quer acessar o recurso, coordenador sabe que P1 ja pediu -> recurso ja emprestado -> o coordenador nao responde, e coloca em sua fila quem pediu e nao deu(P2)
  - Quando P1 libera o recurso, avisa o coordenador, e o coordenador finalmente responde para P2 -> permissao concedida
  - _Possiveis problemas:_ Poderia acontecer de P1 nao entregar a mensagem liberando o recurso, poderia acontecer de o coordenador morrer. Poderia ter problema de escalabilidade de tamanho.

2. *Permissao: Distribuido*
O processo que precisa do recurso envia uma requisicao de permissao a todos os outros processos(inclusive para ele mesmo)
   - A resposta a permissao(denominada de ACK) é enviada quando:
     - O processo receptor nao tem interesse no recurso compartilhado ou
     - o processo receptor está esperando por um recurso, mas tem menos prioridade (a prioridade é determinada via comparaçao de timestamps*)
   - Em todos os outros casos, o envio da resposta é adiado.
   - * timestamp é o horário do relógio ou um valor (e.g., contador).
   - Exemplo: 3 processos com identificadores 0,1,2 e proprio ts. 
     - Dois processos P0 e P2 querem acessar um recurso compartilhado ao mesmo tempo
     - P0 e P2: manda o seu ts para todos e para ele mesmo
     - P1 nao tem interesse no recurso entao manda ACK(permissao para os outros nodes)
     - P0 tem o menor timestamp; logo tem maior prioridade, entao o P2 que manda ACK(OK) para P0.
     - P0: recebeu OK dele mesmo, do P2 e do P1: 3 OK pode acessar o recurso
     - P2: recebeu 2 OK, nao pode ainda acessar
     - P0: acessou e liberou o recurso, respondendo OK para P2
     - P2: 3 permissoes, pode acessar o recurso

- *Baseado em tokens*: um token é passado entrer processos. Aquele processo que tiver o token pode entrar na secao critica ou passa-lo para frente quando nao tiver interessado.
  - Organizar os processos em anel logico e passar um token entre eles. Aquele que estiver token pode entrar na secao critica(se ele quiser) = overlay logica de conexoes
  - Anel:
    - Algum deles tem um token node 0
    - Node 0 usa o token e o recurso e passa o token para n1
    - n0 nao poderia mais usar, somente n1
    - n1 nao quer acessar e passa o token para n2
  - Possiveis problemas: Se morre um node perde o token
  - Cenario de que nenhum node quer somente o 7, porque nao passar direto ao inves de ir de sucessor em sucessor?

- *Descentralizado*:
Assuma que todo recurso é replicado N vezes (para não ter SPoF, i.e., maior disponibilidade).
  - Cada replica está associada a seu próprio coordenador.
  - acesso requer a maioria dos votos de m > N/2 coordenadores. E nao esperar OK de todos

_Hipotese_
Quando um coordenador morrer, ele se recuperará rapidamente, mas terá esquecido tudo sobre as permissões que ele deu.
  - Risco? Após a recuperação, dar incorretamente a permissão de aceso para um processo

> Quão robusto é esse sistema?
Usando probabilidade
- Seja p = ∆t/T a probabilidade do reset do coordenador.
- [*************FORMULAS SEND HELP*************]
Corretude é violada quando m − f coordenadores (corretos, sem
reset) são minoria
• I.e., existe uma maioria dando a exclusão mútua para outro. 
- [ESTUDAR TABELA DE COMPARACAO - EXCLUSAO MUTUA]


### Eleicao 
Eleicao de um node que vai ser responsavel por um grupo de node ou servico. Selecionar um node.

- Um algoritmo precisa que algum dos processos assuma o papel de coordenador. Como selecionar esse processo especial _dinamicamente_?

- `OBS`: Em muitos sistemas o coordenador é escolhido manualmente(exemplo: servidores de dados). Isso leva a solucoes centralizadas *com um ponto unico de falha SPoF*
- Perguntas:
  - Se um coordenador é escolhido dinamicamente, até que ponto
podemos dizer que o sistema será centralizado e não distribuído? O lider que for responsavel por dar permissao na exclusao mutua, nesse momento sim é contralizado no lider. Se o lider morre nao pode parar o sistema, necessario que outro node se torne lider, esse processo é distribuido.
  - Um sistema inteiramente distribuído (ou seja, um sem um
coordenador) é sempre mais robusto que uma solução centralizada/coordenada? Melhor mas talvez ineficiente [rever resposta do professor]. Precisaria estar muito bem parametrizado para aumentar a eficiencia

- Todos os processos possuem um id unico, p.e ip, porta 
- Todos os processos conhecem os ids de todos os outros processos no sistema (mas eles não têm como saber se os nós estão funcionando ou não)
- A *eleição significa identificar o processo de maior id* que está funcionando em um dado momento

#### Anel
As prioridades –identificadores– dos processos são obtidas organizando-os em um anel (lógico). O processo com prioridade mais alta deve ser eleito como coordenador. 
- Qualquer processo pode iniciar a eleição ao enviar uma mensagem
de eleição ao seu sucessor. Se um sucessor estiver indisponível, a mensagem é enviada ao próximo successor. Um node nao pode ver somente o sucesso, mas o sucessor do sucessor, isso depende muito do sistema e implementacao.
- Se uma mensagem for repassada, o remetente se adiciona na lista.
Quando a mensagem voltar ao nó que iniciou, todos tiveram a chance de anunciar a sua presença. 
- O nó que iniciou circula uma mensagem pelo anel com a lista de
nós “vivos”. O processo com maior prioridade é eleito coordenador.
- *Exemplo funcionamento*: anel de 0 a 7(id)
    (1) - (2) - (3) - (4)
     |                  |
    (0) - (7) - (6) - (5)
  - Suponha que um node6 vai iniciar o processo de eleicao porque ele descobriu que o sucessor morreu node7. O node7 era o lider(maior id).
  - node6 nao sabe nda ainda, pq poderia ter um node8. 
  - node6 vai mandar uma mensagem para o seu sucessor, nesse caso node0, enviando o identificador 6.
  - Node0 passa a mensagem para o node1 com [6,0] o id dele proprio
  - vai passando ate o node4 [6,0,1,2,3], porem nesse momento o node3 quer solicitar a eleicao(pode ter acontecido dele identificar que algum node morreu). Mas mesmo assim envia para o node4.
  - Node4 agora tem 2 processos que passa para o node5 um com a lista [6,0,1,2,3,4] e outro com [3,4].
  - Node6: recebe 2 processos um com a lista [6,0,1,2,3,4,5] e outro com [3,4,5] - CHEGOU DE VOLTA NO QUE INICIOU A ELEICAO
  - Quando o node6 tem todos os processos, pode tomar a decisao e ser o lider. Porem temos um outro processo de eleicao [3,4,5], que vai repassando ate o node3 que abriu o segundo processo de eleicao porem nao acontece a mudanca de lider pq o maior é o 6.
  - Consideramos apenas perda de node, mas pode ter perda de mensagem usando TCP, que trata isso.


#### Bully - valentao
Nao é usado, mas importante saber os principios que sao base para outros algoritmos recentes.
- os N processos se conhecem
- ganha o maior id
Considere N processos {P0,..., PN−1} e seja id(Pk ) = k. Quando
qualquer processo Pk perceber que o coordenador não está mais
respondendo às requisições, ele começa uma nova eleição:
1. Pk envia uma mensagem ELECTION para todos os processos com
identificadores maiores que o seu: Pk+1, Pk+2,..., PN−1.
2. Se ninguém responder, Pk ganha a eleição e se torna o coordenador
3. Se um dos nós com maior id responder, esse assume a eleição e o
trabalho de Pk termina.  O maior sempre ganha, por isso o nome de Bully “valentão”.
4. O coordenador escolhido envia COORDINATOR a todos

- *Exemplo funcionamento*: 8 nodes nao estao em anel, sem ordem
  - Node 4 conhece todos os nodes maior que ele 5,6,7. Node 7 que morreu.
  - Se os nodes maiores existirem envia Election para 5,6,7, porem somente responde OK os 5,6 pois sao maiores que 4.
  - O node5 nao sabe se ele é lider, so sabe que o lider morreu, nao sabe quem
  - node5 envia Election para o 6 e o 7. node 6 envia Election para o 7
  - o node6 manda o OK para o 5. Mas o 6 precisa esperar o timeout, Passou um timeout da OK para o node 5 
  - Portanto o node5 sabe que tem alguem maior que ele.
  - Node6 se torna o lider, e avisa todos os nodes com mensagem Coordinator. E agora serve para dar exclusao mutua de algum recurso
- Note que aqui assumimos o caminho feliz, que a comunicacao é confiavel, TCP, mensagem sem demorar muito


### Conceitos - Relogios
