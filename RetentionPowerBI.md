:dart: **Objetivo:** criar uma análise de Retenção no Powerbi com o intuito de entender o % de clientes que está retornando para utlizar a funcionalidade em cada mês.

:dart: **Objective:** create a Retention analysis in Powerbi in order to understand the % of customers that are returning to use the functionality each month.

**Informações Gerais:** Os dados são falsos e mostram os acessos mensais das empresas em uma nova funcionalidade que foi lançada.
**Information:** The data is fake and shows companies' monthly access to a new feature that was launched.

- **Primeiro Passo: pegar o primeiro acesso de cada empresa**
- **First Step: get the first access for each company**
```dax
FirstAccessDate = 
 var FirstAccess = FIRSTNONBLANK(New_Feature_Access[Company],TRUE())  --variavel que pega primeira data de cada empresa
 RETURN
 CALCULATE(
    MIN(New_Feature_Access[Access Date]), -- menor nada
    FILTER(
        ALL(New_Feature_Access), -- filtrando toda a tabela
        New_Feature_Access[Company] = FirstAccess
        )
    )
```
-  **Segundo Passo: criar a columa de cohort**
 - **Second Step: create the cohort column**
```dax
  CohortColumn = DATEDIFF(New_Feature_Access[FirstAccessDate],New_Feature_Access[Access Date],MONTH)
```

-  **Terceiro Passo: criar uma medida que calcula o % de retenção de cada mês**
-  **Third Step: create a measure that calculates the retention % for each month**
```dax
Retention_Cohort = 
DIVIDE(
    DISTINCTCOUNTNOBLANK(New_Feature_Access[Company]),
    CALCULATE(
        DISTINCTCOUNTNOBLANK(New_Feature_Access[Company]),
        New_Feature_Access[CohortColumn] = 0
    )
)
```
**Após a criação das colunas e da medida, basta criamos uma matriz com a CohortColumn como colunas e FirstDate como Linhas. O resultado final com formatação condicional será:**
