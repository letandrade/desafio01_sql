# Desafio 01: SQL

Estruture as seguintes queries de SQL:
1) Escreva uma query que retorne a receita por nome do cliente, ordenada da maior para a menor.
2) Escreva uma query que retorne a receita por Filial entre os dias 01/01/2022 e 03/01/2022.
3) Escreva uma query que retorne clientes que nunca efetuaram uma compra.
4) Escreva uma query que retorne somente clientes que tiveram receita maior que 10.000.
5) Escreva uma query que retorne o primeiro pedido de cada um dos clientes.

Material necessário: relação de tabelas em planilha.
Linguaguem utiulizada: Oracle SQL.

### 1) Escreva uma query que retorne a receita por nome do cliente, ordenada da maior para a menor.
   
SELECT A.NAME, NVL(SUM(B.REVENUE),0) AS RECEITA_TOTAL  -- Seleciona o nome do cliente e a soma da receita de suas compras, retornando 0 caso não haja receita
FROM DIM_CUSTOMER A  -- Define a tabela de clientes como a base principal
LEFT JOIN FACT_ORDERS B  -- Faz um LEFT JOIN com a tabela de pedidos para incluir todos os clientes, independentemente de terem compras
ON A.ID = B.CUSTOMER_ID  -- Condição de junção com base no ID do cliente
GROUP BY A.NAME  -- Agrupa os resultados por nome de cliente
ORDER BY RECEITA_TOTAL DESC;  -- Ordena a receita total de cada cliente em ordem decrescente


### 2) Escreva uma query que retorne a receita por Filial entre os dias 01/01/2022 e 03/01/2022.

SELECT ORGANIZATION, SUM(B.REVENUE) AS RECEITA_TOTAL  -- Seleciona o nome da filial e a soma da receita gerada nesse período
FROM DIM_ORGANIZATION A  -- Define a tabela de organizações (filiais) como base
LEFT JOIN FACT_ORDERS B  -- Faz um LEFT JOIN com a tabela de pedidos para incluir todas as filiais, independentemente de terem vendas
ON A.ID = B.ORGANIZATION_ID  -- Condição de junção baseada no ID da filial
WHERE B.CREATED_AT BETWEEN '01/01/2022' AND '03/01/2022'  -- Filtra os registros de pedidos entre as datas especificadas
GROUP BY ORGANIZATION;  -- Agrupa os resultados por filial

 
### 3) Escreva uma query que retorne clientes que nunca efetuaram uma compra.

SELECT A.NAME  -- Seleciona o nome dos clientes
FROM DIM_CUSTOMER A  -- Define a tabela de clientes como base
LEFT JOIN FACT_ORDERS B  -- Faz um LEFT JOIN com a tabela de pedidos para incluir todos os clientes, independentemente de terem compras
ON A.ID = B.CUSTOMER_ID  -- Condição de junção baseada no ID do cliente
WHERE A.ID NOT IN (SELECT DISTINCT CUSTOMER_ID FROM FACT_ORDERS)  -- Filtra apenas os clientes que não têm nenhum pedido na tabela de pedidos
ORDER BY A.NAME;  -- Ordena o resultado em ordem alfabética

### 4) Escreva uma query que retorne somente clientes que tiveram receita maior que 10.000.

SELECT A.NAME, SUM(B.REVENUE) AS RECEITA_TOTAL  -- Seleciona o nome do cliente e a soma de suas receitas
FROM DIM_CUSTOMER A  -- Define a tabela de clientes como base
LEFT JOIN FACT_ORDERS B  -- Faz um LEFT JOIN com a tabela de pedidos para incluir todos os clientes, independentemente de terem compras
ON A.ID = B.CUSTOMER_ID  -- Condição de junção baseada no ID do cliente
HAVING SUM(B.REVENUE) > 10000  -- Filtra para retornar apenas os clientes com receita superior a 10.000
GROUP BY A.NAME  -- Agrupa os resultados por nome de cliente

  
### 5) Escreva uma query que retorne o primeiro pedido de cada um dos clientes.

SELECT A.NAME AS CLIENTE, NVL(MIN(B.ID_ORDER),'Sem pedidos') AS PRIMEIRO_PEDIDO  -- Seleciona o nome do cliente e o ID do seu primeiro pedido, ou "Sem pedidos" caso não haja
FROM DIM_CUSTOMER A  -- Define a tabela de clientes como base
LEFT JOIN FACT_ORDERS B  -- Faz um LEFT JOIN com a tabela de pedidos para incluir todos os clientes, independentemente de terem pedidos
ON A.ID = B.CUSTOMER_ID  -- Condição de junção baseada no ID do cliente
GROUP BY A.NAME  -- Agrupa os resultados por nome de cliente
ORDER BY A.NAME  -- Ordena o resultado em ordem alfabética de clientes
