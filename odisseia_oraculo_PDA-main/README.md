# Odisseia do Oráculo

Jogo web didático para explorar **Autômatos com Pilha (PDA)**. O viajante percorre três câmaras, coleta cristais e chaves e precisa respeitar a ordem LIFO da mochila para abrir passagens e alcançar os objetivos.

## Como jogar

- Use **WASD** ou as **setas** para movimentar o personagem pelo mapa.
- Ou digite uma sequência no campo de roteiro. Cada caractere é um símbolo de entrada da fase atual.
- Selecione **Processar Roteiro** para executar automaticamente ou **Passo** para avançar um símbolo de cada vez.
- Abra **DICAS** para carregar exemplos de sequências aceitas e rejeitadas.
- Acompanhe em tempo real o estado do PDA, a transição destacada, o símbolo lido, a fita de entrada e a pilha.
- Uma sequência rejeitada oferece a opção de analisar o resultado ou reiniciar. Ao vencer uma fase, escolha **Próxima fase**. Depois da terceira, **Jogar novamente** retorna à fase 1.
- O botão de áudio liga ou desliga a trilha ambiente e os efeitos das ações.

## Símbolos e pilha

A pilha começa com o marcador de fundo `Z0`. O topo é mostrado primeiro na interface. `A` representa um cristal e `X` uma chave. Uma operação só é possível quando o símbolo necessário está no topo.

| Símbolo | Ação | Operação na pilha |
| --- | --- | --- |
| `*` | Coletar cristal | Empilha `A` |
| `a` | Coletar chave | Empilha `X` |
| `s` | Usar poção/barreira | Desempilha `A` |
| `b` | Abrir redemoinho/arco | Desempilha `X` |
| `c` | Atravessar o portal | Exige `Z0`; aceita nas fases 1 e 2 |
| `t` | Abrir o baú | Exige `Z0`; aceita na fase 3 |

Cada fase usa o mesmo conjunto de sete estados, mas possui alfabeto, regras de transição, mapa e objetivo próprios. A aceitação ocorre em `q6`.

$$M_i = (Q, \Sigma_i, \Gamma, \delta_i, q_0, Z_0, F)$$

- $Q = \{q_0,q_1,q_2,q_3,q_4,q_5,q_6\}$
- $\Gamma = \{Z_0,A,X\}$
- $F = \{q_6\}$
- $\lambda$ representa uma transição sem consumir símbolo de entrada.
- Em cada transição, a interface apresenta o estado anterior e o seguinte; operações de pilha seguem estritamente o topo.

## Fases

### Fase 1: O Portal Crepuscular

Alfabeto: $\Sigma_1 = \{*,a,s,b,c\}$. A aventura começa com um cristal. O viajante pode coletar outros cristais e chaves, consumir `A` com `s` e `X` com `b`. A pilha precisa voltar a `Z0` para liberar o portal `c`.

Roteiros de dica: `*sabc` (aceito), `**aabbssc` (aceito), `*sbc` (rejeitado por falta de chave) e `**aabbc` (rejeitado porque sobram cristais).

### Fase 2: A Câmara das Duas Barreiras

Alfabeto: $\Sigma_2 = \{*,a,s,b,c\}$. Depois de coletar cristal e chave, use `b` para abrir o primeiro arco. No corredor, colete o cristal indicado por `*`, abra duas barreiras com `ss` e atravesse o portal com a pilha vazia.

Roteiros de dica: `*ab*ssc` (aceito), `*aabb*ssc` (aceito, demonstra duas chaves e a ordem LIFO), `*a*bc` (rejeitado por tentar empilhar um cristal sobre uma chave) e `*ab*sc` (rejeitado porque sobra um cristal).

### Fase 3: O Tesouro do Oráculo

Alfabeto: $\Sigma_3 = \{*,a,s,b,t\}$. Colete primeiro a chave `a` e depois um ou mais cristais `*`. Use `s` para consumir os cristais na ordem LIFO; só então `b` consome a chave e libera o caminho até o baú `t`. O baú só aceita quando resta apenas `Z0`.

Roteiros de dica: `a*sbt` (aceito), `a**ssbt` (aceito, com dois cristais), `a*bt` (rejeitado porque o cristal ainda está no topo) e `a*sb*t` (rejeitado por continuar a fita depois da transição final).

## Regras de transição

A tabela completa da fase atual é exibida no botão **Guia & Diagrama JFLAP**. A notação usada é `(estado, entrada, topo) → (próximo estado, nova pilha)`. Ao empilhar, o novo símbolo é colocado sobre o topo atual; ao desempilhar, o topo é removido.

Resumo dos caminhos principais:

| Fase | Caminho de estados e operações principais | Aceitação |
| --- | --- | --- |
| 1 | `q0 --* / push A--> q1 --s / pop A--> q2 --a / push X--> q3 --b / pop X--> q4 --λ--> q5` | `q5 --c, Z0--> q6` |
| 2 | `q0 --* / push A--> q1 --a / push X--> q1 --b / pop X--> q2 --* / push A--> q3 --ss / pop A--> q4 --λ--> q5` | `q5 --c, Z0--> q6` |
| 3 | `q0 --a / push X--> q1 --* / push A--> q1 --s / pop A--> q2 --b / pop X--> q3 --λλ--> q5` | `q5 --t, Z0--> q6` |

Nas fases 1 e 2, `q4` avança espontaneamente para `q5` quando a pilha contém apenas `Z0`. Na fase 3, duas transições espontâneas levam de `q3` por `q4` até `q5` depois que a chave e os cristais foram consumidos. As transições adicionais e laços de cada fase estão listados no guia da aplicação.

## Tecnologias

- HTML, CSS e JavaScript, em uma aplicação de página única.
- Canvas 2D para o mapa, personagem, itens e efeitos visuais.
- Web Audio API para trilha ambiente e efeitos sonoros, sem arquivos de áudio externos.
- Tailwind CSS carregado pela CDN para utilitários de interface.

## Executar localmente

Abra `index.html` em um navegador moderno. Uma conexão com a internet é necessária para carregar o Tailwind CSS pela CDN.

## Publicar no GitHub Pages

1. Envie `index.html` e `README.md` para a branch principal do repositório.
2. No GitHub, abra **Settings → Pages**.
3. Em **Build and deployment**, selecione **Deploy from a branch**, escolha a branch principal e a pasta raiz (`/root`), e salve.
4. Aguarde o deploy e abra o endereço indicado pelo GitHub Pages, normalmente `https://<usuario>.github.io/<repositorio>/`.

## Estrutura

- `index.html`: interface, mapas, regras dos PDAs, áudio e lógica do jogo.
- `README.md`: documentação do projeto.
