# Pontos de integração com Firebase

Os pontos abaixo correspondem aos comentários `TODO [...]` no código. Pesquise pela etiqueta no arquivo se as linhas mudarem.

| Arquivo e linha aprox. | Etiqueta | Trabalho para integração |
|---|---|---|
| `AppData.java:41` | `[FIREBASE-AUTH]` | Substituir login/cadastro/sair em `FakeAuthRepository` por Firebase Auth (`signInWithEmailAndPassword`, `createUserWithEmailAndPassword`, `signOut`) e sessão persistente. |
| `AppData.java:43` | `[FIRESTORE-WRITE]` | Após cadastro Auth, criar `usuarios/{uid}` com `nome` e `email`, sem `perfil`. |
| `AppData.java:45` | `[PERFIL]` | Ler `usuarios/{uid}.perfil` e nome; usuário sem perfil deve sair e receber aviso. |
| `Ui.java:10` | `[REGRA-SEGURANCA]` | A proteção visual de cada tela deve ser reforçada nas regras Firestore usando o perfil de `usuarios/{uid}`. |
| `AppData.java:49` | `[FIRESTORE-LISTENER]` | `addSnapshotListener` em `itens`, ordenar por descrição e atualizar todas as telas. |
| `AppData.java:50` | `[FIRESTORE-READ]` | Buscar `itens/{codigoBarras}` e checar existência pelo ID. |
| `AppData.java:51` | `[FIRESTORE-WRITE]` | Gravar `itens/{codigoBarras}` com `codigoBarras, descricao, unidade, local, saldo, minimo`; criar movimento inicial se saldo inicial for positivo. |
| `AppData.java:52` | `[REGRA-SEGURANCA]` | Firestore Rules devem impedir alteração direta do saldo fora das transações de entrada/atendimento. |
| `AppData.java:53` | `[FIRESTORE-TRANSACAO]` | Entrada: atualizar `itens/{codigo}.saldo` e criar `movimentos/{id}` (`itemId,tipo=ENTRADA,quantidade,uid,usuarioNome,data`) atomicamente. |
| `AppData.java:55` | `[FIRESTORE-LISTENER]` | Observar `requisicoes`, aplicando filtros por `status` ou `solicitanteUid` e ordenação por data. |
| `AppData.java:56` | `[FIRESTORE-WRITE]` | Criar `requisicoes/{id}` com item, quantidade, destino, observação, dados do solicitante, status e data. |
| `AppData.java:57` | `[REGRA-NEGOCIO]` | Não rejeitar pedido por saldo insuficiente no envio; validar apenas no atendimento. Manter destinos válidos e código de barras único como ID de item. |
| `AppData.java:58` | `[FIRESTORE-TRANSACAO]` | Em uma transação, validar saldo, baixar `itens/{itemId}.saldo`, criar movimento `SAIDA` e atualizar status/atendente/data em `requisicoes/{id}`. |
| `AppData.java:59` | `[REGRA-SEGURANCA]` | Regras: solicitante só cria pedidos com o próprio UID e lê os próprios; só almoxarife atende e altera saldo; saldo não fica negativo; solicitante não lê movimentos. |
| `AppData.java:61` | `[FIRESTORE-LISTENER]` | Observar `movimentos` por `itemId`, em ordem decrescente de `data`; na ficha usar `limit(3)`. |
| `AppData.java:64` | `[REGRA-NEGOCIO]` | Preservar status derivado: saldo zero = zerado; saldo menor que mínimo = abaixo; demais = em estoque. |

## Regras de segurança pendentes

Além do comentário associado ao atendimento, proteger `itens.saldo` contra atualizações fora das operações de entrada/atendimento, restringir leitura de `movimentos` a almoxarifes e impedir que usuários alterem `usuarios/{uid}.perfil`. O controle visual por perfil no app não substitui essas regras.
