# Problema dos Dois Generais

Olá a todos, bem-vindos de volta. Na última palestra, introduzimos sistemas distribuídos, observando alguns exemplos concretos, como a web e o RPC, que são exemplos de sistemas cliente-servidor. Nesta palestra, vamos levar as coisas para um modelo um pouco mais abstrato e mais geral, e vamos falar sobre modelos de sistema para sistemas distribuídos, que são descrições das suposições que fazemos ao projetar um algoritmo para rodar em um sistema distribuído.

Um modelo de sistema é muito importante porque, como discutimos, as coisas podem dar errado em sistemas distribuídos: nós podem falhar, redes podem falhar, e assim por diante. 

Precisamos ser precisos sobre quais falhas estamos assumindo que são possíveis e quais falhas estamos assumindo que não são possíveis. 

Começaremos isso examinando dois experimentos mentais clássicos de sistemas distribuídos: o Problema dos Dois Generais e o Problema dos Generais Bizantinos.

Vamos começar agora com o Problema dos Dois Generais. 

É como uma analogia militar, e o cenário desse experimento mental é que temos dois exércitos, cada um controlado por um general, e esses exércitos querem atacar e capturar uma cidade. 

A cidade é bem defendida, e se apenas um dos exércitos atacar de cada vez, esse exército será derrotado. 

Portanto, é muito importante que, se os dois generais forem atacar, eles ataquem ao mesmo tempo, porque assim, se ambos os exércitos atacarem ao mesmo tempo, eles sabem que vão vencer.

Você pode ver nesta tabela verdade o que acontece: 

- está tudo bem se nenhum dos exércitos atacar
- se apenas um dos dois exércitos atacar, então tudo vai dar terrivelmente errado. 

O que realmente queremos é que um exército ataque se e somente se o outro exército atacar, ou seja, ambos ataquem juntos.

O que torna isso difícil é que os dois generais não podem simplesmente conversar e concordar com o plano de quando atacar. 
Eles só podem se comunicar por meio de mensageiros. 
Esses mensageiros são pessoas que correm pela floresta, e ao correr pela floresta, eles podem ser capturados pelas forças da cidade. 
Assim, sempre que um general envia um mensageiro ao outro general, essa mensagem pode ou não chegar, e não há como o remetente da mensagem saber se a mensagem chegou, exceto recebendo uma resposta.

O problema aqui é o seguinte: imagine, por exemplo, que o General Um decidiu atacar em 10 de novembro. Ele envia uma mensagem ao General Dois dizendo: "Vamos atacar em 10 de novembro, você concorda com isso?". 

O General Dois recebe a mensagem e responde: "Sim, estou a bordo, vamos atacar juntos em 10 de novembro". 

No entanto, infelizmente, a mensagem de resposta é capturada. A mensagem inicial (a solicitação) chega, mas a resposta é perdida. Assim, o General Um não recebe uma resposta.

Este é um cenário do que poderia acontecer. Aqui está outro cenário: também pode acontecer que o General Um envie a mensagem "ataque em 10 de novembro" para o General Dois, mas essa mensagem inicial seja perdida e nunca chegue ao General Dois. O General Dois não recebe nenhuma mensagem, então ele também não vai responder. O resultado final é que o General Um também não recebe uma resposta. Em ambos os casos, a única coisa que o General Um observa é a ausência de resposta.

No entanto, o General Um não sabe se não há resposta porque a mensagem inicial não chegou ou se a resposta foi perdida. 

Há uma grande diferença entre os dois casos: do ponto de vista do General Dois, no primeiro caso, o General Dois concordou em atacar; no segundo caso, o General Dois nem sabe sobre o ataque. Eles parecem iguais do ponto de vista do General Um, mas muito diferentes do ponto de vista do General Dois.

Vamos tentar projetar um algoritmo que, apesar de tudo, leve os dois generais a um acordo. Vamos pensar primeiro do ponto de vista do General Um. O General Um basicamente tem duas escolhas: ou o General Um vai atacar sempre, independentemente de qualquer resposta ser recebida, ou o General Um vai esperar e só atacar se receber uma resposta do General Dois.

Vamos começar com o primeiro caso: o General Um sempre ataca, mesmo que nenhuma resposta seja recebida. Neste caso, o General Um quer ter certeza de que o General Dois também vai atacar, porque caso contrário, o General Um estará em uma situação problemática. 

Poderíamos dizer que o General Um vai enviar muitos e muitos mensageiros ao General Dois, todos dizendo "ataque a essa hora, ataque, ataque". Se um desses mensageiros conseguir passar, as coisas provavelmente estarão bem, porque o General Dois sabe que o General Um sempre vai atacar. Assim, o General Dois sabe que é seguro para ele também ir para a batalha, mesmo sem responder ao General Um, pois afinal, o General Um prometeu que sempre atacaria. No entanto, pode acontecer que todos os mensageiros sejam perdidos. Neste caso, o General Dois não sabe sobre o ataque, e o General Um acaba indo para a batalha sozinho e perde. Isso significa que esta primeira opção de o General Um sempre atacar não é realmente ótima.

Vamos considerar a alternativa: o General Um não promete atacar sempre, mas só atacará se receber uma resposta positiva do General Dois. 

Neste caso, o General Um está seguro, pois sabe que só entrará em batalha se o General Dois também entrar em batalha. Mas agora, se você pensar do ponto de vista do General Dois, o General Dois sabe que o General Um só atacará se a resposta do General Dois para o General Um conseguir passar, porque afinal, o General Um está esperando por essa resposta. Então, o General Dois está exatamente na mesma situação em que o General Um estava na primeira opção. Ou o General Dois deve se comprometer a atacar sempre, caso em que ele corre o risco de estar sozinho na batalha, ou o General Dois esperará uma resposta do General Um. Mas agora o General Um tem que responder, e assim você acaba com cadeias potencialmente infinitas de "sim, sim, vou atacar", "ok, vou atacar se você atacar", "sim, ok, eu vou atacar, mas só se você também atacar", e assim por diante.

Eles têm que enviar mensagens de volta e para frente, e você acaba com uma cadeia infinita antes que haja alguma certeza de que eles realmente atacarão juntos. 

Isso é chamado em sistemas distribuídos de problema de não haver conhecimento comum. Não há conhecimento no sistema de que um nó sabe e o outro nó sabe que o primeiro nó sabe, e que o primeiro nó sabe que o segundo nó sabe que o primeiro nó sabe, e assim por diante. 

Você pode construir essas cadeias arbitrárias, e o resultado final é que, não importa quantas sequências finitas de mensagens enviemos de volta e para frente, nunca teremos certeza absoluta de que o General Um atacará se e somente se o General Dois atacar. Você pode construir uma certeza probabilística gradualmente crescente, talvez dependendo das suas suposições sobre se os mensageiros são capturados ou não, mas é realmente impossível alcançar uma certeza completa aqui.

Vamos pegar este experimento mental abstrato e aplicá-lo a um exemplo concreto. 

Na última palestra, tivemos o exemplo de uma loja online fazendo uma requisição RPC a um serviço de pagamento para cobrar um cartão de crédito. 
O que realmente queremos aqui é que a loja online despache as mercadorias se e somente se o cliente pagar pelas mercadorias. 

Você pode imaginar que, se a loja online despachar as mercadorias, mas o serviço de pagamento não cobrar o cartão de crédito, a loja ficará infeliz, pois acabou de dar algumas mercadorias de graça. Se a loja online não despachar as mercadorias, mas o serviço de pagamento cobrar o cartão de crédito, o cliente ficará infeliz, pois foi cobrado sem receber nenhuma mercadoria.

Então, o que realmente queremos é algo extremamente semelhante ao Problema dos Dois Generais aqui: que a loja online despache as mercadorias se e somente se o serviço de pagamento cobrar o cartão. E, como você pode imaginar, a comunicação RPC entre a loja online e o serviço de pagamento se parece muito com os mensageiros correndo pela floresta no Problema dos Dois Generais, o que significa que as mensagens podem ser perdidas — seja uma ou outra. E, portanto, não é possível para a loja online e o serviço de pagamento alcançar a certeza de que uma ação acontecerá se e somente se a outra ação acontecer.

Na prática, as lojas online funcionam, mas a razão pela qual elas funcionam é porque existem uma série de salvaguardas de segundo nível que garantem um resultado razoável. Por exemplo, se for descoberto que o cartão foi cobrado, mas a loja online não tem mais os produtos em estoque, a loja online simplesmente enviará um e-mail de desculpas dizendo: "Oh, desculpe, estamos sem estoque, reembolsamos seu cartão", e assim está tudo bem. É possível sair dessa situação porque a cobrança é, na verdade, uma ação revogável. É possível reembolsar a cobrança e, portanto, é possível voltar a um estado seguro onde nem o despacho das mercadorias nem o pagamento efetivamente aconteceram.

Outra opção é que o serviço de pagamento pode ou não ter cobrado um cartão, e então a loja online, quando a rede é reparada e as mensagens podem passar novamente, a loja online verifica com o serviço de pagamento dizendo: 
"Você realmente cobrou esse cartão ou não, porque eu nunca tive notícias suas se você cobrou ou não?". 
E o que provavelmente acontecerá é que o serviço de pagamento sempre irá em frente e cobrará o cartão, mesmo que não seja certo que a loja online irá despachar as mercadorias. Isso ocorre porque, neste caso, está tudo bem, pois o pagamento pode ser reembolsado, se necessário. Essa é a maneira pela qual este problema de compras online não é exatamente o mesmo que o Problema dos Dois Generais, mas, no entanto, o Problema dos Dois Generais ilustra essa questão da incerteza que temos em um sistema distribuído quando não temos certeza se as mensagens foram entregues ou não.

# Problema dos Generais Bizantinos

O segundo experimento mental que quero discutir é chamado de Problema dos Generais Bizantinos.
Antes de entrar nos detalhes, uma questão de pronúncia: é "bizantino" ou "bizantino"? Pelo que sei, a pronúncia britânica padrão desta palavra é "byzantine", e "byzantine" é a pronúncia americana. No entanto, na computação, parece ser mais comum usar "Byzantine", então vou me ater à pronúncia "Byzantine" porque é o que todos os outros na computação parecem fazer.
O Problema dos Generais Bizantinos é semelhante, à primeira vista, ao Problema dos Dois Generais:

Novamente, temos generais liderando exércitos.

Novamente, os exércitos querem atacar uma cidade.

Neste caso, podemos ter três ou mais exércitos, ou seja, qualquer número de exércitos.

Como antes, os generais se comunicam por meio de mensageiros.

Como antes, os exércitos querem concordar sobre a data do ataque para garantir que ataquem ao mesmo tempo.
No entanto, o problema é facilitado de uma forma e dificultado de outra:

Facilitado: Assumimos que a comunicação é confiável. Os mensageiros não são capturados, e qualquer mensagem enviada será de fato recebida pelo destinatário apropriado.

Dificultado: Agora assumimos que alguns dos generais não são leais; eles são traidores, são maliciosos. Eles tentarão ativamente minar os outros generais, mentindo, enganando e se comportando mal de qualquer maneira que desejarem. Eles podem até trabalhar juntos. Apesar disso, queremos que os generais honestos cheguem a um acordo sobre o ataque à cidade.
Exemplo do problema causado por generais maliciosos:

O General Um envia uma mensagem de "ataque" aos Generais Dois e Três.

O General Três recebe a mensagem de "ataque" como esperado.

O General Dois recebe a mensagem de "ataque", mas então envia uma mensagem ao General Três dizendo "recuar".

Neste caso, o General Dois está mentindo: ele afirma que o General Um enviou uma mensagem de "recuar", quando na verdade o General Um enviou uma mensagem de "ataque". Assim, o General Dois é o malicioso.
Dificuldade para o General Três:

Do ponto de vista do General Três, é difícil saber o que realmente está acontecendo.

Igualmente, poderia ter acontecido que o General Um enviou uma mensagem de "ataque" ao General Três e uma mensagem de "recuar" ao General Dois.

Neste segundo cenário, o General Dois estaria apenas repetindo fielmente a mensagem de "recuar" que recebeu do General Um, o que o tornaria honesto.

Nesse caso, o General Um seria o malicioso, enviando comandos contraditórios aos Generais Dois e Três.

A questão central do problema é que, do ponto de vista do General Três, é impossível distinguir entre esses dois cenários. Consequentemente, é impossível para o General Três saber se o General Um está sendo malicioso ou se o General Dois está sendo malicioso.
Dado esse cenário em que os nós (generais) podem mentir, o desejo do Problema dos Generais Bizantinos é que todos os generais honestos cheguem a um acordo.

Os generais honestos não sabem quem são os generais maliciosos.

No entanto, assumimos um número máximo (f) de generais maliciosos de um total de n generais.

Os generais maliciosos podem saber quem são os outros generais maliciosos e podem até trabalhar juntos de forma coordenada para tentar enganar os generais honestos.

Nosso requisito é que os generais honestos concordem com um plano (por exemplo, todos atacarem no mesmo dia), embora não possamos fazer nenhuma afirmação sobre o que os generais maliciosos farão, pois eles podem se comportar de maneiras arbitrárias.
Resultado Típico sobre o Problema dos Generais Bizantinos:

Se tivermos um máximo de f generais maliciosos, precisamos de pelo menos 3f + 1 generais no total para tolerar esses f generais maliciosos.

Isso significa que menos de um terço do número total de generais pode ser malicioso.
◦
Se tivermos três generais e um for malicioso (f=1), o problema é insolúvel.
◦
Para tolerar um sistema com um general malicioso, precisamos de pelo menos quatro generais no total (três honestos e um malicioso).
◦
Se quisermos um sistema em que dois generais possam ser maliciosos (f=2), precisamos de sete generais (cinco honestos e dois maliciosos).
A Criptografia ajuda?:

O problema se torna um pouco mais fácil se assumirmos que podemos usar criptografia, como assinaturas digitais.

Uma assinatura digital é uma forma de mensagem em que pode ser provado que uma certa parte enviou uma certa mensagem.

Por exemplo, isso permitiria ao General Dois provar ao General Três qual comando o General Um enviou ao General Dois, convencendo o General Três de que o General Dois é realmente honesto.

A criptografia ajuda, mas não torna o Problema dos Generais Bizantinos magicamente simples; ele permanece difícil mesmo com criptografia.
Relevância Prática do Problema dos Generais Bizantinos:

O problema pode ser adaptado para um exemplo concreto de uma loja online, um serviço de pagamento e um cliente.

Nesse cenário de relacionamento triangular, queremos que todas as três partes concordem sobre o status de uma transação ou pedido. A loja online deve enviar as mercadorias apenas se concordar com o serviço de pagamento que o pagamento realmente ocorreu e se o cliente concordou que realmente pediu o item e com o valor cobrado no cartão.

Na vida real, as relações de confiança entre essas três partes podem ser bastante complicadas:
◦
Do ponto de vista da loja online, o cliente precisa ser tratado como potencialmente malicioso, pois se fosse cegamente confiável, poderiam ocorrer fraudes.
◦
O serviço de pagamento pode não confiar totalmente na loja online, pois uma loja fraudulenta poderia ser configurada para processar transações com números de cartão de crédito roubados sem enviar mercadorias.
◦
As relações de confiança podem ser assimétricas (por exemplo, a loja confia no serviço de pagamento, mas o serviço de pagamento não confia totalmente na loja).

Essas situações em que uma parte não confia na outra, mas ainda assim querem realizar algo, mostram que o comportamento bizantino é real e prático.

Embora o Problema dos Generais Bizantinos seja uma simplificação (por exemplo, tratando todos os generais como simétricos), ele é um ponto de partida útil para estudar situações em que os participantes não confiam totalmente uns nos outros.
Origem do Termo "Bizantino":

O nome "Bizantino" vem do Império Bizantino, também conhecido como Bizâncio, o antigo Império Romano do Oriente. Sua capital era Constantinopla, que era conhecida como Bizâncio (hoje Istambul, na Turquia).

No início do século XX, o termo "bizantino" passou a ser usado para descrever cenários excessivamente complicados, incrivelmente burocráticos ou potencialmente astutos (como legislação tributária).

Não há evidências históricas de que o Império Bizantino fosse mais ou menos malicioso do que qualquer outro império, mas o termo adquiriu esse significado muito antes de ser usado no contexto da computação.

# Modelos de Sistema para Sistemas Distribuídos

Vimos agora o Problema dos Dois Generais e o Problema dos Generais Bizantinos.

No Problema dos Dois Generais, assumimos que os nós são honestos, mas as mensagens podem ser perdidas.

No Problema dos Generais Bizantinos, assumimos que as mensagens são confiáveis, mas os nós podem ser desonestos.
Agora, devemos juntar os dois e tentar projetar modelos de sistema para sistemas nos quais tanto os nós quanto as redes podem falhar de várias maneiras. Esta é a base de qualquer algoritmo em sistemas distribuídos. Precisamos assumir certas coisas sobre as propriedades que o sistema terá.
Tipicamente, fazemos isso olhando para três áreas diferentes de interesse em um modelo de sistema:
1.
Precisamos descrever como assumimos que a rede se comportará.
2.
Vamos assumir como os nós se comportarão.
3.
Precisamos assumir como o tempo e o sistema funcionarão, o que afeta tanto as redes quanto os nós.
Vamos começar com as redes:

Redes são não confiáveis por muitas razões.
◦
Sobrecarga temporária: um buffer pode ficar cheio e uma mensagem ser descartada.
◦
Problemas fundamentais: alguém pode desconectar o cabo de rede errado, interrompendo a rede.
◦
Interações curiosas com a natureza: Google observou tubarões mordendo cabos de fibra óptica submarinos. Pessoas observaram vacas pisando em cabos de fibra óptica e criando "torções" que impedem a passagem da luz.
Em sistemas distribuídos, ao projetar algoritmos, tipicamente assumimos alguma forma de comunicação ponto a ponto:

Mensagens têm um remetente e um destinatário.

São enviadas por um link bidirecional, permitindo que o destinatário responda ao remetente.
Podemos escolher quão confiável assumimos que este link é:
1.
Link confiável: Assumimos que as mensagens sempre são entregues. Se uma mensagem é enviada, ela é recebida, e se é recebida, foi enviada previamente. O link não perde nem fabrica mensagens, mas as mensagens podem ser reordenadas. Esta é uma suposição muito forte.
2.
Link com perda justa (Fair Loss Link): Sempre que uma mensagem é enviada, ela tem uma probabilidade não zero de ser entregue. Ela pode ou não ser entregue, mas se o envio for repetido, eventualmente será entregue. Não há suposições sobre o tempo que levará.
3.
Arbitrário: O link de rede pode fazer qualquer coisa. Pode ser modelado como um adversário ativo e malicioso que modifica o tráfego da rede.
◦
Isso acontece na internet real, por exemplo, em um Wi-Fi de cafeteria, onde o proprietário pode interferir nos pacotes.
◦
Eles podem olhar sua comunicação, modificar pacotes, gravar e reproduzir pacotes, fingir ser um site e, claro, descartar mensagens.
◦
Um link arbitrário pode fazer qualquer uma dessas coisas, e esta é uma modelagem razoável de como a internet funciona hoje.
Um conceito final de terminologia é a partição de rede:

Uma partição de rede ocorre quando os nós continuam funcionando, mas o link de comunicação entre eles é interrompido.

Geralmente, essa interrupção é por um período finito de tempo, o que significa que a partição eventualmente é reparada e a comunicação pode ser restabelecida. No entanto, o período de interrupção pode ser substancial.

É possível que um subgrupo de nós se comunique e outro subgrupo se comunique, mas os dois grupos não possam se comunicar entre si.
É quase possível converter um modelo de link em outro:

Um link com perda justa pode ser transformado em um link confiável. Isso é feito simplesmente repetindo as mensagens até que sejam entregues e desduplicando as mensagens no lado do destinatário. Isso pode levar muito tempo, mas eventualmente a mensagem passará.

É quase possível converter um link arbitrário em um link com perda justa usando um protocolo criptográfico como TLS (Transport Layer Security).
◦
O TLS é o que fornece o cadeado verde no navegador e o 's' em HTTPS.
◦
Ele garante que, se a comunicação for bem-sucedida, ela não foi adulterada, é autêntica e privada (criptografada).
◦
A única coisa que não se pode fazer é se o adversário decidir bloquear toda a comunicação para sempre.
◦
Mas, se o adversário interferir apenas em um número finito de pacotes, um link arbitrário pode ser "atualizado" para um link com perda justa. A partir daí, com tentativas e desduplicação, ele pode ser transformado em um link confiável.
A próxima parte do modelo de sistema é como os nós se comportam:
1.
Falha por pane (Crash Fault): Assumimos que um processo pode falhar a qualquer momento e, uma vez que falha, nunca mais voltará. É uma suposição simplificadora, mas pode ser precisa em casos de falha catastrófica de hardware (ex: telefone que cai na privada). Qualquer estado em memória é perdido, mas dados salvos em armazenamento não volátil sobrevivem.
2.
Falha por pane com recuperação (Crash Recovery): Nó pode falhar e depois voltar após algum tempo. O estado em memória é perdido, mas dados em armazenamento estável permanecem. Ainda é possível que um nó falhe e nunca mais volte.
3.
Bizantino: Assim como no Problema dos Generais Bizantinos, um nó com falha bizantina desvia do algoritmo. Ele pode fingir seguir o algoritmo ou agir de forma maliciosa. Não há restrição para o comportamento de um nó com falha bizantina; ele pode fazer o que quiser.
Terminologia: Um nó pode ser categorizado como defeituoso (faulty) ou correto (correct).

Um nó é defeituoso se falhar (no modelo de pane) ou se desviar do algoritmo (no modelo bizantino).

Um nó é correto se não for defeituoso.

Um nó não sabe necessariamente se outro nó é correto ou defeituoso.
A terceira parte de um modelo de sistema são os modelos de tempo (timing):
1.
Síncrono: Assumimos que tudo leva um tempo conhecido.
◦
Uma mensagem enviada pela rede tem um tempo máximo após o qual será entregue ou perdida.
◦
Nós sempre executam seu código a uma velocidade conhecida, com um limite superior para o tempo de execução de cada passo. Esta é uma suposição muito forte.
2.
Assíncrono: Nenhuma suposição de tempo é feita.
◦
Uma mensagem enviada pode levar 20 anos para chegar; não há limites superiores para a latência da mensagem.
◦
Não há suposições sobre a velocidade de execução dos nós; um nó pode pausar sua execução a qualquer momento e retomar depois. Isso pode acontecer devido a suspensão de threads.
3.
Parcialmente Síncrono: Um compromisso entre os modelos síncrono e assíncrono.
◦
Por certos períodos de tempo, o sistema se comporta como um modelo síncrono, e por outros, como um assíncrono.
◦
Certas coisas não podem ser resolvidas em um modelo assíncrono, portanto, às vezes, suposições de tempo são necessárias.
◦
No entanto, assumir um modelo síncrono é perigoso, pois redes reais se comportam de forma parcialmente síncrona. Se o sistema demorar mais do que o limite superior para entregar uma mensagem, o algoritmo pode falhar catastroficamente.
◦
No modelo parcialmente síncrono, assume-se que na maior parte do tempo o sistema é bem-comportado (síncrono), mas ocasionalmente fica "estranho", com mensagens demorando muito ou nós lentos, para depois retornar ao estado síncrono.
Problemas de assumir sincronia:

Mesmo em redes rápidas, mensagens podem demorar mais ocasionalmente.
◦
Uma mensagem pode ser perdida e precisar ser reenviada. Em caso de partição de rede, pode-se ter que esperar minutos, horas ou até dias.
◦
Congestionamento e filas na rede.
◦
Reconfiguração de rede pode fazer pacotes ficarem presos por mais de um minuto.
◦
Mesmo em datacenters bem gerenciados, é possível ter latências de mensagens extremamente altas.

A velocidade de execução dos nós também pode ser interrompida.
◦
Trocas de contexto e suspensão temporária de threads (por exemplo, devido a inversão de prioridade).
◦
Coleta de lixo "stop the world" em linguagens como Java, onde todos os threads são pausados enquanto a coleta de lixo ocorre. Essas pausas podem durar minutos se o heap for grande.
◦
Outras causas de atrasos variáveis no sistema operacional, como falhas de página.

Sistemas operacionais de tempo real podem fornecer garantias de agendamento, mas a maioria dos sistemas distribuídos é construída em sistemas operacionais de propósito geral que não dão tais garantias. Mesmo com RTOS, é difícil garantir as premissas de tempo.
Portanto, para a maioria dos sistemas distribuídos práticos, não podemos assumir nenhum limite superior sobre quanto tempo uma mensagem pode demorar para ser entregue ou um processo para executar um passo.
Resumo do modelo de sistema:

Rede: Pode ser assumida como confiável, com perda justa ou arbitrária.

Nós: Podem ter comportamento de pane (crash stop - nunca retorna), recuperação de pane (crash recovery - pode retornar), ou bizantino (comportamento malicioso).

Tempo: Pode ser síncrono, parcialmente síncrono ou assíncrono.
Essas escolhas de abstração são cruciais. Se um algoritmo for projetado sob certas suposições (ex: modelo de recuperação de pane), mas o sistema real tiver nós bizantinos, o algoritmo pode ser destruído. Se houver a possibilidade de comportamento bizantino, o algoritmo deve ser projetado para tolerá-lo. Da mesma forma, fazer suposições de sincronia em um sistema que é apenas parcialmente síncrono é muito perigoso e pode anular as garantias do algoritmo.

# Tolerância a Falhas

Este segmento aborda o contexto prático de como lidar com falhas e criar serviços altamente disponíveis, como uma loja online que precisa estar ativa 24 horas por dia, 7 dias por semana.
Disponibilidade e Tolerância a Falhas

Disponibilidade: É a fração de tempo durante a qual um serviço está funcionando corretamente.
◦
É medida em "noves" (nines), por exemplo:
▪
99% de disponibilidade significa 3 a 4 dias de indisponibilidade por ano.
▪
99.9% (três noves) significa um máximo de 9 horas de indisponibilidade por ano.
▪
Redes telefônicas fixas mais antigas são projetadas para cinco noves, o que indica uma alta confiabilidade.

SLO (Service Level Objective): É o objetivo de disponibilidade que um serviço estabelece para si mesmo, especificando, por exemplo, a porcentagem de requisições que devem receber uma resposta correta dentro de um tempo máximo.

SLA (Service Level Agreement): É um contrato entre um serviço e seus clientes que especifica o nível de serviço esperado.

Tolerância a falhas (Fault Tolerance): É a maneira típica de alcançar alta disponibilidade em sistemas distribuídos.
◦
Uma falha (fault) ocorre quando alguma parte do sistema não está funcionando, como uma falha de nó (crash) ou uma partição de rede.
◦
O objetivo é que o sistema seja capaz de tolerar um certo número de falhas. Não é razoável esperar que um sistema tolere todas as falhas (ex: todos os nós falharem ao mesmo tempo).
◦
Por exemplo, um sistema pode continuar funcionando se menos da metade de seus nós falharem (permitindo que 1 de 3 ou 2 de 5 falhem).
◦
Ponto único de falha (Single Point of Failure): É um componente único que, se falhar, torna todo o sistema indisponível. Um sistema bem projetado evita pontos únicos de falha, o que significa que remover qualquer componente não deve derrubar o sistema inteiro.
Detecção de Falhas

Para tolerar falhas, geralmente é preciso primeiro detectar uma falha e depois lidar com ela.

Detector de falhas (Failure Detector): É um mecanismo (pode ser um algoritmo de software ou hardware) para detectar se outro nó está com falha.

Implementação com Timeouts: A forma mais comum de implementar detectores de falhas é usando timeouts.
◦
Envia-se uma mensagem a um nó e espera-se uma resposta dentro de um determinado tempo.
◦
Se nenhuma resposta for recebida dentro do tempo limite, o nó é considerado "morto" ou falho.

Limitações dos Timeouts: Em sistemas parcialmente síncronos ou assíncronos, um timeout não indica necessariamente que um nó falhou.
◦
Pode ser que a mensagem inicial ou a resposta tenha sido perdida ou atrasada na rede.
◦
O nó pode estar vivo, mas temporariamente pausado (ex: devido a uma pausa de coleta de lixo "stop the world" em Java que pode durar minutos, ou outras interrupções do sistema operacional).
◦
É impossível diferenciar se a ausência de resposta é por um problema de rede, um atraso aleatório, ou um crash real do nó.

Detector de Falhas Perfeito: Um detector de falhas perfeito só pode ser construído em um modelo de sistema síncrono e assumindo apenas falhas por pane (crash stop), sem comportamento bizantino.

Detector de Falhas Eventualmente Perfeito: Em um sistema parcialmente síncrono, o melhor que se pode ter é um detector de falhas eventualmente perfeito.
◦
Este detector pode estar errado temporariamente, gerando falsos positivos (detecta um timeout mesmo que o nó não tenha falhado) ou falsos negativos (demora para detectar um crash).
◦
No entanto, eventualmente, o detector de falhas rotula um nó como falho se e somente se ele realmente falhou. Ou seja, qualquer suspeita temporária de falha cessará se o nó estiver correto, e se um nó falhar, ele acabará sendo detectado como tal.
◦
Apesar de não ser perfeito, um detector de falhas eventualmente perfeito é suficiente para construir algoritmos úteis.
Em resumo, a tolerância a falhas é fundamental para a alta disponibilidade, e a detecção de falhas, embora desafiadora em ambientes assíncronos e parcialmente síncronos, pode ser alcançada com detectores eventualmente perfeitos.
