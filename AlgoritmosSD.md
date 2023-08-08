
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

```python
```


### Resolucao de Nomes Recursiva [Montar de acordo com slides]
```python
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

### Exclusao Mutua [?INCOMPLETO]

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
    findSucessor(Q.id,No)
}

No response(Node){
    if OP == Join
    Node.Join(Q)
    Atualizar finger table
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

### Acesso RPC: Pseudocodigo [?]
### Relogio [?]