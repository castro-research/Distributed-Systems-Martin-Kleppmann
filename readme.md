# Aulas da Cambridge University em inglês e português

Lecture 1 - Introdução, Redes de computadores e RPC

Lecture 2 - Modelos de Sistemas distribuídos

Lecture 3 - Relógios, Tempo e Ordenação de Eventos

Lecture 4 - Broadcast Protocols e Relógio lógico

Lecture 5 - Replicação

Lecture 6 - Consenso

Lecture 7 - Consistencia de Replicação

Lecture 8 - Casos de estudo

OBS: As aulas são todas em inglês, e todo o conteúdo do slide está no dist-sys-notes.pdf
Eu acabei passando para português usando Google Document Translate e acabei corrigindo alguns erros de tradução, mas não está perfeito. No entanto, no Lecture 3 eu já estava acompanhando mais o inglês, por que ficou mais comodo para mim. Então não vou focar em ter uma tradução perfeita, mas sim em entender o conteúdo.

# Playlist

https://www.youtube.com/watch?v=UEAMfLPZZhE&list=PLeKd45zvjcDFUEv_ohr_HdUFe97RItdiB&index=1

## Department of Computer Science and Technology - Cambridge University

https://www.cl.cam.ac.uk/teaching/2021/ConcDisSys/

### Ementa

Principais palestrantes: Dr. David Greaves, Dr. Martin Kleppmann  
Destinado a: Part IB CST 50%, Part IB CST 75%  
Horas: 16  
Horas sugeridas de supervisão: 4  
Pré-requisitos: Programação Orientada a Objetos, Sistemas Operacionais  
Este curso é pré-requisito para: Computação em Nuvem, Tecnologias de Ledger Distribuído: Fundamentos e Aplicações, Sistemas Móveis e de Sensores  
Questões de exames anteriores

**Objetivos**  
Este curso aborda dois tópicos intimamente relacionados, Sistemas Concorrentes e Sistemas Distribuídos, ao longo de 16 aulas. O objetivo da primeira metade do curso é introduzir conceitos de controle de concorrência e suas implicações para o design e implementação de sistemas. Os objetivos da segunda metade do curso são estudar as características fundamentais dos sistemas distribuídos, incluindo seus modelos e arquiteturas; as implicações para o design de software; algumas das técnicas utilizadas para construí-los; e os detalhes resultantes de bons algoritmos e aplicações distribuídas.

#### Aulas: Concorrência

Introdução à concorrência, threads e exclusão mútua. Introdução a sistemas concorrentes; threads; entrelaçamento; preempção; paralelismo; ordenação de execução; processos e threads; threads de kernel vs. usuário; threads M:N; atomicidade; exclusão mútua; e locks de exclusão mútua (mutexes).  
Composição de autômatos. Paralelismo síncrono e assíncrono; consistência sequencial; rendezvous. Segurança, vivacidade e deadlock; os Filósofos Jantando; fundamentos de hardware para atomicidade: instruções test-and-set, load-linked/store-conditional e fence. Algoritmo padaria de Lamport.  
Padrões comuns de design: semáforos, produtor-consumidor e MRSW. Locks e invariantes; semáforos; sincronização de condição; alocação de N recursos; produtor-consumidor de duas partes e generalizado; locks Multi-Leitor, Escrita Única (MRSW).  
CCR, monitores e concorrência na prática. Regiões críticas condicionais (CCR); monitores; variáveis de condição; semântica signal-wait vs. signal-continue; concorrência na prática (kernels, pthreads, Java).  
Deadlock e garantias de vivacidade. Offline vs. online; verificação de modelos; gráficos de alocação de recursos; verificação de ordem de locks; prevenção, evitação, detecção e recuperação de deadlock; livelock; inversão de prioridade; herança de prioridade.  
Concorrência sem dados compartilhados; transações. Objetos ativos; passagem de mensagens; espaços de tuplas; CSP; e modelos de atores. Operações compostas; transações; ACID; isolamento; e serializabilidade.  
Mais sobre transações. Grafos históricos; escalonamentos bons e ruins; isolamento vs. isolamento estrito; bloqueio em 2 fases; rollback; ordenação por timestamp (TSO); e controle de concorrência otimista (OCC).  
Recuperação de falhas, programação sem locks e memória transacional. Log de escrita antecipada, checkpoints e recuperação. Programação sem locks. Memórias transacionais de hardware e software.

#### Aulas: Sistemas Distribuídos

Introdução a sistemas distribuídos; RPC. Vantagens e desafios de sistemas distribuídos; atraso ilimitado e falha parcial; protocolos de rede; transparência; sistemas cliente-servidor; chamada de procedimento remoto (RPC); marshalling; linguagens de definição de interface (IDLs).  
Modelos de sistema e falhas. Modelos de rede síncronos, parcialmente síncronos e assíncronos; falhas crash-stop, crash-recovery e bizantinas; falhas, defeitos e tolerância a falhas; problema dos dois generais.  
Tempo, relógios e ordenação de eventos. Relógios físicos; UTC; sincronização de relógios, desvio e compensação; Protocolo de Tempo de Rede (NTP). Tempo lógico; relação happens-before; relógios de Lamport; relógios vetoriais.  
Replicação. Tolerância a falhas; replicação baseada em líder, multi-líder e sem líder; sistemas de quórum; consistência de réplicas; linearizabilidade; Teorema CAP; consistência eventual; garantias de sessão.  
Middleware e protocolos. Grupos de processos; broadcast FIFO, ordem causal e ordem total; middleware orientado a mensagens e orientado a objetos; exclusão mútua distribuída.  
Consenso e transações distribuídas. Eleições de líder; consenso; resultado FLP; Paxos e Raft; replicação de máquina de estados; transações distribuídas; protocolos de commit atômico; commit em 2 fases.  
Estudos de caso. Sistema de Arquivos de Rede (NFS); Dynamo da Amazon; tecnologias de datacenter do Google (ex: MapReduce, Spanner); serviços de computação em nuvem.  
Tópicos avançados. Tipos de Dados Replicados Sem Conflito (CRDTs); tolerância a falhas bizantinas; sistemas peer-to-peer; segurança em sistemas distribuídos.

**Objetivos**  
Ao final da parte de Sistemas Concorrentes do curso, os alunos devem:

- Entender a necessidade de controle de concorrência em sistemas operacionais e aplicações, tanto exclusão mútua quanto sincronização de condição;
- Entender como a multithread pode ser suportada e as implicações de diferentes abordagens;
- Estar familiarizado com o suporte oferecido por várias linguagens de programação para controle de concorrência e ser capaz de julgar o escopo, implicações de desempenho e possíveis aplicações das várias abordagens;
- Estar ciente de que a alocação dinâmica de recursos pode levar a deadlock;
- Entender o conceito de transação; as propriedades das transações, como podem ser implementadas e como seu desempenho pode ser otimizado com base em suposições otimistas;
- Entender como as propriedades de persistência das transações são tratadas por meio de logging;
- Ter uma compreensão de alto nível da evolução do uso de concorrência em software no estudo de caso do kernel do sistema operacional.

Ao final da parte de Sistemas Distribuídos do curso, os alunos devem:

- Entender a diferença entre concorrência em memória compartilhada e sistemas distribuídos;
- Entender as propriedades fundamentais dos sistemas distribuídos e suas implicações para o design de sistemas;
- Entender noções de tempo, incluindo relógios lógicos, relógios vetoriais e sincronização de tempo físico;
- Estar familiarizado com várias abordagens de replicação de dados e serviços, bem como o conceito de consistência de dados;
- Entender os efeitos da grande escala na provisão de serviços fundamentais e os trade-offs decorrentes da escala;
- Compreender as implicações de falhas de nós individuais e de comunicação de rede no cálculo distribuído;
- Estar ciente de uma variedade de modelos e abstrações de programação para sistemas distribuídos, como RPC, middleware e broadcast de ordem total;
- Estar familiarizado com uma variedade de algoritmos distribuídos, como consenso e commit em duas fases;
- Estar familiarizado com vários estudos de caso em design de sistemas distribuídos, incluindo o Sistema de Arquivos de Rede (NFS), o Protocolo de Tempo de Rede (NTP), o Dynamo da Amazon e os sistemas MapReduce e Spanner do Google.

**Leitura recomendada**  
Bacon, J. e Harris, T. (2003). Operating systems: distributed and concurrent software design. Addison-Wesley.  
Bacon, J. (1997). Concurrent systems. Addison-Wesley.  
Kleppmann, M. (2017). Designing data-intensive applications. O’Reilly.  
Tanenbaum, A.S. e van Steen, M. (2017). Distributed systems, 3ª edição. disponível online.  
Cachin, C., Guerraoui, R. e Rodrigues, L. (2011) Introduction to Reliable and Secure Distributed Programming. Springer (2ª edição).
