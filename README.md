# Algoritmos Genéticos para a Escolha de uma Mistura Binária de Combustíveis

Olá, esse é o repositório do trabalho final da Disciplina de Algoritmos Genéticos e Redes Neurais, ministrada pelo professor Daniel Cassar na Ilum Escola de Ciência durante o terceiro semestre do curso de Bacharelado em Ciência e Tecnologia, ao longo do primeiro semestre de 2023. Nesse sentido, o trabalho por Isadora Marcondes, Joâo Caramês e Raphaella Siqueira.

## Autores

- [Isadora Marcondes](https://github.com/isadoramarcondes)
- Email: isadora220051@ilum.cnpem.br

- [João Caramês](https://github.com/JoaoCarames)
- Email: joao220064@ilum.cnpem.br

- [Raphaella Siqueira](https://github.com/raphaella220046)
- Email: raphaella220046@ilum.cnpem.br

## Contextualização

No âmbito da disciplina de Termodinâmica Avançada, ministrada pelo professor Amauri Jardim de Paula, foi realizado ao longo do semestre, concomitantemente à essa disciplina, um projeto. Esse projeto está ilustrado na imagem abaixo:

<p align="center">
  <img src="https://github.com/isadoramarcondes/Trabalho_Redes/blob/main/Figuras%20Readme/ProjetoTermodinamica.png">
</p>

Nele, tivemos a missão de escolher uma mistura binária, formada então por dois compostos, para passar pelas etapas ilustradas, das quais destacamos dois processos, a separação de mistura (destilação fracionada na maioria dos casos) e o ciclo de rankine. Nesse sentido, usaremos esses dois processos para orientar a buscar pela melhor mistura binária.

Destilação: vários fatores podem influenciar a facilidade do processo, em especial a diferença de temperatura de ebulição dos compostos misturados. Assim, quão maior for essa diferença, mais fácil é separar os compostos.

Ciclo de Rankine: o ciclo de rankine é dividido nas seguintes quatro etapas;

1. Boiler (Aquecimento Isobárico):
Primeiro, acontece o processo de aquecimento isobaricamente, ou seja, a pressão se mantém constante durante esse processo. Nesse estágio, a água, é aquecida a alta pressão em uma caldeira, na qual o aquecimento ocorre até que a água atinja a temperatura de ebulição e se transforme em vapor. É aqui que entra o nosso combustível, o qual será usado para aquecer a água.

2. Turbina (Expansão Isentrópica):
Após o aquecimento na caldeira, o vapor de alta pressão e temperatura vai à turbina, onde passa por uma expansão isentrópica, ou seja, uma expansão adiabática reversível, na qual a entropia do sistema não sofre variação. Ao longo da expansão, o vapor libera energia cinética e gera trabalho. Assim, temos que a energia térmica do vapor é convertida em energia mecânica no eixo da turbina, a qual está conectado a um gerador para produção de eletricidade.

3. Condensador (Resfriamento Isobárico):
Depois de sair da turbina, o vapor agora de baixa pressão é vai ao condensador, no qual é resfriado isobaricamente e condensado de volta para a forma líquida. Nesse sentido, o calor é transferido do vapor para uma tubulação contendo água fria (de um rio ou do mar, geralmente).

4. Bomba (Compressão Isentrópica):
Por fim, a água vai para a bomba, a qual a comprime isentropicamente, aumentando a pressão sobre a água e mandando ela para a caldeira.

Assim, na etapa em que a substância de trabalho (água) está no boiler, temos que usar de um combustível para aquecê-la. Dessa forma, um composto com alta entalpia de combustão é desejado.

## Objetivos

### Objetivo Principal

> Usar o algoritmo genético para realizar a escolha da melhor mistura binária possível.

### Objetivos Secundários

> Trabalhar diferentes dados termodinâmicos e otimizá-los para atender o problema.
> Mostrar que é possível utilizar o algoritmo genético sobre dados termodinâmicos para realizar uma escolha.
> Trabalhar uma implementação de avaliação por meio de critérios envolvidos na escolha baseados no problema apresentado.

## Materiais e Métodos

Para realizar nosso objetivo, usamos do algoritmo genético e de uma base de dados termodinâmicos. Sendo assim, há nesse repositório um notebook para cada um.

### Organizando Dados Termodinâmicos

Nesse notebook, trabalhamos a construção de uma tabela organizada de possíveis dados termodinâmicos a serem utilizados ao longo do desenvolvimento do algoritmo genéticos e isso foi feito dada a dificuldade em encontrar de maneira simples e acessível esses dados dessa forma online. 

Nesse contexto, utilizamos de início os dados presentes na tabela qm9, os quais foram obtidos da Quantum Machine 9 (QM9), a qual disponibiliza dados obtidos por cálculos de DFT para mais de 100000 (cem mil) moléculas pequenas, as quais possuem até 9 átomos diferentes do hidrogênio na sua composição (Carbono, Oxigênio, Nitrogênio e Fósforo, no caso). Nessa tabela, encontramos dados preciosos para esse trabalho, como o código SMILES (identificador de moléculas), a entalpia de atomização, a entropia e a energia livre de Gibbs padrões. Todavia, esses dados estão quantificados na unidade Hartree (própria para calculos de DFT) e foram convertidos para as unidades usuais (baseadas na entalpia de formação) usando os dados de referência disponibilizados por eles.

Ademais, usamos os dados convertidos para estimar a entalpia de combustão de cada molécula presente no dataset, mas antes de entrarmos nessa parte, vale ressaltar os possíveis erros de aproximação obtidos pela conversão citada. Desse modo, a conversão pode gerar discrepâncias dada a natureza das medições, já que a entalpia de atomização faz referência a energia necessária para separar uma molécula em núcleos e elétrons e a entalpia de formação para separar em elementos. Segue um exemplo da discrepância dos valores para o etanol:

$$C_2H_6O \xrightarrow{} \Delta H_{atomization} = -97243.5744747 \text{ kcal/mol} \quad C_2H_6O \xrightarrow{} \Delta H_{formation} = -66.39579 \text{ kcal/mol}$$

Assim, mesmo utilizando as referências para a correção, esse tipo de diferença pode gerar imprecisão, como no caso do etanol, cujo valor obtido foi:

$$C_2H_6O \xrightarrow{} \Delta H_{formation} = 771.180433 \text{kcal/mol}$$

Vale notar que o valor é positivo por mera convenção e isso não afeta os cálculos detalhados no notebook. Então, o motivo de escolha desses dados foi a alta quantidade de moléculas, o que nos possibilita uma alta diversidade de dados e entendemos que isso é o mais importante para nossos objetivos, sendo então indicada uma avaliação do usuário ao final do processo.

Após isso, é realizada a reação de combustão e a estimativa da entalpia de combustão de cada molécula por meio de ferramentas de string e dicionários. Com isso, temos ao final os seguintes dados em nossa tabela:

<table ><tr><th>Fórmula<th><th>N° de Carbonos<th><th>N° de Hidrogênios<th><th>N° de Oxigênios<th><th>N° de Nitrogênios<th><th>N° de Fósforos<th><th>Isomeric Smiles<th><th>Smiles<th><th>Massa Molar (g/mol)<th><th>Entalpia de Combustão (kcal/mol)<th><th>Entalpia de Formação (kcal/mol)<th><th>Energia Interna (kcal)<th><th>Energia Livre de Gibbs (kcal/mol)<tr><tr>
<tr><td> C2H6O <td><td> 2 <td><td> 6 <td><td> 1 <td><td> 0 <td><td> 0 <td><td> CCO <td><td> CCO <td><td> 46.069 <td><td> 294.320086 <td><td> 771.180433 <td><td> 766.438343 <td><td> 709.349402 <td><tr> <table>

### Algoritmo Genético

O algoritmo genético presente no arquivo 'alg_gen.ipynb' foi usado para encontrar a melhor mistura binária no contexto do nosso problema e como outros algoritmos genéticos, é dividido em algumas partes, as quais estão detalhadas a seguir:

1. Importações: na primeira parte do notebook, trazemos todas bibliotecas utilizadas e os dados obtidos na seção anterior, dos quais são selecionadas algumas informações de interesse e então é feita uma normalização dos dados usados como critério mais tarde.

2. Funções: nessa segunda parte, são montadas as engrenagens do algoritmo, sendo elas:

    - Gene; um combustível aleatório da tabela.
    - Indivíduos; uma mistura binária, ou seja, uma lista de dois combustíveis.
    - População; um posto, ou seja, um conjunto de indivíduos/combustíveis diferentes do qual queremos o melhor.
    - Funçao Objetivo; calcula o fitness baseado no Método de Avaliação por pesos/critérios.
    - Seleção; usa o Método da Roleta para maximizar o fitness.
    - Cruzamento; usa o cruzamento simples para trocar combustíveis de indivíduo.
    - Mutação; troca um combustível por outro aleatório.

3. Funções Locais e Constantes: nessa terceira parte são definidas as funções locais necessárias para pouco mudar o algoritmo genético e os parâmetros constantes a serem utilizados no algoritmo.

4. Algoritmo Genéticos: o algoritmo genético clássico produzido ao longo do semestre em sala de aula com a ajuda do professor Daniel Cassar e de outros colegas de classe de maneira didática com a mudança no hall da fama para analisar melhor os resultado na última parte.

5. Análise de Resultado: na última parte do notebook, buscamos visualizar os membros do hall da fama e verificar se os valores obtidos são válidos para a solução do problema.

Com isso, foi possível obter diversos resultados baseados no desenvolvimento do algoritmo, o qual chegou a esse estado após uma progressão que será melhor detalhada na seção de resultados. No mais, vale salientar o que seria o Método de Avaliação.

Assim, o Método de Avaliação é uma abordagem que fizemos para com o fitness na função objetivo, onde invés de tentar maximizar ou minimizar diretamente as grandezas de interesse, nós as utilizamos como critério em uma avaliação. Dessa maneira, o fitness é a soma da avaliações, as quais são a combinação linear das notas obtidas pelos compostos, as quais são o produto de um peso por um critério. Por exemplo, podemos calcular o fitness de um combustível da seguinte maneira:

$$fitness = 10 \cdot \Delta H_c - 3 \cdot MM$$

Onde as constantes são os pesos atribuidos baseados no objetivo e as grandezas são a entalpia de combustão e a massa molar, respectivamente.

## Resultados e Discussão

Como dito anteriormente, ao longo do desenvolvimento desse trabalho, outras abordagens foram utilizadas, não com objetivo em si, mas como parte do objetivo final. Assim, mencionaremos os resultados obtidos antes e como eles ajudaram na criação do notebook final.

### Escolhendo um bom combustível

A primeira versão do nosso algoritmo nos entregava uma sequência de combustíveis, onde o primeiro melhor combustível era selecionado e depois substituido caso outra geração possuise um melhor. Essa abordagem foi mantida ao longo do desenvolvimento e melhorada com a atribuição desse hall da fama à uma lista mais tarde. Além disso, é importante ressaltar que o indíviduo era apenas um combustível, portanto, igual ao gene e não fora usado o cruzamento por não haver o que cruzar, apenas seleção e mutação. Com isso, foram obtidos os seguintes resultados:

<p align="center">
  <img src="https://github.com/isadoramarcondes/Trabalho_Redes/blob/main/Figuras%20Readme/Resultado01.png">
</p>

Como podemos observar, no primeiro uso do algoritmo, o último combustível obtido, portanto o melhor, tinha a mesma massa molar que o anterior e um valor de entalpia ligeiramente superior. Esse fato mostra que o algoritmo genético é capaz de passar pelos dados termodinâmicos e então obter uma resposta razoável para nosso problema, usando a abordagem explicada para a função objetivo.

### Escolhendo dois bons combustíveis

Já a segunda versão do algoritmos nos entregava uma sequência de indivíduos, os quais eram formados por dois combustíveis, ou seja, eram misturas binárisa. Além disso, eles foram avaliados sobre os mesmos critérios, porém a necessidade de avaliar dois genes fez necessária a realização de uma combinação linear para termos um valor único de fitness e introduzimos o cruzamento, o qual trocava os combustíveis de duas misturas, o que aumentou ainda mais a nossa variabilidade. Assim, obtivemos esses resultados:

<p align="center">
  <img src="https://github.com/isadoramarcondes/Trabalho_Redes/blob/main/Figuras%20Readme/Resultado02.png">
</p>

Aqui podemos ver que foi possível obter duas boas opções de combustível ao mesmo tempo, ou seja, conseguimos aplicar a avaliação para maximizar as características desejadas em ambos os genes da mistura, além de manter o comportamento anterior de obter uma melhora ao longo das gerações. Nesse sentido, vale destacar que os fatores de aleatoriedade não garantem uma melhora progressiva sempre e esse comportamento não é garantido, somente é maximizado com a otimização dos critérios, dos pesos e da construção geral do algoritmos.

### Escolhendo uma mistura binária

Com os feitos da segunda versão, a terceira pôde finalmente buscar uma mistura binária ideal. Nesse contexto, mesmo que na situação anterior tivéssemos a estrutura de uma mistura sendo usada, não havia avaliação baseada nisso. Portanto, nessa versão, propusemos avaliações diferentes para nossos compostos, onde um deveria ter sua eficiência energética maximizada e o outro uma diferença de massa molar considerável para o primeiro pois sabemos que uma maior massa molar pode significar um maior ponto de ebulição, onde esses valores foram normalizados para facilitar a atribuição de pesos. Vale notar que essa abordagem foi adotada vista a falta de dados sobre o ponto de ebulição, volatilidade ou possíveis relações entre os potenciais termodinâmicos presentes em nossa tabela com essas grandezas. Assim, gostaríamos de ressaltar a necessidade de melhor explorar as ligações entre essas grandezas e os potenciais. Com isso, obtivemos os seguintes resultados:

<p align="center">
  <img src="https://github.com/isadoramarcondes/Trabalho_Redes/blob/main/Figuras%20Readme/Resultado03.png">
</p>

Dessa vez, é factível a escolha de uma boa mistura binária usando os algoritmos genéticos, visto que conseguimos encontrar um par de compostos que atenderam aos critérios comentados e, portanto, possuem valores de eficiência e massa desejáveis. Além disso, é possível visualizar a estrutura dos compostos e seus nomes com o uso das bibliotecas rdkit e pubchempy, as quais podem ser usadas ao fim do algoritmo para uma melhor avaliação dos resultados. Dessa maneira, os compostos encontrados ao fim das gerações foram esses:

<p align="center">
  <img src="https://github.com/isadoramarcondes/Trabalho_Redes/blob/main/Figuras%20Readme/Resultado04.png">
</p>

## Conclusão

À luz dos argumentos supracitados, os objetivos foram alcançados, uma vez que foi possível escolher uma mistura binária razoável com os dados obtidos após o tratamento, os quais forma otimizados com uma normalização para melhorar e facilitar o uso do Método de Avaliação e tudo isso foi feito para fazer escolhas diferentes em momentos diferentes. Sendo assim, todos os objetivos apresentados foram alcançados e abrem as portas para diferentes formas de visualização de dados termodinâmicos massívos, já que a possibilidade de uso de critérios mais diversos e precisos do que os usados no presente trabalho permitem uma escolha mais refinada pelo algoritmo e, portanto, respostas mais completas para perguntas mais complexas.