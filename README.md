# Impacto da Densidade de Dados na Convergência de Modelos Preditivos

> **Nota para a equipe/professor:** Este README documenta o racional investigativo passo a passo do projeto. Ele serve como guia para a reprodução dos testes e para a compreensão das hipóteses levantadas durante a exploração dos dados.

## Objetivo do Projeto
Este projeto acadêmico utiliza a investigação técnica para provar que a falha em algoritmos preditivos (como baixíssima acurácia e erros de convergência) muitas vezes decorre da incompatibilidade entre a base de dados (escassez/densidade estatística) e a arquitetura escolhida, em vez de erros de código ou de configuração.

## Bases de Dados (Dicionários e Referências)
Para compreender a fundo a natureza das classes, categorias e o dicionário de variáveis de cada base, seguem os links das fontes originais utilizadas neste estudo:

1. **Dataset 1 - Desempenho e Notas de Alunos:** [Inserir Link do Kaggle/UCI aqui]
   * *Característica:* 116 instâncias, 30 atributos numéricos/categóricos e 8 classes alvo (notas). 
   * *Papel no projeto:* Utilizado como ambiente de estresse para demonstrar o colapso dos algoritmos frente ao ruído estatístico e à escassez de dados.
2. **Dataset 2 - Evasão e Sucesso Acadêmico:** [Inserir Link do UCI Predict Students' Dropout and Academic Success aqui]
   * *Característica:* 4.424 instâncias e 3 classes alvo (Dropout, Enrolled, Graduate).
   * *Papel no projeto:* Utilizado como contraponto para validar a estabilização matemática gerada pelo volume amostral.

## Estrutura do Repositório
```text
IAtrabalho/
├── dados/
│   ├── raw/             # Bases de dados originais (.csv)
├── notebooks/
│   ├── exploracao_pt1.ipynb  # Investigação de falhas e levantamento de hipóteses (Dataset 1)
│   └── exploracao_pt2.ipynb  # Validação de volume e escalabilidade preditiva (Dataset 2)
├── .venv/               # Ambiente virtual
└── README.md            # Documentação do projeto
```


## O Racional Investigativo e Principais Conclusões
O desenvolvimento deste trabalho não seguiu uma aplicação direta de algoritmos, mas sim um método científico de levantamento e refutação de hipóteses. A construção do pensamento no exploracao_pt1 moldou as seguintes conclusões:

1. A Ilusão da Escala (StandardScaler): A primeira execução do Multi-Layer Perceptron (MLP) gerou 17,24% de acurácia e um aviso de falha de convergência. A primeira hipótese atribuía o erro à heterogeneidade dos dados (atributos com valores absolutos muito distintos esmagando o cálculo do gradiente). A aplicação do StandardScaler equalizou as variáveis, mas não resolveu a acurácia (20,69%), forçando-nos a buscar causas mais profundas.  


2. A Incompatibilidade do Otimizador: A segunda hipótese focou no motor do algoritmo. Descobrimos que o otimizador padrão (adam) é instável para bases minúsculas. A troca para o lbfgs (método analítico) resolveu o erro de convergência matemática, mas a matriz de confusão revelou que o modelo passou a "chutar" uma única classe dominante para minimizar a perda.  


3. Fome de Dados e o Colapso Linear: Constatamos que os 30 atributos exigiam da rede neural uma calibração de centenas de pesos, algo impossível com apenas 116 alunos (Data Hunger). Tentamos reduzir a complexidade testando um Single-Layer Perceptron (SLP), mas a acurácia caiu para 13,79%. Concluímos que a base não apenas era pequena, mas de natureza multivariável e não-linear, o que causou o underfitting (subajuste) imediato do SLP.  


4. A Prova Definitiva (Dataset 2): Algoritmos lógicos que mitigam superajuste (Decision Tree e Random Forest) também falharam no primeiro dataset (abaixo de 18%). Contudo, ao migrarmos a exata mesma metodologia para o Dataset 2 (4.424 linhas), a Árvore de Decisão alcançou 69,49%, o MLP 72,66% e o Random Forest estabeleceu o teto preditivo de 76,38%. Isso provou definitivamente que a complexidade do algoritmo é inútil sem a densidade estatística para sustentá-la.  


5. O Desafio do Estado Transitório: As matrizes de confusão da Fase 2 revelaram que os modelos mapeiam com excelência os desfechos consolidados (Formados e Evadidos), mas apresentam alta dispersão na classe intermediária (Alunos Matriculados). Essa sobreposição geométrica indica que quebrar a barreira dos 90% de acurácia exigirá engenharia de atributos e tratamentos qualitativos (como One-Hot Encoding e SMOTE). 


## Ferramentas e Tecnologias Utilizadas
Este projeto foi desenvolvido utilizando o ecossistema de Data Science do Python, com apoio das seguintes ferramentas e bibliotecas:

* **Linguagem:** Python 3.x
* **Ambiente de Desenvolvimento:** Visual Studio Code (VSCode) com suporte a Jupyter Notebooks.
* **Manipulação de Dados:** `pandas` (leitura, estruturação e inspeção dos datasets).
* **Machine Learning:** `scikit-learn` 
  * *Pré-processamento:* `StandardScaler`, `LabelEncoder`, `train_test_split`.
  * *Modelos:* `MLPClassifier`, `DecisionTreeClassifier`, `RandomForestClassifier`.
  * *Métricas:* `accuracy_score`, `classification_report`, `confusion_matrix`.
* **Visualização de Dados:** `matplotlib` (plotagem e análise gráfica das matrizes de confusão).
