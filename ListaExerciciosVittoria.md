## Exercicios Capitulo 1

1. Hoje em dia sao muitos dos exemplos que podemos citar que vao de encontro a essa definicao. Um deles seria o Google docs, por exemplo um usuario poderia acessar essa aplicacao de maneira transparente, mas por tras sao varias maquinas distribuidas que atuam ccomo um  ssitema unico e coerente. Outros exemplos bem populares hoje com o uso da nuvem, sao cloudfront que tem varios servidores espalhados geograficamente, mas que funcionam como um unico servico de distribuicao de conteudo.
2. Em um SD o middleware pode atuar de diversas maneira, como por exemplo com a funcao de seguranca, replicacao, uma camada que te prove servicos para aplicacoes independente do que essas fazem. Podendo ser servicos de seguranca, replicacao, essenciais para confiabilidade do sistema distribuido. É um tipo de software que atua como um intermediário entre diferentes aplicativos ou sistemas. Sua principal função é `facilitar a comunicação e a interação entre esses sistemas`, permitindo que eles se conectem e troquem informações de maneira eficiente.
*Camada de middleware*
Acima da camada de transporte
- Prover servicoes e protocolos frequentemente usados que podem ser utilizados por varias aplicacoes diferentes
- Um conjunto rico de protocolos de comunicacao
- Desempacotamento de dados, necessarios para a integracao de sistemas
- protocolos de gerenciamento de nomes, para auxiliar o compartilhamento de recursos
- Protocolos de seguranca para comunicacoes seguras
- mecanismos de escalabilidade como replicacao e caching
  
3. Transparencia de distribuicao: capacidade de ocultar a complexidade da distribuicao dos recursos e servicos, permitindo que os usuarios e aplicativos interajam com o sistema como se fosse unico e centralizado. Exemplos de diferentes tipos de transparencia de distribuicao(7 tipos): 
   1. acesso: cliente nao deve saber tambem COMO e ONDE esta sendo chamado os outros nodes, pois precisa ser um sistema unico
   2. localizacao: Esconder onde o objeto esta localizado(exemplo URL, vc nao sabe onde esta a pagina web); 
   3. relocalizacao: de forma transparente o acesso ao cliente muda para o servidor novo com o mesmo processo trazendo mais velocidade de desempenho.(Esconde que um objeto pode ser movido para outra localidade enquanto esta sendo utilizado). Quem define é o SD /= de migracao;
   4. migracao: A diferenca entre Relocalizacao, é que aqui o cliente que define que o objeto deve ser migrado para outro local; 
   5. replicacao: a replicacao ocorre mas o cliente nem sabe que ocorre; 
   6. concorrencia: Esconder que um objeto pode ser `compartilhado` entre diferentes usuarios independentes.; 
   7. falhas: Esconder falhas e a possivel recuperacao de um objeto;

4. _Por que as vezes é tao dificil ocultar a ocorrencia e a recuperacao de falhas em um sistema distribuido?_

5. _Por que nem sempre é uma boa visar a implementacao do mais alto grau de transparencia possivel?_
Nem sempre é uma boa visar a implementacao do mais alto grau de transparencia possivel, pois tras `complexidade` a arquitetura do sistema, que dificulta a manutencao dos sistema. Alem disso pode ter um custo no `desempenho` por ter muita complexidade na compunicacao, pode tornar o sistema mais lento. Com muitos processos rodando e complexidade pode acontecer de ter `sobrecarga na rede` pelo aumento na quatidade de trafego, aumentando a latencia nas comunicacoes. E tambem pode afetar a escalabilidade negativamente, por limita-la conforme cresce a demanda do sistema.

6. _O que é um sistema distribuido aberto e quais sao os beneficios que a abertura proporciona?_
Um sistema distribuido aberto é um sistema que independe de linguagem de programacao, plataforma, e hardware, pois por meio de interfaces(cabecalho, nao implementacao) bem definidas sao capazes de interagir com outros sistemas abertos. Beneficios é essa interoperabilidade por conta das interfaces, independencia. alem disso ha uma maior flexibilidade de tecnologia. 

7. _Descreva com Exatidao o que quer dizer um sistema escalavel._
Um sistema escalavel é um sistema que mantem o desempenho mesmo com um aumento grande de requisicoes no sistema, independente do numero de usuarios/processos, distancia entre nodes, e numeros de dominios administrativos. 3 tipos de escalabilidade: `tamanho`, `geografica`, `administrativa`. Hoje em dia o maior desafio é conseguir a escalabilidade geografica e administrativa

8. _Pode-se conseguir escalabilidade pela aplicacao de diferentes tecnicas. Quais sao essas tecnicas?_
As tecnicas de escalabilidade servem para esconder latencia de comunicacao. Nao fique esperando por resposta, faca outra coisa: o uso de comunicacao assincrona, diferentes handlers(multithread). Outra tecnica é o particionamento de dados e computacao em muitas maquinas: se algum processo puder ser realizado no cliente, faca, para nao sobrecarregar a CPU do servidor, microsservicos, particionamento de tabelas de bd(Escalabilidade Vertical/horizontal.). A terceira tecnica é Replicacao/caching: fazer copias de dados e disponibilizar em diferentes maquinas.

9.  Dissemos que quando uma transacao é abortada, o mundo é restaurado ao estado anterior, como se a transacao nunca tivesse acontecido. Mentimos. De um exemplo no qual restaurar o mundo é impossivel. [?]

10. _Executar transacoes aninhadas requer certo nivel de coordenacao. Explique o que um coordenador realmente deveria fazer._
[?]

11. _Argumentamos que a transparencia de distribuicao pode nao estar presente em sistemas pervasivos. Essa declaracao nao vale para todos os tipos de transparencias. De um Exemplo._
Um exemplo disso é a transparência de acesso em sistemas pervasivos. Nesses sistemas, os usuários podem interagir com aplicativos e serviços sem precisar se preocupar com a localização física dos recursos ou dispositivos subjacentes.(Casa inteligente). As demais transparencias podem ser mais desafiadoras de alcançar em sistemas pervasivos devido à natureza distribuída e heterogênea desses ambientes.

12. _Ja demos alguns exemplos de SDs pervasivos: sistemas domesticos, sistemas eletronicos para tratamento de saude e redes de sensores. Amplie essa lista com mais exemplos._
Como ja estudado os sistemas pervasivos podem ser associados com IoT. Outros exemplos poderiam ser sistemas de automacao industrial, cidades inteligentes, sistemas de entretinimento domesticos, etc.

## Exercicios Capitulo 2

1. _Se um cliente e um servidor forem colocados longe um do outro, podemos ver a latencia de rede dominar o desempenho global. Como podemos atacar esse problema?_
Atraves do uso de caching e replicacao em diferentes locais, 

2. _O que é um arquitetura cliente-servidor de tres divisoes?_
Uma arquitetura cliente-servidor de tres divisoes, significa que sao 3 camadas logicas: camada de apresentacao(UI) que contem o necessario para a aplicacao interagir com o usuario. Camada de negocio(application) que contem as funcionalidades da aplicacao, e acamada de dados(database) que contem os dados que o cliente quer manipular atraves dos componentes da aplicacao.

3. _Qual a diferencao entre uma distribuicao vertical e uma distribuicao horizontal?_
Essas distribuicoes estao relacionadas a forma como os dados e as funcionalidades sao divididos e organizados para suportar a escalabilidade e o desempenho do sistema.
- distribuicao vertical: diferentes componentes ou modulos sao colocados em servidores distintos. Cada servidor pode ser dedicado ao banco de dados, outro aos servicos web e ate mesmo outro para logica de negocios. Util quando ha a necessidade de especializacao e otimizacao de recursos para funcoes especificas. Porem pode sobrecarregar a comunicacao entre os servidores especialmente se as interacoes entre os modulos forem frequentes.
- distribuicao horizontal: os dados e funcionalidades sao particionados e distribuidos em varios servidores, de forma que cada servidor contenha uma copia identica. É eficiente para escalabilidade, pois permite adicionar mais servidores conforme a demanda aumetnsa, distribuindo a carga de trabalho de maneira uniforma, ajjuda a melhorar o desempenho geral do ssitema

4. [?]

5. _Em uma rede de sobreposicao estruturada, mensagens sao roteadas de acordo com a topologia da sobreposicao. Cite uma importante desvantagem dessa abordagem._
Uma importante desvantagem é a falta de flexibilidade para acomodar alteracoes na topologia ou na distribuicao de dados. Como as rotas de comunicacoes é determinada pela estrutura da sobreposicao, qualquer alteracao na rede ou adicao/remocaoo de nodes pode exigir uma reorganizacao completa da estrutura, resultando em um custo computacional e comuicacao. Alem disso podemos ter pontos de gargalo se algum node falhar ou ficar congestionado.


## Exercicios Capitulo 3

1. _Nesse problema voce devera fazer uma comparacao entre ler um arquivo usando um servidor de arquivos monothread ou um servidor multithread. Obter uma requisicao para trabalho, despacha-la e fazer o resto do processamento necessario demora 15ms. Considerando que os dados necessarios estrejam em um cache na memoria principal. Se for preciso uma operacao de disco, como acontece em um terco das vezes, serao necessario mais 75 ms, durante os quais o thread dorme. Quantas requisicoes por seguindo o servidor pode manipular se for monothread? E se for multithread?_
   
2. Teria sentido limitar a quantidade de threads em um processo servidor?


3. Descrevemos no texto um servidor de arquivo multithread mostrando por que ele é melhor do que um servidor  monothread e um servidor com maquina de estado finito. Ha alguma circunstancia na qual um servidor monothread poderia ser melhor? De um exemplo.
4. Proxies podem suportar transparencia de replicacao invocando cada replica, copmo explicado no texto. O lado servidor de uma aplciacao pode estar sujeito a uma chamada replicada?
5. Construir um servidor concorrente por meio da multiplicacao de um processo tem vantagens e desvantagens em comparacao com servidores multithread. Cite algumas.
6. Como podemos impedir que uma aplicacao evite um gerenciador de janela, e, assim consiga baguncar completamente uma tela?
7. Um servidor que mantem uma conexao TCP/IP com um cliente é com estado ou sem estado?
8. Imagine um servidor Web que mantenha uma tabela na qual enderecos IP de clientes sejam mapeaedos para as paginas Web acessadas mais recentemente. Quando um cliente se conecta ao servidor, este consulta o cliente em sua tabela e caso o encontre, retorna a pagina registrada. Esse servidor é com estado ou sem estado?
9.  Considere um processo P que requer acesso ao arquivo F, disponivel na maquina em que P esta executando no momento em questao. Quando P passa para outra maquina, ele ainda requer acesso a F. Se a vinculacao arquivo-maquina for fixam como poderia ser implementada a referencia a F no ambito do sistema?
10. Descreva com detalher como pacotes TCP fluem no caso de transferencia TCP junto com informacoes sobre enderecos de fonte e destino nos varios cabecalhos.

## Exercicios Capitulo 4

1. 

## Exercicios Capitulo 5