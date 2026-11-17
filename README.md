# Olist: vendas, entregas e satisfação do cliente

Dashboard em Power BI feito com a base pública da Olist (e-commerce brasileiro, 2016 a 2018), com cerca de 99 mil pedidos. Os dados passam por um banco PostgreSQL antes de chegar ao Power BI. É o meu primeiro projeto de portfólio na área de dados.

<img width="1366" height="766" alt="Dashboard" src="https://github.com/user-attachments/assets/3356a327-61b0-4080-95e7-a30c5f06382f" />


 O que o dashboard responde

1. Como o faturamento evoluiu ao longo do tempo?
2. Quais categorias vendem mais?
3. Qual é a taxa de atraso nas entregas e onde ela é maior?
4. Como os clientes avaliam as compras?

 Números gerais

| Indicador | Valor |
|---|---|
| Total de pedidos | 99.441 |
| Pedidos entregues | 96.478 |
| Faturamento total | R$ 13,59 mi |
| Ticket médio | R$ 136,68 |
| Nota média das avaliações | 4,09 |
| Taxa de atraso | 8,11% |

 O que encontrei:

- Faturamento: as vendas são pequenas em 2016, crescem ao longo de 2017 com um pico no fim do ano (novembro, que coincide com a Black Friday) e ficam perto de R$ 1 mi por mês em 2018.
- Categorias: `beleza_saude` (R$ 1,26 mi) e `relogios_presentes` (R$ 1,21 mi) lideram, seguidas de `cama_mesa_banho` e `esporte_lazer`. As quatro juntas somam cerca de um terço do faturamento (R$ 4,50 mi de R$ 13,59 mi).
- Atrasos: a média nacional é de 8,11%, mas varia muito por estado. Os seis estados com maior taxa são todos do Nordeste (AL, MA, PI, CE, SE e BA). Alagoas chega a 23,93%, o Maranhão a 19,67% e o Piauí a 15,97%, contra 5,62% em São Paulo. Não analisei a causa.
- Avaliações: mais da metade dos clientes dá nota 5.

 Fluxo dos dados:

1. Criei o banco de dados no PostgreSQL.
2. Importei os arquivos da Olist para o banco.
3. Conectei o Power BI direto ao banco e montei o modelo e as medidas a partir dele.

 Como o modelo foi montado

Modelo dimensional, com as tabelas:

- Dimensões: `dClientes` (cidade e estado) e `dProdutos` (categoria, peso e dimensões).
- Fatos: `fPedidos` (datas e status do pedido), `fitens` (preço e frete de cada item) e `fAvaliacoes` (notas de 1 a 5).
- Medidas: todas na tabela `_medidas`.

 Exemplos de medidas em DAX

```dax
Faturamento Total = SUM(fitens[price])

Total Entregues =
CALCULATE(
    [Total Pedidos];
    fPedidos[order_status] = "delivered"
)

Taxa Atraso =
VAR _PedidosAtrasados =
    CALCULATE(
        [Total Entregues];
        FILTER(
            fPedidos;
            fPedidos[order_delivered_customer_date] > fPedidos[order_estimated_delivery_date]
        )
    )
RETURN
DIVIDE(_PedidosAtrasados; [Total Entregues]; 0)
```
 Próximos passos

- Comparar a nota média dos pedidos entregues no prazo com a dos atrasados, para saber se o atraso derruba a avaliação.
- Investigar por que o Nordeste concentra os atrasos.

Fonte: [Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce), no Kaggle. Os arquivos de dados não estão neste repositório: baixe no Kaggle para reproduzir.

Ferramentas:

PostgreSQL, Power BI Desktop, Power Query e DAX.
