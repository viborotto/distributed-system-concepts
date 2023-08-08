
## Capitulo 4

### RPC: Exemplo (RMI – Remote Method Invocation)

```java
public interface ServicoMatriz extends Remote {
    public int[][] inverte(int[][] matrizGigante) throws RemoteException;
}
```

```java
import etml.*
public class ServicoImpl extends UnicastRemoteObject
                         implements ServicoMatriz {
        
        public int[][] inverte(int[][] matrizGigante)
                                            throws RemoteException {
            Matrix m = new Matrix(matrizGigante);
            return m.invert();
        }
}
```

```java
public class Servidor {
    public static void main(String[] args) {
        ServicoMatriz sm = new ServicoImpl();
        LocateRegistry.createRegistry(1099);
        Registry reg = LocateRegistry.getRegistry();
        reg.bind("rmi://127.0.0.1/servicomat", sm);
    }
}
```

```java
public class Cliente {
    public static void main(String[] args) {
        Registry reg = LocateRegistry.getRegistry();
        ServicoMatriz sm = (ServicoMatriz)
        reg.lookup("rmi://127.0.0.1/servicomat");
        int [][] mat = {};
        int[][] inv = sm.inverte(mat);
    }
}
```

### RPC: Exemplo (gRPC – Google RPC)

```java
// The greeter service definition.
service Greeter {
    // Sends a greeting
    rpc SayHello (HelloRequest) returns (HelloReply) {}
}
// The request message containing the user's name.
message HelloRequest { string name = 1; }
// The response message containing the greetings
message HelloReply { string message = 1; }
```


### Gossiping

```python
Node N #node, thread, ou processo
    n.run()
        while True: #Método main executado por Node n
            sleepMs(x)
            executeGossip() #Cada certo tempo acorda e faz algo (gossip)
    
    n.executeGossip():
        p = selectNode()
        infoLocal = getLocalInfo()
        p.shareInfo(infoLocal, n) #compartilha via RPC para o node p
    
    n.shareInfo(info, q): # Orientado a evento (muitos Nós podem chamá-lo ao mesmo tempo)
        addInfo(info)
        infoLocal = getLocalInfo()
        q.updateInfo(infoLocal) #Via RPC ao Nó q

    n.updateInfo(info): #Orientado a evento (muitos Nós podem chamá-lo ao mesmo tempo)
        addInfo(info)
```

## Capitulo 5

### Resolucao de Nomes Iterativa [Montar de acordo com slides]

```java
Cliente {
    Diretorio resolveNome(Diretorio dir, ListaNomes nomes) {
        EnderecoServidor enderecoAtual = EnderecoServidor(Server0)

        para cada nome em nomes {
            enderecoAtual = enviarRequisicao(enderecoAtual, dir, nome)
            dir = buscarDiretorio(enderecoAtual)
        }

        retornar dir
    }

    EnderecoServidor enviarRequisicao(EnderecoServidor endereco, Diretorio dir, Nome nome) {
        Requisicao req = criarRequisicao(dir, nome)
        EnderecoServidor novoEndereco = enviarParaServidor(endereco, req)
        retornar novoEndereco
    }

    Diretorio buscarDiretorio(EnderecoServidor endereco) {
        // Realizar busca no servidor para obter o diretório associado ao endereço
    }
}

Servidor {
    Map<Diretorio, EnderecoServidor> tabelaDiretorios

    EnderecoServidor processarRequisicao(Requisicao req) {
        Diretorio dir = req.diretorio
        EnderecoServidor endereco = tabelaDiretorios[dir]
        retornar endereco
    }
}
```


### Resolucao de Nomes Recursiva [Montar de acordo com slides]
```java
Cliente {
    Diretorio resolveNome(Diretorio dir, ListaNomes nomes) {
        Diretorio resultado = enviarRequisicaoRecursiva(Server0, dir, nomes)
        retornar resultado
    }

    Diretorio enviarRequisicaoRecursiva(EnderecoServidor endereco, Diretorio dir, ListaNomes nomes) {
        if (nomes.vazia()) {
            retornar dir
        } else {
            Nome proximoNome = nomes.removerPrimeiro()
            Requisicao req = criarRequisicao(dir, proximoNome)
            Diretorio dir1 = enviarParaServidor(endereco, req)
            EnderecoServidor proximoEndereco = obterEnderecoServidor(dir1)
            retornar enviarRequisicaoRecursiva(proximoEndereco, dir1, nomes)
        }
    }

    Diretorio obterEnderecoServidor(Diretorio dir) {
        // Realizar busca no servidor para obter o endereço associado ao diretório
    }
}

Servidor {
    Map<Diretorio, EnderecoServidor> tabelaDiretorios

    Diretorio processarRequisicao(Requisicao req) {
        Diretorio dir = req.diretorio
        EnderecoServidor endereco = tabelaDiretorios[dir]
        retornar endereco
    }
}
```

## DHT Chord

```java
// Estrutura do nó n
No {
    int meuId = n
    No predecessor = null
    No sucessor = null
}

// pergunta ao nó n encontrar o successor do id procurado
procedure n.findSuccessor(id, No ini) {
    if (predecessor  nil and id  (predecessor, n]) then ini.response(id, n)
    else if (id (n, successor]) then ini.response(id, sucessor)
    else // encaminha a pergunta pelo anel
        sucessor.findSucessor(id,ini)
}

procedure n.put(id, value) {
    op = put
    findSuccessor(id)
}
procedure n.get(id) {
    op = get
    findSuccessor(id)
}
procedure n.response(id, node) {
    if (op == put) node.store(id,value)
    else if (op == get) node.retrieve(id)
}
```

```java
// Estrutura do nó n (inicial)
No {
    int meuId = n
    No predecessor = null
    No sucessor = null
}
// Modificando a estrutura do nó n (agora considerando store/retrieve)
No {
    int meuId = n
    No predecessor = null
    No sucessor = null
    KV chaves-valor = null
}
```

#### Buscas mais rapidas DHT CHORD
```java
// pergunta ao nó n encontrar o successor do id
procedure n.findSuccessor(id, No ini) {
    if (predecessor  nil and id  (predecessor, n]) then ini.response(id, n)
    else if (id (n, successor]) then ini.response(id, sucessor)
    else // encaminha a pergunta pelo anel
        m = closestPrecedingNode(id)
        m.findSucessor(id,ini)
}

// procura localmente pelo nó próximo ao id
procedure closestPrecedingNode(id) {
    for (i = M to 1 do) {
        if (FT[i] (n, id)) then
            return FT[i]
        }
    return n // meu id
}

// Estrutura do nó n (considerando store)
No {
    int meuId = n
    No predecessor = null
    No sucessor = null
    KV chaves-valor = null
}

// Modificando a estrutura do nó n (agora considerando FT)
No {
    int meuId = n
    No predecessor = null
    No sucessor = null
    KV chaves-valor = null
    No[] FT = null
}
```

## Capitulo 6

### Exclusao Mutua [INCOMPLETO]

```java
No {
    int id
    No predecessor
    No sucessor
    KV CV
    No[] FingerTable
}

No join(Q){
    id = Q.id
    findSucessor(Q.id,this)
    AtualizarFingerTable() // Chame a função de atualização da Finger Table
}

No response(Node){
    if OP == Join
    Node.Join(this)
    Atualizar finger table
    AtualizarFingerTable()
}
```


```java
Servidor {
    bool deiAcesso = False
    Cliente[] fila
    Cliente CDP
} S

S request(C){
    if deiAcesso
        CDP = C 
        C.acessa()
        deiAcesso = True
    else
        fila.add(C)
}

S release(C){
    if ()
        if fila is vazia
            deiAcesso = False
        else
            C = fila.remove()
            C.acessa()
            deiAcesso = True
            CDP = C
}
```

### Acesso RPC: Pseudocodigo [CHATGPT]

```java
Servidor {
    Map<String, Funcao> tabelaDeFuncoes // Tabela de funções disponíveis no servidor

    void registrarFuncao(nome, funcao) {
        tabelaDeFuncoes[nome] = funcao
    }

    RespostaRPC processarChamada(RPCRequest request) {
        String nomeFuncao = request.nomeFuncao
        if (tabelaDeFuncoes.contem(nomeFuncao)) {
            Funcao funcao = tabelaDeFuncoes[nomeFuncao]
            return funcao.executar(request.argumentos)
        } else {
            return RespostaRPC(erro)
        }
    }
}

Cliente {
    RespostaRPC chamarFuncaoRemota(nomeFuncao, argumentos) {
        RPCRequest request = RPCRequest(nomeFuncao, argumentos)
        Conexao conexao = estabelecerConexaoComServidor()
        conexao.enviar(request)
        RespostaRPC resposta = conexao.receberResposta()
        conexao.fechar()
        return resposta
    }
}

RPCRequest {
    String nomeFuncao
    Argumentos argumentos
}

RespostaRPC {
    Status status
    Resultado resultado
}
```


### Relogio Logico Lamport [?]

```java
Processo {
    int contadorLocal

    // Quando ocorre um evento
    eventoOcorreu() {
        contadorLocal = contadorLocal + 1
    }

    // Quando uma mensagem é enviada
    enviarMensagem(mensagem) {
        mensagem.timestamp = contadorLocal
        enviar(mensagem)
    }

    // Quando uma mensagem é recebida
    receberMensagem(mensagem) {
        contadorLocal = max(contadorLocal, mensagem.timestamp) + 1
        eventoOcorreu()
        repassarMensagem(mensagem)
    }
}

// Exemplo de uso
Processo P1, P2

// Evento ocorre em P1
P1.eventoOcorreu()
P1.enviarMensagem(mensagem)
P2.receberMensagem(mensagem)

```

