# Solução Avaliação – Cientista de Dados I

### Candidato: Oscar J. O. Ayala

## 1.1	Uma estrutura de diretórios e nomenclatura de arquivos que permita armazenar as consultas de modo coerente:

Uma estrutura de diretórios e nomenclatura de arquivos que permita armazenar as consultas de modo coerente:

`comercio_exterior/ano/sigla_pais_origem/sigla_pais_destino.txt`

Onde:

- `comercio_exterior`: refere-se aos dois principais componentes importação e exportação.
- `ano`: devido que a referência temporal de interesse é anual.
- `sigla_pais_origem`: refere-se à sigla padrão do país de origem de interesse, exemplo: BRA (Brasil), ARG (Argentina) e assim por diante.
- `sigla_pais_destino.txt`: refere-se aos dados de importação e exportação do país de origem ao país de destino. A sigla do país de destino também obedece às siglas padrões.

  ## 1.2	Liste as etapas necessárias para integração de 3 anos (2019~2021) de comércio exterior.

  Segui o pseudocódigo solicitado em sintaxes *Python*:

1. Lista de anos: criar uma lista nomeada `lista_anos` que armazene os 3 anos (2019-2021) em estrutura de dados inteiros.
2. Lista de países: criar uma lista nomeada `lista_paises` com entradas string que contenha o nome dos países.
3. Loops aninhados: definir um primeiro loop `for` com um iterador `ano` que avance por toda a lista de marco temporal anos. Depois, um loop interno `for` com o iterador `pais_origem` que avance a lista de países. Logo outro loop interno `for` com o iterador `pais_destino` que avance a lista de países.
4. Condição de consulta válida: para cada combinação (`pais_origem`, `pais_destino`, `ano`), apenas seguir com aquelas onde o país de origem seja diferente ao de destino e onde o país de origem não seja “WLD” e de destino “BRA”.
5. Consultar API: obter dados desde a API seguindo a estrutura (`pais_origem`, `pais_destino`, `ano`) com a função `get_data_from_api`.
6. Criar diretório e salvar: criar diretório se não existir e salvar o arquivo correspondente.

Segui o código em sintaxes Python: 

```python
# dados iniciais
lista_anos = [2019, 2020, 2021]
lista_paises = ["BRA", "ARG", "AFG", "WLD", ...]  # um total de 143 países

# loops aninhados
for ano in lista_anos:  # iterador por ano
    for pais_origem in lista_paises:  # iterador por país origem
        for pais_destino in lista_paises:  # iterador por país destino
            # avaliar consulta é válida
            if (pais_origem != pais_destino) and not (pais_origem == "WLD" and pais_destino == "BRA" and ano == 2019):
                # consulta à API
                res = get_data_from_api(pais_origem, pais_destino, ano)
                # Criar diretório se não existir
                os.makedirs(f'comercio_exterior/{ano}/{pais_origem}', exist_ok=True)
                # Salvar os dados no arquivo correspondente
                with open(f'comercio_exterior/{ano}/{pais_origem}/{pais_destino}.txt', 'w') as f:
                    f.write(res)
            else:
                print("Verificar país de origem, destino e ano")
```
## 1.3	Qual/Quais arquivo(s) da sua base de dados respondem a questão: 'Qual a quantidade de soja o Mundo importou do Brasil em 2020?'

Qual arquivo da sua base de dados responde à questão: "Qual a quantidade de soja que o Mundo importou do Brasil em 2020?"

`comercio_exterior/2020/WLD/BRA.txt`

Devido que a consulta inválida de país de origem WLD e destino BRA apenas para o ano de 2019.

## 1.4 Passos para Manter as Bases Atualizadas Recorrentemente

1. Lista de anos: criar uma lista nomeada `lista_anos` que armazene o ano atual.
2. Lista de países: criar uma lista nomeada `lista_paises` com entradas string que contenha o nome dos países.
3. Registro de reporte final: salvar um registro das datas em que cada país reporta os dados anuais completos.
4. Consultar dados mensais até o reporte final (mês - ano): efetuar loops aninhados de anos, país de origem, país de destino, realizar consultas mensais para obter os dados disponíveis até o momento.
5. Realizar consultas anuais após o reporte final: após a data de reporte final de cada país, realizar a consulta anual para obter os dados completos.
6. Atualizar a base de dados: definir uma rotina para realizar as consultas e atualizações, por exemplo, semanalmente ou mensalmente.
7. Manter a estrutura de diretórios: garantir que a estrutura de diretórios e arquivos está organizada conforme definido.

Definiria funções para os seguintes grupos de passos: 1-2, 3, 4-5, 6 e 7.

## 2. Automatizar o processo de baixar, empilhar e manter atualizado os arquivos de empresas (0~9).

### Baixar os Arquivos de Empresas

- Acessar o site e verificar a disponibilidade dos arquivos.
- Utilizar uma ferramenta como `wget` ou `requests` para baixar os arquivos de empresas (0-9).
- Implementar um mecanismo de repetição com limite de tentativas para lidar com possíveis falhas de download.

### Verificar a Integridade dos Arquivos Baixados

- Calcular checksums (como MD5 ou SHA-256) dos arquivos baixados e comparar com checksums esperados, se disponíveis.
- Rebaixar qualquer arquivo que não passe na verificação de integridade.

### Extrair os Arquivos de Empresas

- Utilizar uma ferramenta como `unzip` ou a biblioteca `zipfile` em Python para extrair os arquivos baixados.
- Verificar a integridade dos arquivos extraídos para garantir que todos os dados necessários estejam presentes.

### Validar os Dados Extraídos

- Carregar os arquivos extraídos em um DataFrame temporário usando `pandas`.
- Verificar a consistência e integridade dos dados checando colunas obrigatórias, tipos de dados e valores ausentes.

### Empilhar os Arquivos

- Concatenar os arquivos extraídos em um único DataFrame usando `pandas.concat`.
- Remover duplicatas com base em identificadores únicos, se necessário.

### Armazenar os Dados Concatenados

- Salvar o DataFrame concatenado em um formato de armazenamento eficiente como Parquet ou HDF5 para operações rápidas de leitura e escrita.
- Manter uma versão anterior do dataset para recuperação em caso de erro na atualização.

### Automatizar o Processo

- Agendar a execução do script de atualização usando um scheduler como `cron` (Linux) ou o Agendador de Tarefas do Windows.
- Configurar notificações (por exemplo, via email ou Slack) para alertar sobre o sucesso ou falha do processo de atualização.

### Gerenciar Logs e Erros

- Implementar um sistema de logging para registrar todas as etapas do processo, incluindo tentativas de download, verificações de integridade, extrações e concatenações.
- Registrar erros e exceções para facilitar a depuração.

### Manter a Documentação Atualizada

- Documentar o processo de atualização, incluindo dependências, instruções de configuração e resolução de problemas comuns.
- Atualizar a documentação sempre que houver mudanças no processo ou nas dependências.

## 3. Consulta em banco de dados compatível com SQL, como MySQL ou PostgreSQL.

```sql
SELECT
    e.employee_name,
    d1.department_name AS first_department,
    d2.department_name AS current_department,
    COUNT(DISTINCT edh.department_id) AS department_count
FROM
    employees e
JOIN
    employee_department_history edh ON e.employee_id = edh.employee_id
JOIN
    departments d1 ON d1.department_id = (
        SELECT edh1.department_id
        FROM employee_department_history edh1
        WHERE edh1.employee_id = e.employee_id
        ORDER BY edh1.start_date
        LIMIT 1
    )
JOIN
    departments d2 ON d2.department_id = (
        SELECT edh2.department_id
        FROM employee_department_history edh2
        WHERE edh2.employee_id = e.employee_id
        AND edh2.end_date IS NULL
        LIMIT 1
    )
GROUP BY
    e.employee_id, e.employee_name, d1.department_name, d2.department_name;
```

## 4. Utilizando processamento paralelo (pyspark) em cluster.

### Criar um Índice de Dados

Gerar um índice que mapeie identificadores únicos para posições específicas no arquivo Parquet, armazenando-o em um banco de dados leve como SQLite.

### Particionar o Arquivo Parquet

Dividir o arquivo Parquet em partes menores baseadas em uma chave de busca (como IDs) para facilitar o acesso a segmentos específicos.

### Utilizar Caching

Implementar caching com Redis para armazenar resultados de consultas frequentes, reduzindo o tempo de acesso para essas consultas.

### Otimizar o Armazenamento

Armazenar o arquivo Parquet em um SSD para melhorar a velocidade de leitura.

### Desenvolver a API

Utilizar frameworks leves como Flask ou FastAPI para criar a API. Configure endpoints que utilizem o índice para acessar rapidamente as posições necessárias no arquivo Parquet.

### Automatizar Atualizações
Criar scripts para atualizar mensalmente o arquivo Parquet e o índice. Reiniciar a API após cada atualização para garantir que as consultas usem os dados mais recentes.

## 5. Modelo de regressão. 
•	Violação de supostos, especificamente linearidade, homoscedasticidade, independência, normalidade dos resíduos e não correlação entre os resíduos e as covariáveis. 
•	Overfitting, possivelmente se está ajustando um modelo muito complexo para o conjunto de dados disponível. O que pode estar causando que não generalize bem. 
•	Presença de multicolinearidade entre as variáveis independentes. 
•	Falhas no pré-processamento e engenharia de recursos. Causando problemas como presença de outlier e valores ausentes. 


## 6. ELT e ETL. 
d) Estrutura de dados em nuvem são ideais para a adoção de estratégias ELT, devido a maior rapidez no carregamento dos dados e a adequada capacidade de processamento posterior.


#### Muito obrigado!
