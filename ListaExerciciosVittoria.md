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

9.  _Dissemos que quando uma transacao é abortada, o mundo é restaurado ao estado anterior, como se a transacao nunca tivesse acontecido. Mentimos. De um exemplo no qual restaurar o mundo é impossivel._ [?]

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
- `Monothread`: 
  - Tempo necessário para processar uma requisição: 15 ms (operação em cache)
  - Tempo necessário para processar uma requisição com operação de disco: 15 ms + 75 ms = 90 ms
  - 1/90 = 11,11 Requisicao por segundo
- `Multithread`:
  - Tempo necessário para processar uma requisição: 15 ms(operação em cache)
  - Tempo necessário para processar uma requisição com operação de disco: 15 ms + 75 ms = 90 ms
  - Como nesse caso nao precisa esperar para processar novas requests, o tempo de 75ms pode ser utilizado para processar novas requisicoes em outras threads
  - 1 / (Tempo Total por Requisição - Tempo de Espera para Novas Threads) = 1/90-75 = 1/15 = 66,66 requisicoes por segundo
  - CAPACIDADE MULTITHREAD > CAPACIDADE MONOTHREAD

2. _Teria sentido limitar a quantidade de threads em um processo servidor?_          
Depende, limitar a quantidade de threads em um processo servidor pode ser uma abordagem sensata para garantir que o servidor utilize os recursos de forma eficiente e evite problemas de desempenho e escalabilidade. A escolha do número ideal de threads dependerá da carga de trabalho do servidor, das características do sistema e das necessidades específicas do aplicativo.(Evitando sobrecarga de uso de recursos do sistema, overhead de troca de contexto, concorrencia excessiva, problemas de escalabilidade)

3. _Descrevemos no texto um servidor de arquivo multithread mostrando por que ele é melhor do que um servidor  monothread e um servidor com maquina de estado finito. Ha alguma circunstancia na qual um servidor monothread poderia ser melhor? De um exemplo._        
Um servidor monothread poderia ser melhor no cenario em que o servidor precisa atender a uma carga de trabalho muito leve ou de baixa complexidade. Ou seja se a demanda por op de leitura e escrita for muito baixa e nao houver muitos clientes simultaneos, o monothread seria suficiente. Outro exemplo poderia ser alguns sistemas embarcados com recursos limitados para economizar memoria e CPU.

4. _Proxies podem suportar transparencia de replicacao invocando cada replica, como explicado no texto. O lado servidor de uma aplciacao pode estar sujeito a uma chamada replicada?_       
O lado servidor de uma aplicação pode estar sujeito a chamadas replicadas somente se o servidor NAO implementar mecanismos para lidar com essas chamadas de forma apropriada.  Isso pode envolver o uso de técnicas de exclusão mútua ou semântica específica para garantir que as chamadas replicadas sejam tratadas corretamente.

5. _Construir um servidor concorrente por meio da multiplicacao de um processo tem vantagens e desvantagens em comparacao com servidores multithread. Cite algumas._            
- Vantagens: `isolamento` cada processo tem seu enderecamento, alto grau de isolamento, e ate mesmo seguranca. `confiabilidade`: Se um processo falhar nao afetara os outros processos. `escalabilidade`: multiplicar processos pode ser mais escalável em sistemas com vários núcleos de CPU, pois cada processo pode ser executado em um núcleo separado, permitindo melhor aproveitamento do hardware.
- Desvantagens: `overhead de comunicacao` como os processos sao isolados a comunicacao entre eles deve ser feita por meio de comunismos de IPC(overhead adicional). `uso de recurso`: multiplicar processos pode consumir mais recursos do sistema. `complexidade` a gestão e coordenação de múltiplos processos podem ser mais complexas do que lidar com várias threads dentro de um único processo.

6. _Como podemos impedir que uma aplicacao evite um gerenciador de janela, e, assim consiga baguncar completamente uma tela?_       
[?]

7. _Um servidor que mantem uma conexao TCP/IP com um cliente é com estado ou sem estado?_       
Sem estado, TCP mantem estado na camada de transporte e nao na camada de aplicacao(stateless)

8. _Imagine um servidor Web que mantenha uma tabela na qual enderecos IP de clientes sejam mapeaedos para as paginas Web acessadas mais recentemente. Quando um cliente se conecta ao servidor, este consulta o cliente em sua tabela e caso o encontre, retorna a pagina registrada. Esse servidor é com estado ou sem estado?_        
Esse é um servidor com estado, pois a informacao armazenada(estado) é utilizada para atender as requisicoes subsequentes como se fosse um historico.

9.  _Considere um processo P que requer acesso ao arquivo F, disponivel na maquina em que P esta executando no momento em questao. Quando P passa para outra maquina, ele ainda requer acesso a F. Se a vinculacao arquivo-maquina for fixam como poderia ser implementada a referencia a F no ambito do sistema?_           
- Se a vinculação arquivo-máquina for fixa, isso significa que o arquivo F está associado a uma máquina específica e não pode ser acessado diretamente de outras máquinas. Nesse caso, para permitir que o processo P acesse o arquivo F mesmo quando estiver em outra máquina, uma abordagem possível é utilizar um serviço de servidor de arquivos centralizado. 
- O servidor de arquivos centralizado é uma máquina dedicada que mantém os arquivos compartilhados e oferece serviços de acesso aos clientes. Quando o processo P se move para outra máquina, ele precisa fazer uma solicitação ao servidor de arquivos centralizado para acessar o arquivo F. O servidor de arquivos, por sua vez, verifica a vinculação arquivo-máquina para localizar a máquina correta onde o arquivo está disponível.
- O processo P pode acessar o arquivo F por meio de chamadas de procedimento remoto (RPC) ou outros mecanismos de comunicação interprocesso para interagir com o servidor de arquivos centralizado. O servidor de arquivos gerencia o acesso concorrente ao arquivo F e garante que as operações de leitura e gravação sejam realizadas de forma consistente.
- Essa abordagem com um servidor de arquivos centralizado permite que o processo P acesse o arquivo F independentemente de sua localização atual e fornece um mecanismo centralizado para garantir o acesso seguro e coordenado aos arquivos compartilhados em um sistema distribuído. No entanto, é importante considerar que essa abordagem centralizada pode criar um ponto único de falha e gargalo no sistema se o servidor de arquivos não estiver disponível ou não puder lidar com a demanda de solicitações de acesso aos arquivos.

10.  _Descreva com detalhes como pacotes TCP fluem no caso de transferencia TCP junto com informacoes sobre enderecos de fonte e destino nos varios cabecalhos._        
 - Estabelecimento de conexao(handshake) cliente envia SYN(contém o endereço de IP e a porta do cliente como endereço de origem e o endereço de IP e a porta do servidor como endereço de destino.) para o Servidor. 
 - O servidor responde com um pacote SYN-ACK (synchronize-acknowledgment), que possui o endereço de IP e a porta do servidor como endereço de origem e o endereço de IP e a porta do cliente como endereço de destino.
 - O cliente finaliza o processo de estabelecimento enviando um pacote ACK (acknowledgment) para o servidor.
 - A conexão TCP está estabelecida e pronta para a transferência de dados
 - [?]Somente isso?


## Exercicios Capitulo 4

1. _Em muitos protocolos de camadas, cada camada tem seu proprio cabecalho. Por certo seria mais edficiente ter um unico cabecalho a frente de cada mensagem que contivesse todos os controles do que ter todos esses cabecalhos separadados. Por que isso nao é feito?_            
Embora a sobreposição de cabeçalhos possa parecer redundante em um primeiro momento, a abordagem em camadas traz várias vantagens em termos de modularidade, flexibilidade, interoperabilidade e abstração, tornando-a amplamente adotada em sistemas de comunicação e redes.[?]
   

2. _Por que servicos de comunicacao de nivel de transporte frequentemente sao inadequados para construir aplicacoes distribuidas?_            
Por possuir algumas limitacoes, que vao de encontro com os objetivos de sistemas distribuidos, como escalabilidade que protocolos como TCP podem ter problemas de escalabilidade quando o numero de conexoes silmultaneas sao muito grandes, podendo levar a gargalos e atrasos na comunicacao. Alem disso o TCP por exemplo nao suporta multicast nativamente, podendo ser uma desvantagem para aplicacoes que precisam transmitir dados para varios receptores ao mesmo tempo.

3. _Um servico multicast confiavel permite que um remetente passe mensagens confiaveis para um conjunto de receptores. O melhor lugar para esse servico é uma camada de middleware, ou ele deveria ser parte de uma camada de nivel mais baixo?_               
Apesar do multicast poder ser implementado na camada de rede, o que seria mais dificil de ser implementado, o melhor seria implementar no middleware abstraindo complexidade; melhor e mais eficientegerenciamento de rescursos necessarios para o multicast confiavel; pode ser erutilizado em varias aplicacoes distribuidas promovendo a interoperabilidade

4. _Considere que um cliente chama uma RPC assincrona para um servidor e na sequencia, espera ate que o servidor retorne um resultado usando outra RPC assincrona. Essa abordagem é o mesmo que deixar o cliente executar um RPC normal?_            
Nao. Em resumo, a abordagem com RPC assíncrona é mais flexível e permite que o cliente execute outras tarefas enquanto aguarda as respostas dos servidores, tornando-a mais adequada para cenários em que a eficiência e a utilização de recursos são importantes. A abordagem de RPC síncrona pode ser mais simples de implementar em alguns casos, mas pode levar a um uso menos eficiente dos recursos do cliente. A escolha entre as duas abordagens depende dos requisitos específicos da aplicação e das necessidades de desempenho.


5. _Suponha que voce so possa utilizar primitivas de comunicacao transiente sincrona. Como voce implementaria primitivas para comunicacao transiente assincrona?_       
- Se estamos limitados a primitivas de comunicação transiente síncrona, não seria possível implementar primitivas para comunicação transiente assíncrona sem violar o conceito de transitoriedade. Nesse cenário, a comunicação assíncrona não seria possível usando apenas primitivas síncronas, pois as primitivas síncronas esperam a conclusão da operação antes de continuar.
- Portanto, para obter verdadeira comunicação transiente assíncrona, seria necessário contar com primitivas de comunicação assíncrona que permitam enviar e receber mensagens sem bloquear a execução do programa principal.

6. _Suponha que voce so possa utilizar primitivas de comunicacao transiente assincronas, entre elas apenas uma primitiva assincrona receive. COmo vc implementaria primitivas para comunicacao transiente sincrona?_        
Podemos usar um mecanismo de espera ativa (polling) para simular a sincronicidade. Para fazer isso, podemos criar uma primitiva assíncrona de envio (send) que também aguarde uma confirmação de recebimento antes de continuar. No entanto, é importante notar que a espera ativa (polling) pode ser ineficiente, pois o programa pode consumir recursos de forma desnecessária enquanto aguarda a confirmação. Em sistemas reais, é preferível utilizar mecanismos de espera mais eficientes, como callbacks ou eventos, para lidar com a comunicação assíncrona de forma síncrona.
- Vamos implementar as primitivas de comunicação transiente síncrona: send_sync e receive_sync.

**a.** send_sync(message):
   - Envie a mensagem usando a primitiva assíncrona de envio (send). Aguarde ativamente até que um sinal de confirmação seja recebido (usando a primitiva assíncrona de receive). Quando o sinal de confirmação for recebido, continue a execução
**b.** receive_sync():
   - Aguarde ativamente até que uma mensagem seja recebida (usando a primitiva assíncrona de receive). Quando a mensagem for recebida, envie um sinal de confirmação usando a primitiva assíncrona de envio (send). Retorne a mensagem recebida.


7. _Faz sentido implementar `comunicacao persistente assincrona` por meio de RPCs?_     
Sim. As RPCs são uma forma de comunicação entre processos distribuídos, permitindo que um processo chame uma função em um processo remoto como se fosse uma chamada de procedimento local.
- A comunicação persistente assíncrona é útil quando é necessário manter uma conexão de longa duração entre o cliente e o servidor para troca contínua de informações. Nesse caso, as chamadas RPC assíncronas podem ser utilizadas para enviar solicitações e receber respostas de forma não bloqueante, permitindo que o cliente continue a executar outras tarefas enquanto aguarda as respostas. p.e como em aplicativos de mensagens instantâneas, jogos online ou sistemas de notificações em tempo real.

8. _Tabelas de roteamento no WebSphere da IBM e em muitos outros sistemas de enfileiramento de mensagens sao configuradas manualmente. Descreva um modo simples de fazer isso automaticamente._     
[?]

9.  _Com comunicacao persistente, um receptor geralmente tem seu proprio buffer local no qual mensagens podem ser armazenadas quando o receptor nao estiver em execucao. Para criar tal buffer, talvez seja preciso especificar seu tamanho. Cite um argumento a favor e outro contra a especificacao de tramanho._     
Vantagem: permite um melhor dimensionamento e controle de recursos. Evitar desperdício de recursos ou problemas de desempenho devido a buffers muito pequenos ou muito grandes.

10. _Explique por que a comunicacao transiente sincrona tem problemas inerentes de escalabilidade e como podem ser resolvidos?_             
- A comunicação transiente síncrona tem problemas inerentes de escalabilidade porque cada chamada de comunicação bloqueia o processo chamador até que a resposta seja recebida. Isso significa que, se houver várias chamadas síncronas concorrentes, o processo chamador ficará bloqueado esperando as respostas de todas as chamadas antes de poder continuar sua execução. Isso pode levar a um desperdício significativo de recursos e atrasos no processamento.
- A comunicação transiente síncrona pode ser problemática em ambientes distribuídos devido aos bloqueios e atrasos que podem ocorrer. Adotar abordagens como comunicação assíncrona, uso de threads, comunicação por lotes e sistemas de mensageria pode melhorar a escalabilidade e a eficiência da comunicação em tais ambientes.


11. _Quando se trata de procurar arquivos em um sistema P2P nao estruturado. Pode ser util restringir a busca a nodes que tenham arquivos semelhantes aos seus. Explique como o gossiping pode ajudar a achar esses nodes?_     
Nós iniciam consultas (gossips) aleatórias para seus vizinhos, que podem retransmiti-las para outros nós. Essa disseminação aleatória das consultas permite que elas alcancem nós com arquivos semelhantes. Quando um nó encontra um arquivo correspondente, ele responde à consulta. Embora eficiente na disseminação de informações, o gossiping não garante encontrar todos os nós com arquivos semelhantes, dependendo da topologia da rede e outros fatores. É uma abordagem útil, mas deve ser combinada com outras técnicas para melhorar a eficiência da busca.

## Exercicios Capitulo 5

1. _De um exemplo de onde um endereco de uma entidade E precisa ser resolvido para um outro endereco a fim de poder acessar E._     



2. _Voce consideraria que um URL como http://www.acme.org/index.html é independente de localizacao? E o endereco http://www.acme.nl/index.html ?_     


3. _De alguns exemplos de identificadores verdadeiros._     


4. _Um identificador tem permissao de conter informacoes sobre a entidade que ele referencia?_      


5. _Observe o sistema Chord como mostra a figura 5.4 e considere que o node 7 acabou de se juntar a rede. Qual seria sua tabela de derivacao? Haveria quaisquer mudancas em outras tabelas de derivacao?_       


6. _Considere um sistema Chorde baseado em DHT no qual k bits de um espaco de identificadores de m bits foram reservados para designar a superpares. Se os identificadores forem designados aleatoriamentem, quantos super pares podemos esperar que um sistema de N nodes tenha?_      


7. _Se inserirmos um node em um sistema Chord, precisaremos atualizar imediatamente todas as tabelas de derivacao?_     


8. _Qual é a maior desvantagem de consultas recursivas na resolucao de uma chave em um sistem a baseado em DHT?_   


9.  _Servidores de nomes de nivel alto em DNS, isto é, servidores de nomes que implementam nodes no espaco de nomes DNS que esta proximo da raiz, em geral nao suportam resolucaso recursiva de nomes. Poderiamos esperar grande aprimoramento de desempenho caso suportassem?_      


10. _Explique como o DNS pode ser usado para implementar uma abordagem baseada em localizacao nativa para localizar hospedeiros moveis._        



## Exercicios Capitulo 6

