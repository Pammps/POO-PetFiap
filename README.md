# Checkpoint 5 — Bug Hunt PetFiap

> Copie este arquivo para a raiz do seu repositório com o nome **README.md**
> e preencha todas as seções.

## Identificação


| Integrante                      | RM     | Turma |
|---------------------------------|--------|-------|
| Pamella Souza da Silva Ferreira | 566172 | 2CCPH |

| Campo |                     |
|---|---------------------|
| **Total de bugs corrigidos** | 12 / 12             |
| **Total de ajustes de Clean Code** | 6 / 6               |
| **Total de testes novos escritos** | 6 / 6               |
| **Suíte final (Run As → JUnit Test)** | 26 testes, 0 falhas |

---

## Parte 1 — Bugs encontrados

> Uma linha por bug, na ordem em que você os encontrou. Use a numeração dos seus
> commits (`fix: bug01 ...`). Preencha TODAS as colunas — metade da nota está aqui.

| # | Sintoma observado (o que fiz/vi) | Causa raiz (arquivo e linha aproximada) | Correção aplicada | Conceito da disciplina |
|---|---|---|---|---|
| bug01 | HorarioOcupadoException era esperado, mas ocorreu NullPointerException|Uso de == para comparar String e LocalDateTime; o conflito não era reconhecido |Troca de == por .equals() |Comparação de objetos em Java / regra de negócio |
| bug02 |Ao buscar um atendimento inexistente, o teste esperava AtendimentoNaoEncontradoException, mas nenhuma exceção chegava ao teste. |AgendaService.java, método buscarPorId(). Um catch (Exception) capturava a exceção correta e retornava null. |Remoção do try/catch genérico, permitindo que orElseThrow() propague a exceção de negócio. |Exceções unchecked e tratamento correto de exceções. |
| bug03 |A Factory deveria criar uma Tosa quando o tipo era TOSA, mas criava um objeto Banho. |AtendimentoFactory.java, switch do método criar(), caso TOSA. |Alterado o caso TOSA para instanciar new Tosa(...). |Padrão Factory e polimorfismo. |
| bug04 |Ao criar uma consulta veterinária, os dados do pet e do tutor chegavam vazios/nulos. |ConsultaVeterinaria.java, construtor. Os parâmetros recebidos não eram enviados para o construtor da classe pai; era usado super(). |Alterado para super(protocolo, petNome, petPorte, tutorNome, dataHora). |Herança, construtor da superclasse e reutilização de código. |
| bug05 |O teste de montagem completa esperava o nome do pet, mas o objeto criado tinha petNome como null. |AtendimentoBuilder.java, método comPet(). Havia petNome = petNome em vez de atribuir ao atributo da classe. |Alterado para this.petNome = petNome. |Builder, escopo de atributos/parâmetros e uso de this |
| bug06 |O Builder aceitava a criação de um atendimento sem nome do pet, embora a regra exigisse rejeição. |AtendimentoBuilder.java, método construir(). Não havia validação do nome do pet antes de chamar a Factory. |Adicionada validação para null/vazio e lançamento de IllegalArgumentException. |Validação de entrada e Builder. |
| bug07 |O Builder aceitava a criação de um atendimento sem porte do pet. |AtendimentoBuilder.java, método construir(). Não havia validação do porte antes da criação do objeto. |Adicionada validação para null/vazio e lançamento de IllegalArgumentException. |Validação de entrada, encapsulamento de regras e Builder. |
| bug08 |O teste de Singleton mostrava que chamadas consecutivas devolviam objetos diferentes e a numeração não permanecia global. |GeradorProtocolo.java, método getInstancia(). Quando instancia era null, era criado um objeto novo sem armazená-lo no atributo estático. |A nova instância passou a ser atribuída a instancia antes de ser retornada. |Padrão Singleton e estado compartilhado. |
| bug09 |O novo teste de preço do Banho para porte pequeno retornava R$ 100,00 em vez de R$ 60,00. |Banho.java, método calcularPreco(). Os valores de PEQUENO e GRANDE estavam invertidos. |Ajustados os retornos para PEQUENO = 60, MEDIO = 80 e GRANDE = 100. |Polimorfismo e implementação de regra de negócio no Model. |
| bug10 |O novo teste de duração da Tosa retornava 30 minutos em vez de 60. |Tosa.java. O método getDuracaoMinutos(String porte) tinha assinatura diferente do método herdado getDuracaoMinutos(), portanto não era um override. |Alterado para @Override public int getDuracaoMinutos() retornando 60. |Sobrescrita (override) vs. sobrecarga (overload). |
| bug11 |O novo teste de agendamento no passado não recebia IllegalArgumentException e o repository poderia ser consultado antes da validação. |AgendaService.java, início do método agendar(). A consulta ao repository acontecia antes da validação da data/hora. |Adicionada validação de dataHora antes de consultar o repository. |Regra de negócio, ordem de validação e Mockito (verify(..., never())). |
| bug12 |O novo teste de cancelamento de atendimento concluído permitia a mudança para CANCELADO, quando a operação deveria ser recusada. |Atendimento.java, método cancelar(). O método alterava o status sem verificar o estado atual. |Cancelamento permitido somente quando o status é AGENDADO; nos demais casos é lançada StatusInvalidoException. |Máquina de estados simples, encapsulamento e exceções de negócio. |

## Parte 2 — Ajustes de Clean Code

| # | Onde estava | Qual princípio/boas práticas era violado | O que eu mudei |
|---|---|---|---|
| clean01 |AtendimentoFactory.java, parâmetros do método criar() |Nomes de variáveis pouco descritivos dificultavam a leitura e aumentavam a carga de interpretação. |Renomeei p, t, n, po, tu e d para protocolo, tipo, petNome, petPorte, tutorNome e dataHora. |
| clean02 |AtendimentoFactory.java, switch de tipos |Uso repetido de strings literais para representar os tipos, aumentando duplicação e risco de inconsistência. |Passei a usar as constantes Banho.TIPO, Tosa.TIPO e ConsultaVeterinaria.TIPO. |
| clean03 |Atendimento.java e regras de status |Strings mágicas como AGENDADO, CONCLUIDO e CANCELADO estavam espalhadas pelo código. |Criei constantes de status na classe Atendimento e substituí os literais pelos nomes das constantes. |
| clean04 |AgendaService.java, método agendar() |O Service imprimia informações diretamente no console, misturando regra de negócio com saída de diagnóstico. |Removi o System.out.println; o método continua retornando o atendimento salvo. |
| clean05 |AtendimentoController.java, bloco de fidelidade futuro |Existia código morto: um método privado não utilizado e acompanhado por regra ainda não implementada. |Removi o método calcularDescontoFidelidade() e o bloco de código futuro não utilizado. |
| clean06 |AgendaService.java e AtendimentoController.java |Field injection com @Autowired escondia as dependências e dificultava a leitura das dependências obrigatórias. |Troquei por injeção por construtor e marquei as dependências como final. |

## Parte 3 — Testes novos (regras que estavam sem cobertura)

> Uma linha por teste novo (`test: ...`). "Regra coberta" é o comportamento do
> contrato (seção 3 do enunciado) que o teste protege. Em "Resultado", diga se o
> teste ficou vermelho ao ser escrito (revelou bug — qual?) ou verde de cara
> (regra já estava correta).

| # | Teste escrito (classe.método) | Regra coberta | Resultado ao escrever (vermelho/verde) |
|---|---|---|---|
| teste01 |BanhoTest.deveCustar60ReaisParaPortePequeno() |Banho para pet PEQUENO custa R$ 60,00; a regra de preço depende do porte. |Vermelho — revelou o bug09, pois o código retornava R$ 100,00. |
| teste02 |TosaTest.deveDurar60Minutos() |Tosa possui duração de 60 minutos. |Vermelho — revelou o bug10, pois o método da Tosa não sobrescrevia o método da classe pai. |
| teste03 |ConsultaVeterinariaTest.deveCustar150ReaisIndependenteDoPorte() |Consulta veterinária custa R$ 150,00 independentemente do porte do pet. |Verde — a regra já estava correta. |
| teste04 |AgendaServiceTest.deveRecusarAgendamentoComDataHoraNoPassado() |Agendamento com data/hora no passado deve lançar IllegalArgumentException e não consultar o repository. |Vermelho — revelou o bug11 |
| teste05 |BanhoTest.deveCancelarAtendimentoAgendado() |Atendimento com status AGENDADO pode ser cancelado e passa para CANCELADO. |Verde — a regra já estava correta. |
| teste06 |BanhoTest.deveRecusarCancelamentoDeAtendimentoConcluido() |Atendimento já CONCLUIDO não pode ser cancelado e deve manter o status. |Vermelho — revelou o bug12 |

---

## Parte 4 — Perguntas de reflexão

> Responda com suas palavras, 5 a 10 linhas cada, **usando o código real do
> projeto como exemplo**. Respostas genéricas de tutorial não pontuam.

### 1. A suíte como contrato (Aula 15)
O projeto chegou com 20 testes, 9 vermelhos. Descreva como você usou as
mensagens de falha (ex.: `expected: <Rex> but was: <null>`) para caçar os bugs.
O que a suíte de testes tem de melhor do que testar tudo na mão com curl?

### Resposta 1: 
A suíte funcionou como o contrato porque cada teste dizia exatamente qual comportamento o sistema deveria apresentar. Quando apareceu uma falha como expected: <Rex> but was: <null>, eu usei o valor esperado e o valor recebido para descobrir em qual parte do código o dado estava sendo perdido. No AtendimentoBuilder, isso levou até a linha em que petNome era atribuído a ele mesmo. Em outro caso, a expectativa de HorarioOcupadoException levou à verificação da comparação entre String e LocalDateTime no AgendaService. A suíte é melhor do que testar tudo manualmente com curl porque os cenários são reproduzíveis e podem ser executados em segundos. Além disso, depois de cada correção eu conseguia verificar se uma mudança tinha criado alguma regressão nas outras regras.

### 2. Mock e injeção de dependência (Aulas 13 a 15)
No `AgendaServiceTest`, o `@Mock` cria um `AtendimentoRepository` falso e o
`@InjectMocks` o injeta no service. Explique a relação disso com o `@Autowired`
que o Spring faz em produção — quem "injeta" em cada mundo, e por que o teste
consegue rodar sem banco e sem subir o Spring?

### Resposta 2:
No teste, o @Mock cria uma implementação falsa de AtendimentoRepository controlada pelo Mockito. O @InjectMocks coloca esse mock dentro do AgendaService, então o teste consegue exercitar a regra de negócio sem precisar de um Oracle real. Em produção, o Spring é quem cria e conecta os beans, usando a configuração de injeção de dependência, e no projeto a injeção passou a ser feita por construtor. A diferença principal é quem faz a injeção: no teste, o Mockito; na aplicação, o container do Spring. Por isso o AgendaServiceTest não precisa iniciar o Spring nem abrir conexão com banco. O mock também permite verificar comportamentos como verify(repository, never()), garantindo que o repository não foi chamado em um caminho que deveria ser interrompido antes.

### 3. `==` vs `.equals()` (Aula 7)
Um dos bugs fazia o agendamento duplicado passar pela verificação de conflito.
Explique por que `==` entre Strings e `LocalDateTime` falhou aqui, por que ele
"funciona por sorte" com literais como `"Rex"`, e o que a sua correção mudou.

### Resposta 3:
Em Java, == compara referências quando estamos trabalhando com objetos, enquanto .equals() é usado para comparar o conteúdo de acordo com a implementação da classe. No bug do AgendaService, o pet e a data podiam ter exatamente os mesmos valores, mas estar em objetos diferentes na memória. Por isso a comparação com == falhava mesmo quando o horário era o mesmo. Com Strings como Rex, às vezes o == pode parecer funcionar por causa do pool de Strings e do reaproveitamento de literais, mas isso não deve ser usado como regra. No teste, o LocalDateTime foi criado como outro objeto com o mesmo valor justamente para expor esse problema. A correção para .equals() faz a verificação pelo valor e deixa a regra de conflito funcionar como esperado.

### 4. Sobrescrita vs sobrecarga (Aula 7)
Um dos bugs compilava sem nenhum erro: um método parecia sobrescrever
`getDuracaoMinutos`, mas na verdade criava uma assinatura nova. Explique a
diferença entre override e overload nesse caso e por que a anotação `@Override`
teria impedido o bug.

### Resposta 4:
O problema da Tosa aconteceu porque a classe pai tinha o método getDuracaoMinutos() sem parâmetros, enquanto a Tosa criou getDuracaoMinutos(String porte). Como as assinaturas são diferentes, o método da Tosa não sobrescrevia o método herdado: ele criava outro método, caracterizando sobrecarga. Por isso, quando o código trabalhava com a referência de Atendimento, continuava usando o método da classe pai, que retornava 30 minutos. Ao trocar a assinatura para getDuracaoMinutos() e adicionar @Override, a Tosa passou a participar corretamente do polimorfismo e retornar 60 minutos. A anotação @Override teria evitado o bug porque o compilador acusaria erro caso não existisse um método correspondente na superclasse. Foi um exemplo de bug que compilava normalmente e só apareceu quando a regra foi testada.

### 5. Singleton manual vs bean do Spring (Aula 14)
O `GeradorProtocolo` é um Singleton escrito à mão e causou um dos bugs.
Explique o que ele garante, qual foi o bug, e por que o `AgendaService`
(`@Service`) não corre o mesmo risco no container do Spring.

### Resposta 5:
O GeradorProtocolo é um Singleton implementado manualmente por meio de um atributo estático que deveria guardar a única instância criada. O bug aconteceu porque getInstancia() fazia return new GeradorProtocolo() sem salvar esse objeto em instancia, então chamadas seguintes criavam novas instâncias e a contagem podia ser reiniciada. A correção foi guardar a nova instância no atributo estático antes de retorná-la. O objetivo do Singleton nesse projeto é garantir um gerador único para manter a sequência global dos protocolos. Já o AgendaService é um bean gerenciado pelo Spring com @Service, e suas dependências são fornecidas pelo container. Nesse caso, a criação e o ciclo de vida do objeto ficam sob responsabilidade do Spring, em vez de uma implementação manual do padrão.

### 6. Cobertura de testes: onde parar? (Aula 15)
Dos 6 testes novos que você escreveu, alguns ficaram vermelhos (revelaram
bugs) e outros verdes de cara (regras já corretas). Vale a pena manter os que
ficaram verdes? Em um projeto real com prazo, o que você priorizaria testar:
caminho feliz, caminhos de erro, ou 100% de cobertura? Justifique.

### Resposta 6:
Eu manteria os seis testes novos mesmo quando eles ficaram verdes de primeira, porque eles registram regras que antes não estavam protegidas contra regressões. Um exemplo foi o teste do preço da consulta, que já estava correto, mas agora documenta explicitamente a regra de R$ 150,00. Em um projeto real, eu priorizaria primeiro os caminhos de negócio mais importantes e os caminhos de erro, principalmente aqueles que podem gerar dados incorretos ou estados inválidos. O caminho feliz também precisa de cobertura, mas sozinho não mostra como o sistema se comporta quando recebe entradas inesperadas. Eu não usaria 100% de cobertura como único objetivo, porque percentual de cobertura não garante que todos os comportamentos importantes estejam bem testados. O mais importante é escolher testes que protejam as regras críticas e mantenham confiança para fazer mudanças no código.

---

## Parte 5 — Espaço livre (opcional)

Alguma dificuldade, dúvida ou comentário sobre o checkpoint?


```
A principal dificuldade foi diferenciar o sintoma apresentado pelo teste da causa raiz do problema e programar novos testes para soluções.
```
