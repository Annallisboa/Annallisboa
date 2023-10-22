<h1 align="center">Objetivo: criar uma análise de Retenção no Powerbi</h1>
<h3 align="center">Os dados são falsos e mostram os acessos mensais das empresas a uma nova funcionalidade que foi lançada</h3>
<h3 align="center">O Objetivo é entender o % de clientes que está retornando para utlizar a funcionalidade em cada mês</h3>

- **Primeiro Passo: pegar o primeiro acesso de cada empresa**
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

-  **Segundo Passo: criar a columa de cohort**
  CohortColumn = DATEDIFF(New_Feature_Access[FirstAccessDate],New_Feature_Access[Access Date],MONTH)

-  **Terceiro Passo: criar uma medida que calcula o % de retenção de cada mês**
Retention_Cohort = 
DIVIDE(
    DISTINCTCOUNTNOBLANK(New_Feature_Access[Company]),
    CALCULATE(
        DISTINCTCOUNTNOBLANK(New_Feature_Access[Company]),
        New_Feature_Access[CohortColumn] = 0
    )
)
