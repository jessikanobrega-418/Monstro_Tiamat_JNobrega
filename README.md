# Previsão do Gap HOMO-LUMO de Moléculas Orgânicas Utilizando Aprendizado de Máquina

## Contexto do Projeto
Este repositório contém a documentação e a implementação do projeto final desenvolvido por **Jéssika Ferreira da Nóbrega**, estudante da **Ilum Escola de Ciência**, representante da Casa da Lâmina da Alvorada e integrante da Guilda dos Cavaleiros. O trabalho foi realizado para o Monstro Tiamat da disciplina de Aprendizado de Máquina, integrando a fundamentação em química quântica com a construção de modelos preditivos supervisionados e pipelines de ciência de dados.


## Visão Geral e Objetivos do Trabalho
A determinação da estrutura eletrônica de moléculas orgânicas por métodos tradicionais de química quântica exige alto custo computacional. O objetivo principal desta pesquisa é avaliar a capacidade de algoritmos de aprendizado de máquina em prever o gap HOMO-LUMO, definido matematicamente pela diferença entre a energia do orbital molecular ocupado de maior energia e a energia do orbital desocupado de menor energia, a partir de um conjunto de 133.885 moléculas do repositório QM9. A investigação busca estabelecer uma alternativa eficiente aos cálculos teóricos pesados, mantendo um rigoroso controle metodológico para garantir a generalização do modelo em dados inéditos.


## Estrutura do Repositório
A organização dos arquivos no repositório foi projetada de forma transparente para guiar o leitor sobre a navegação e a execução do trabalho. O arquivo `Monstro Tiamat - Jéssika Nóbrega.ipynb` concentra todo o pipeline de código executável, abrangendo desde a análise exploratória e pré-processamento até o treinamento, otimização e avaliação dos modelos. O arquivo `tiamat_optuna_pca.db` consiste no banco de dados em formato SQLite responsável por armazenar o histórico detalhado de todas as tentativas de busca e otimização de hiperparâmetros conduzidas via Optuna. A base de dados local `qm9.csv` armazena o dataset tabular, sendo que o próprio notebook realiza o download automático a partir do repositório remoto `yairschiff/qm9` na Hugging Face Datasets caso o arquivo local não esteja presente. O arquivo `Brasão Tiamat.png` contém o desenho do brasão da Casa da Lâmina da Alvorada desenhado como pontuação complementar. 


## Metodologia e Prevenção de Data Leakage
A escolha dos descritores moleculares seguiu uma rigorosa política de prevenção contra o vazamento de dados (data leakage). Para evitar predições circulares ou artificialmente inflacionadas, as variáveis de energia dos orbitais HOMO е LUMO foram inteiramente excluídas do conjunto de preditores, assim como as propriedades termoquímicas calculadas previamente. Ao final da seleção, restaram 24 descritores topológicos, geométricos e físico-químicos, tais como o número de átomos, constantes rotacionais, momento de dipolo, polarizabilidade, raio quadrático médio, energia vibracional de ponto zero e contagens de anéis e ligações químicas. Os dados foram divididos em 80% para treinamento e 20% para um conjunto de teste mantido estritamente isolado. Todas as etapas de imputação de valores nulos e padronização com StandardScaler foram inseridas diretamente dentro de objetos Pipeline do scikit-learn, garantindo que o ajuste das transformações ocorresse exclusivamente dentro dos subconjuntos de treino em cada fold da validação cruzada.


## Modelagem e Otimização de Hiperparâmetros
O trabalho comparou o desempenho de seis algoritmos de regressão em relação a um modelo baseline trivial que prevê unicamente a média da variável-alvo. Foram avaliados a Regressão Ridge como referência linear regularizada, o k-Nearest Neighbors (k-NN) baseado em proximidade geométrica, e quatro métodos baseados em conjuntos de árvores de decisão: Random Forest, Extra Trees, HistGradientBoosting e Gradient Boosting. Para cada um dos algoritmos, foi realizada uma busca de hiperparâmetros por Otimização Bayesiana utilizando o Optuna, totalizando 100 tentativas por modelo sob validação cruzada de três folds. O espaço de busca também permitiu ao algoritmo escolher de forma autônoma a inclusão ou exclusão de redução de dimensionalidade por Análise de Componentes Principais (PCA). Todos os modelos de árvore e o k-NN selecionaram pipelines sem a aplicação de PCA, uma vez que a mistura linear de atributos promovida pela PCA prejudicava as divisões ortogonais diretas dos algoritmos em variáveis discretas.


## Explicabilidade e Importância por Permutação
Para compreender quais características físicas mais influenciam o comportamento dos orbitais moleculares, foi realizada uma análise de importância por permutação no conjunto de teste utilizando o modelo campeão. Os descritores de maior impacto preditivo foram a contagem de ligações duplas (double_bond), a contagem de ligações aromáticas (aromatic_bond), a contagem de ligações triplas (triple_bond), a lipofilicidade (logP) e a contagem de ligações simples (single_bond). Sob a ótica físico-química, a dominância das insaturações e das ligações conjugadas evidencia que a densidade e a deslocalização dos elétrons pi são os fatores chave na determinação do gap de energia entre o HOMO e o LUMO em moléculas orgânicas pequenas.


## Resultados
A avaliação final no conjunto de teste independente confirmou a superioridade dos métodos **não-lineares** de árvore sobre as abordagens lineares e sobre a referência inicial. O modelo campeão foi o **Extra Trees Regressor**, que atingiu um Erro Absoluto Médio (MAE) de 0,009011 Hartree, equivalente a 0,2452 eV, uma Raiz do Erro Quadrático Médio (RMSE) de 0,012591 Hartree (0,3426 eV) e um Coeficiente de Determinação R² de 0,929381. Esse resultado representa uma redução de 77,18% no erro absoluto em comparação com o modelo baseline da média, que havia registrado um MAE de 0,039492 Hartree (1,0746 eV). Em termos comparativos, o Random Forest alcançou MAE de 0,009512 Hartree (0,2588 eV) e R² de 0,922940; ok-NN obteve MAE de 0,010492 Hartree (0,2855 eV) e R² de 0,897704; ο HistGradientBoosting obteve MAE de 0,010503 Hartree (0,2858 eV) e R² de 0,913123; ο Gradient Boosting obteve MAE de 0,012764 Hartree (0,3473 eV) e R² de 0,877311; e a Regressão Ridge registrou MAE de 0,018647 Hartree (0,5074 eV) e R² de 0,748791.



## Conclusões e Principais Aprendizados
A realização deste trabalho demonstrou que o aprendizado de máquina não-linear é plenamente capaz de mapear propriedades quânticas complexas com alta precisão e baixo tempo de execução. O isolamento estrito dos dados e a estruturação via pipelines provaram ser essenciais para evitar qualquer contaminação entre os conjuntos de treino e teste. O estudo confirmou que os conjuntos de árvores superam significativamente os modelos lineares regularizados na captura de interações de alta ordem do dataset QM9. Por fim, identificou-se que a preservação dos descritores físicos originais sem a rotação da PCA é benéfica para a decisão ortogonal das árvores, mantendo a interpretabilidade química e a capacidade explicativa do modelo.


## Descrição do Uso de Inteligência Artificial
Foi utilizada Inteligência Artificial para auxiliar a busca por um conjunto de dados de interesse, para debater quais algoritmos seriam mais interessantes nesta aplicação, para auxiliar a formatar os gráficos, para definir a licença no GitHub e para ajudar a alterar algumas coisas na carta de apresentação ao Rei Milu Iluminarius.


## Instruções para Reprodução do Projeto
Para reproduzir os experimentos e executar o pipeline completo de código, o primeiro passo consiste em clonar este repositório do GitHub e instalar as bibliotecas requeridas através do comando pip install datasets pandas numpy matplotlib seaborn scikit-learn scipy optuna pyarrow. Em seguida, basta iniciar o ambiente do Jupyter Notebook e executar sequencialmente as células do arquivo `Monstro Tiamat - Jéssika Nóbrega.ipynb`. Todas as operações estocásticas possuem a semente aleatória fixada no valor 73 para assegurar a reprodutibilidade dos resultados, e a conversão de unidades utiliza a constante de 1 Hartree para 27,2114 eV.


## Licença do Projeto
Este projeto é distribuído sob os termos da `Licença MIT`, que é uma licença de software livre permissiva bastante utilizada na comunidade acadêmica e de código aberto. Sob as condições dessa licença, qualquer pessoa recebe permissão gratuita para utilizar, copiar, modificar, mesclar, publicar, distribuir, sublicenciar ou vender cópias do software, seja para fins comerciais ou não comerciais. A única exigência obrigatória para o uso ou redistribuição é a manutenção do aviso original de **direitos autorais e do texto desta licença em todas as cópias ou partes substanciais do código**. O software é fornecido exatamente no estado em que se encontra, sem garantias explícitas ou implícitas de qualquer tipo por parte da autora.
