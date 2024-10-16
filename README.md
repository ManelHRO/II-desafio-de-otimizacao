# Descrição do Hardware e Software Utilizados

## Sumário
- [Descrição do Hardware e Software Utilizados](#descrição-do-hardware-e-software-utilizados)
  - [Software](#software)
  - [Hardware](#hardware)
  - [Instâncias Utilizadas](#instâncias-utilizadas)
- [Descrição Detalhada da Metaheurística e Variações](#descrição-detalhada-da-metaheurística-e-variações)
  - [Fundamentação Teórica](#fundamentação-teórica)
  - [Implementação e Adaptações](#implementação-e-adaptações)
    - [Passos Principais da Implementação](#passos-principais-da-implementação)
  - [Variações Testadas](#variações-testadas)
  - [Justificativa para as Escolhas](#justificativa-para-as-escolhas)
  - [Parâmetros e Configurações](#parâmetros-e-configurações)
- [Resultados](#resultados)
- [Discussões e Análise Crítica](#discussões-e-análise-crítica)

## Software
- *Visual Studio Code* foi utilizado.
- O algoritmo foi testado em um sistema operacional *Windows*.

## Hardware
- *Processador:* AMD Ryzen 3500U
- *Memória RAM:* 8 GB
- *SSD:* 256 GB

## Instâncias Utilizadas
As principais instâncias utilizadas foram:
- *Scholl (BPP - CSP)*
- *Schwerin (BPP - CSP)*

Retiradas do [BPPLib - A Bin Packing Problem Library](https://site.unibo.it/operations-research/en/research/bpplib-a-bin-packing-problem-library)

# Descrição Detalhada da Metaheurística e Variações

## Fundamentação Teórica
A metaheurística testada foi o *Simulated Annealing (SA). Essa metaheurística é uma técnica inspirada no processo físico de recozimento de metais, no qual um material é aquecido até um ponto de fusão e depois resfriado lentamente, de forma controlada, para atingir um estado final de menor energia, ou seja, uma forma mais estável. Este conceito foi adaptado para problemas de otimização combinatória, como o **Bin Packing Problem*, onde o objetivo é minimizar o número de caixas usadas para armazenar objetos de tamanhos variados, respeitando uma capacidade máxima por caixa.

Em SA, o algoritmo explora diferentes soluções possíveis (estado atual) e as perturba para gerar novas soluções (vizinhança). Em cada iteração, a escolha da nova solução depende de dois fatores:
1. *Se a nova solução for melhor* (ou seja, mais próxima de uma solução ótima), ela é aceita.
2. *Se for pior*, pode ainda ser aceita com uma certa probabilidade, que depende da temperatura atual. A temperatura diminui ao longo do tempo, reduzindo a chance de aceitar soluções piores conforme o algoritmo avança.

Isso permite que o algoritmo escape de mínimos locais no início da execução e se concentre em otimizar a solução nas iterações finais.

## Implementação e Adaptações
O algoritmo foi implementado utilizando uma função objetiva que calcula o preenchimento das caixas e visa minimizar o número de caixas usadas, equilibrando a distribuição dos objetos nelas. A função de avaliação mede a proporção do uso das caixas, ou seja, o quão bem preenchidas estão.

### Passos Principais da Implementação
1. *Inicialização:*  
   Os objetos são alocados nas caixas utilizando o algoritmo *First Fit Decreasing (FFD)*, que primeiramente ordena de forma decrescente o array de peso dos itens e tenta inserir cada item na primeira caixa disponível que tenha espaço suficiente. Se não houver nenhuma caixa com a capacidade necessária para colocar o item da vez, é criada uma caixa com capacidade máxima e, logo em seguida, é feita a inserção desse item.
   
2. *Geração de Vizinhança:*  
   A cada iteração, uma caixa é escolhida aleatoriamente, e um item dessa caixa (escolhido de forma aleatória) é movido para outra caixa que tenha capacidade disponível. Se nenhuma caixa tiver espaço suficiente, a mesma solução é mantida.
   
3. *Critério de Aceitação:*  
   - Se a solução vizinha (solução gerada pela função gerar_vizinhos) for melhor que a atual, ela é aceita.
   - Se for pior, é aceita com uma probabilidade que depende da diferença entre as soluções e da temperatura.
   
4. *Função de Avaliação:*  
   A função f(caixas) avalia a qualidade de uma solução calculando a proporção de preenchimento das caixas, que é a razão entre o volume ocupado e a capacidade máxima da caixa. Essa média indica o quão eficiente é o uso das caixas. Quanto maior essa média (próxima de 1), melhor uso do espaço.
   
5. *Refrigeração:*  
   A temperatura diminui multiplicativamente após cada iteração, garantindo que o algoritmo explore menos com o tempo. Como o valor atribuído à temperatura foi *0.9*, ela esfria mais devagar, pois quanto mais próximo de 1, mais lentamente ela esfria.

## Variações Testadas
- *Taxa de Resfriamento:*  
  Variamos a taxa de resfriamento *alpha* entre *0.95* e *0.8* para observar como isso impacta a convergência.
  - *Taxas mais lentas* (como 0.95) resultaram em uma melhor exploração de soluções, porém com maior tempo de execução.
  - *Taxas mais rápidas* (como 0.8) apresentaram uma menor exploração de soluções, porém com uma redução no tempo de execução.
  
- *Tamanho da Vizinhança:*  
  Foi testado mover mais de um objeto por iteração (modificar múltiplas caixas simultaneamente), mas isso não apresentou melhorias significativas e aumentou a complexidade.

## Justificativa para as Escolhas
- *Função de Avaliação:*  
  Permite medir o balanceamento das caixas, refletindo melhor a eficiência da solução.
  
- *Estratégia de Vizinhança Simples:*  
  Escolhida para garantir que o algoritmo tenha iterações rápidas, permitindo uma maior exploração do espaço de soluções em menos tempo.
  
- *Implementação de Condicional:*  
  Foi criado um if que auxilia na questão do tamanho da melhor solução local e da solução vizinha. Se o tamanho da solução vizinha for menor do que a melhor solução local, a melhor solução local passa a ser a solução vizinha.

## Parâmetros e Configurações
- *Temperatura inicial (T):* 1000  
  Permite uma alta taxa de aceitação inicial.
  
- *Taxa de Resfriamento (alpha):* 0.9  
  Para que o resfriamento da temperatura seja mais lento.
  
- *Número de Iterações por Temperatura (sMAX):* 500  
  Garante uma exploração adequada em cada nível de temperatura.
  
- *Capacidade Máxima das Caixas (capMAX):*  
  Modificada de acordo com o input.

# Resultados
- *Instâncias Pequenas (menos de 100 objetos):*  
  O algoritmo conseguiu rapidamente encontrar soluções com poucas caixas e um bom balanceamento de preenchimento (média acima de 0.85).
  
- *Instâncias Maiores (mais de 500 objetos):*  
  O algoritmo ainda foi capaz de reduzir significativamente o número de caixas, mas o tempo de execução aumentou.

# Discussões e Análise Crítica
O *Simulated Annealing* provou-se eficiente para o *Bin Packing Problem*, principalmente em instâncias pequenas e médias. No entanto, para instâncias maiores, a convergência é mais lenta. A função de aceitação baseada na temperatura permite uma exploração inicial ampla do espaço de soluções, mas ajustá-la poderia trazer maior eficiência para problemas maiores.
