# Sistemas Distribuidos

## Capitulo 7 - Consistencia e Replicaaco

### Conceitos
* [Introducao]()
* [Consistência centrada nos dados]()
  * linearizável, 
  * sequencial, 
  * causal, 
  * read your writes, 
  * eventual.
* [Protocolos de consistência]()
  * [primary-based: remote write, local write, chain]
  * [leader-less: quórum]
* [Cassandra]

### Introducao

Razoes para replicar
- Aumentar a _confiabilidade_ do sistema(caso alguma replica morra ou haja alguma corrupcao nos dados)
- Aumentar o desempenho e escalabilidade de tamanho e geografica do sistema, por exemplo nao sobrecarrega um unico node podendo usar a replica para distribuir a carga e estar mais proximo do usuario

> Se a replicacao ajuda tanto, qual o problema? *CONSISTENCIA*

_Esse problema de consistencia se refere a quando uma informacao precisa ser atualizada. Exemplo contador replicado, e se alguem atualizar em S1 tem um tempo para replicar, e quando alguem acessar S1 pode pegar a informacao nao atualizada = inconsistencia_

`Problema principal`: Para manter a consistencia entre as replicas, geralmente precisamos garantir que todas as `operacoes conflitantes` sejam *realizadas na mesma ordem em todas as replicas*

_Operacoes conflitantes_:
1. `read–write` conflito onde uma operação de leitura e uma de escrita ocorrem de forma concorrente
2. `write–write` conflito com duas operações concorrentes de escrita

> Garantir a ordem global de operações conflitantes pode ser _muito custoso, diminuindo a escalabilidade_

*Solucao*: diminuir os requisitos de consistência e, com sorte, conseguir evitar sincronizações globais custosas.

### Consistência centrada nos **dados**
Componentes(Modelo do sistem):
- Data store é uma colecao de dispositivos de armazenamento
- Processos que enviam operacoes de leitura e escrita
- Interacao entre um data store distribuido e os processos, no qual o data store define o resultado de operacoes concorrentes de leitura e escrita.


#### Linearizável [?]
- Informalmente: não é possível dizer que o dado está replicado.
- A operação deve ser realizada “instantaneamente” em  determinado ponto do tempo (propriedade real-time)
- Cada operação deve ser vista com um início e fim


#### Sequencial
- Faz sentido obter NILL (depois de um Write) na imagem abaixo?
  - Sim, pois pode haver um delay(`tempo de propagacao das operacoes`) de escrita e retornar nill enquanto nao escrever x=a no processo 2, inconsitencia, nao poderia ter R(x)a e depois R(x)NIL
- Note que não há início-fim nas operações como no linearizable.
- Há um tempo de propagação das operações.

> LINHA DE TEMPO do processo 1 e 2 que realiza operacoes. W =write do valor a, R=leitura de x recebendo Nil
- Processo 1 fez escrita da variavel x=a 
- Processo 2 leu x e devolve Nil
P1:    W(x)a
P2:             R(x)NIL     R(x)a

P1:       -->       W(x)a
P2:      R(x)NIL    -->     R(x)a

- Sequencial é facil de entender e dificil de implementar
- FoundationDB: ACID + NoSQL - criado pela Apple
- `Definicao`: _Qualquer intercalação de operações (read e write) é aceitável desde que todos os processos vejam a mesma intercalação e na ordem especificada pelo seu programa_
Exemplo: 
P1:    W(x)a
P2:          W(x)a
P3:                R(x)b     R(x)b
P4:                      R(x)b     R(x)b   
- Apresenta data store com consistência sequencial? Lembre que com a sequencial é possível mover o W(x)a
Porque P3 deu _read b antes de read a_? INCONSISTENCIA SEQUENCIAL


#### Causal
> Enfraquecimento da sequencial (usando o conceito de causalidade)
- `Definicao`: Operações _de write_ que potencialmente têm uma relação de _causalidade_ devem ser vistas por todos os processos na mesma ordem
- Diferentemente da sequencial, Writes concorrentes podem ser vistos em uma ordem diferente por processos diferentes (desde que não tenham uma relação de causalidade). - _todos os processos NAO precisam ver a mesma intercalacao de read/write, tanto faz a ordem_

Exemplo1: 
P1:   W(x)a
P2:        R(x)a   W(x)b
P3:                     R(x)b     R(x)a
P4:                     R(x)a     R(x)b   
Apresenta consistência causal?
- Observar writes, W(x)a e W(x)b tem relacao de causalidade? `Sim`, porque read le a informacao do a em P2, entao vem antes (happens before) de W(x)b. Entao o resto deveria enxergar a mesma ordem, `Nao apresenta` por que P3 e P4 deveriam obter a mesma ordem, entao a definicao fica meio verdadeira, portanto NAO APRESENTA CONSISTENCIA CAUSAL

Exemplo2: 
P1:   W(x)a
P2:             W(x)b
P3:                     R(x)b     R(x)a
P4:                     R(x)a     R(x)b   
_Writes concorrentes_, portanto APRESENTA CONSISTENCIA CAUSAL
Pode parecer estranho a ordem de P3 e P4, e se voce nao concordar pode usar a sequencial que mantem a ordem que enxerga a intercalacao de operacoes.


#### Eventual
Dependendo da aplicação é aceitável que *as atualizações não sejam propagadas imediatamente*
- Normalmente os clientes acessam a mesma réplica, então não há problemas de inconsistência (na visão do cliente)
- Permite uma implementação de modelo de consistência com menos restrições e portanto *mais eficiente*
- Exemplos: Facebook, DNS, Paginas Web em geral
- Em algum momento vai propagar a informacao e vai chegar no cliente, doesnt matter quando.
- Relativamente fácil de implementar
- Em ausência de conflitos write-write, todas as réplicas convergirão a cópias idênticas (eventualmente)
- Assume-se que somente uma pequena quantidade de processos realiza operações de write.
- Quando acontece um conflito write-write, geralmente declara-se vencedor a última escrita, i.e., last-write-wins, sobrescrevendo as anteriores

Exemplo: Cliente C2 obtém um valor antigo

*Protocolos de consistencia*