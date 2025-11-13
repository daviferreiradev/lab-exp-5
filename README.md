# Lab05S01: Desenho e Preparação do Experimento

## Passo 1: Desenho do Experimento

Avaliar quantitativamente se a API GraphQL oferece melhor performance (velocidade) e eficiência de transferência (tamanho de resposta) em comparação com a API REST do GitHub ao buscar métricas e metadados de repositórios que morreram e reviveram, considerando indicadores de qualidade e engajamento.

### Contexto do Experimento

-   **Base de Dados:** 1000 repositórios iniciais do GitHub
-   **Amostra Filtrada:** 185 repositórios que morreram e reviveram (critério: inatividade seguida de reativação)
-   **Objetivo:** Comparar REST vs GraphQL na coleta de:
    -   Data de ressurreição
    -   Presença de documentação
    -   Menções de incentivo/motivação
    -   Adoção de CI/CD após reativação
    -   Comentários médios antes e depois
    -   Diversidade de contribuidores antes e depois
    -   Taxa de fechamento de issues em 30 dias

### A. Hipóteses Nula e Alternativa

#### Para RQ1 (Velocidade): "Respostas às consultas GraphQL são mais rápidas que respostas às consultas REST?"

-   **Hipótese Nula ($H_0$):** O tempo de resposta médio das consultas REST é menor ou igual ao tempo de resposta médio das consultas GraphQL. ($T_{REST} \leq T_{GraphQL}$)
-   **Hipótese Alternativa ($H_A$):** O tempo de resposta médio das consultas GraphQL é menor que o tempo de resposta médio das consultas REST. ($T_{GraphQL} < T_{REST}$)

#### Para RQ2 (Tamanho de Resposta): "Respostas às consultas GraphQL transferem menos dados que respostas às consultas REST?"

-   **Hipótese Nula ($H_0$):** O tamanho médio das respostas REST (payload) é menor ou igual ao tamanho médio das respostas GraphQL. ($S_{REST} \leq S_{GraphQL}$)
-   **Hipótese Alternativa ($H_A$):** O tamanho médio das respostas GraphQL é menor que o tamanho médio das respostas REST. ($S_{GraphQL} < S_{REST}$)

### A. Hipóteses Nula e Alternativa

#### Para RQ1 (Velocidade): "Respostas às consultas GraphQL são mais rápidas que respostas às consultas REST?"

-   **Hipótese Nula ($H_0$):** O tempo de resposta médio das consultas REST é menor ou igual ao tempo de resposta médio das consultas GraphQL. ($T_{REST} \leq T_{GraphQL}$)
-   **Hipótese Alternativa ($H_A$):** O tempo de resposta médio das consultas GraphQL é menor que o tempo de resposta médio das consultas REST. ($T_{GraphQL} < T_{REST}$)

#### Para RQ2 (Tamanho de Resposta): "Respostas às consultas GraphQL transferem menos dados que respostas às consultas REST?"

-   **Hipótese Nula ($H_0$):** O tamanho médio das respostas REST (payload) é menor ou igual ao tamanho médio das respostas GraphQL. ($S_{REST} \leq S_{GraphQL}$)
-   **Hipótese Alternativa ($H_A$):** O tamanho médio das respostas GraphQL é menor que o tamanho médio das respostas REST. ($S_{GraphQL} < S_{REST}$)

### B. Variáveis Dependentes

São as métricas que serão medidas para avaliar as hipóteses:

-   **VD1 (para RQ1):** Tempo de resposta total da consulta (medido em milissegundos).
-   **VD2 (para RQ2):** Tamanho do payload da resposta (medido em kilobytes).

### C. Variáveis Independentes

É a variável que será manipulada para observar o efeito nas variáveis dependentes:

-   **VI1:** O tipo de API utilizado para buscar dados do GitHub (REST vs GraphQL).

### D. Tratamentos

Os tratamentos são as diferentes "versões" da variável independente que serão aplicadas:

-   **T1:** Execução de consultas utilizando a API GraphQL do GitHub para obter métricas de repositórios revividos.
-   **T2:** Execução de consultas utilizando a API REST do GitHub para obter as mesmas métricas.

### E. Objetos Experimentais

Os objetos são os sistemas onde o experimento será conduzido.

**Objeto:** Os 185 repositórios GitHub que apresentaram morte e reativação.

**Dados Coletados:** Para cada repositório, buscar (tanto via REST quanto GraphQL):

-   **Data de Ressurreição:** Data em que o repositório voltou a ficar ativo
-   **Documentação:** Booleano indicando presença de documentação (README, wikis, etc.)
-   **Menções de Incentivo:** Número de menções a motivação/incentivo nos issues/commits
-   **CI Adotado Pós-Revive:** Booleano indicando adoção de CI/CD após reativação
-   **Comentários Médios Antes:** Média de comentários em issues antes da morte
-   **Comentários Médios Depois:** Média de comentários em issues após reativação
-   **Diversidade de Contribuidores Antes:** Índice de diversidade de contribuidores antes da morte
-   **Diversidade de Contribuidores Depois:** Índice de diversidade de contribuidores após reativação
-   **Taxa de Fechamento em 30 Dias:** Percentual de issues fechadas nos 30 dias após reativação

### F. Tipo de Projeto Experimental

-   **Tipo:** Experimento controlado com design pareado (paired design).
-   **Justificativa:** Para cada um dos 185 repositórios, executaremos as mesmas consultas de dados em ambos os tratamentos (T1 via GraphQL e T2 via REST). Isso permite uma comparação direta do desempenho de ambas as APIs ao realizar exatamente a mesma tarefa.

### G. Quantidade de Medições

Para garantir significância estatística e mitigar flutuações momentâneas (ex: picos de latência de rede, rate limiting da API), cada consulta será executada $N$ vezes (ex: $N=5$ repetições) para cada repositório e para cada tratamento (T1 e T2).

**Total de Medições:** 185 repositórios × 2 tratamentos × N repetições = 370N medições

Os resultados (tempo de resposta e tamanho de payload) de cada execução serão registrados.

### H. Ameaças à Validade

#### Ameaça à Validade Interna (Rate Limiting da API)

-   **Problema:** O GitHub impõe limites de taxa (rate limits) nas suas APIs. Requisições em rápida sucessão podem ser throttled, afetando os tempos de resposta.
-   **Mitigação:** Implementar delays entre requisições e usar diferentes tokens de autenticação para aumentar os limites. Registrar quando rate limiting é acionado e excluir essas medições da análise.

#### Ameaça à Validade Interna (Latência de Rede)

-   **Problema:** Variações na latência de rede podem afetar significativamente os tempos de resposta, mascarando diferenças reais entre as APIs.
-   **Mitigação:** Executar o experimento em um ambiente de rede estável. Realizar múltiplas repetições (N) para cada repositório para capturar uma distribuição de tempos.

#### Ameaça à Validade de Construção (Completude das Consultas)

-   **Problema:** Se a consulta REST ou GraphQL não retornar os mesmos dados, a comparação não será válida (ex: GraphQL pode limitar a profundidade de resultados).
-   **Mitigação:** Validar que ambas as APIs retornam os mesmos dados (número de commits, lista de contribuidores). Implementar verificações no script para garantir paridade dos dados.

#### Ameaça à Validade de Construção (Overhead de Serialização)

-   **Problema:** Diferentes formatos de resposta (JSON no REST vs JSON no GraphQL) podem ter tamanhos diferentes mesmo com os mesmos dados.
-   **Mitigação:** Medir o tamanho bruto do payload transmitido (bytes de rede) para ambos, independentemente do formato.

---

## Passo 2: Preparação do Experimento

Esta etapa descreve as ações práticas para montar o cenário experimental.

### Ambiente

-   **Linguagem:** Python 3.9+
-   **Bibliotecas Principais:**
    -   `requests` - para requisições HTTP à API REST do GitHub
    -   `gql` e `gql[requests]` - para requisições GraphQL
    -   `pandas` - para manipulação e análise de dados
    -   `time` - para medição de latência
    -   `psutil` - para monitorar overhead de sistema
-   **Execução:** Localmente em máquina com conexão estável à internet, preferencialmente em períodos de baixo tráfego de rede.

### Dados de Entrada

**Arquivo:** `resurrected_repos.csv` (contém os 185 repositórios identificados como mortos e revividos)

**Colunas do arquivo:**

-   `repo` - Nome completo do repositório (owner/name)
-   `data_ressurreicao` - Data em que o repositório voltou a ter atividade
-   `tem_documentacao` - Booleano (TRUE/FALSE) indicando presença de documentação
-   `mencoes_incentivo` - Número inteiro de menções a incentivo nos issues e commits
-   `ci_adotado_pos_revive` - Booleano (TRUE/FALSE) indicando adoção de CI/CD após reativação
-   `comentarios_medios_antes` - Média de comentários em issues antes da morte
-   `comentarios_medios_depois` - Média de comentários em issues após reativação
-   `diversidade_contrib_antes` - Índice de diversidade (0 a 1) antes da morte
-   `diversidade_contrib_depois` - Índice de diversidade (0 a 1) após reativação
-   `taxa_fechamento_30d` - Percentual de issues fechadas (0 a 1) nos 30 dias após reativação

### Definição das Consultas (Tratamentos)

Ambos os tratamentos (T1 e T2) buscarão as mesmas informações para cada repositório de forma a garantir uma comparação justa.

#### Dados a Buscar:

1. **Data de Ressurreição:** Data em que o repositório voltou a ter commits
2. **Presença de Documentação:** Verificar existência de README, wikis ou páginas de documentação
3. **Menções de Incentivo:** Contar menções a palavras-chave como "revived", "resurrection", "motivation", "incentive" em issues, PRs e commits
4. **Adoção de CI/CD:** Verificar existência de workflows (GitHub Actions, Travis CI, etc.) criados após a data de reativação
5. **Comentários Médios:** Calcular a média aritmética de comentários em issues
6. **Diversidade de Contribuidores:** Usar índice de Herfindahl para medir concentração de contribuições
7. **Taxa de Fechamento de Issues:** Percentual de issues criadas nos 30 dias pós-reativação que foram fechadas

#### T1 - Consulta GraphQL

Requisição única ao GraphQL do GitHub para coletar todas as métricas simultaneamente.

#### T2 - Consultas REST

Série de chamadas REST ao GitHub para coletar as mesmas métricas (múltiplas endpoints necessárias).

### Scripts de Medição

O script executa o seguinte fluxo:

1. **Carrega** a lista de 185 repositórios do arquivo `resurrected_repos.csv`
2. **Para cada repositório:**
    - Executa N vezes (N=5) a consulta T1 (GraphQL), medindo:
        - Tempo total de resposta (ms)
        - Tamanho do payload (KB)
        - Validação dos dados retornados
    - Executa N vezes a consulta T2 (REST), medindo as mesmas métricas
    - Valida que ambas retornam os mesmos dados
3. **Armazena** todos os resultados em um arquivo CSV estruturado
4. **Computa** estatísticas descritivas (média, desvio padrão, mediana) para cada API

#### Métricas Coletadas por Execução:

-   `repo` - Identificador do repositório
-   `api` - Tipo de API utilizada (GraphQL ou REST)
-   `iteration` - Número da repetição (1 a N)
-   `response_time_ms` - Tempo de resposta em milissegundos
-   `payload_size_kb` - Tamanho bruto do payload em kilobytes
-   `data_ressurreicao` - Data retornada
-   `tem_documentacao` - Booleano retornado
-   `mencoes_incentivo` - Número retornado
-   `ci_adotado_pos_revive` - Booleano retornado
-   `comentarios_medios_antes` - Valor retornado
-   `comentarios_medios_depois` - Valor retornado
-   `diversidade_contrib_antes` - Valor retornado
-   `diversidade_contrib_depois` - Valor retornado
-   `taxa_fechamento_30d` - Valor retornado

#### Arquivos de Saída:

**`experimento_resultados.csv`** - Dados brutos de todas as medições

**`experimento_analise.csv`** - Agregação estatística dos resultados por API (média, desvio padrão, mediana)
