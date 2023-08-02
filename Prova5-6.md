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
