# Análise Preditiva de NPS para E-commerce

Análise exploratória de dados operacionais de um e-commerce com o objetivo de entender quais fatores influenciam a satisfação do cliente (medida pelo NPS) e propor ações práticas para melhorar a experiência antes mesmo da aplicação da pesquisa.

Projeto desenvolvido como entrega da Fase 1 do Tech Challenge da Pós-Tech em IA (FIAP).

## Sumário

1. [Objetivo do projeto](#objetivo-do-projeto)
2. [O problema de negócio](#o-problema-de-negócio)
3. [Base de dados](#base-de-dados)
4. [Metodologia](#metodologia)
5. [Principais descobertas](#principais-descobertas)
6. [Recomendações](#recomendações)
7. [Limitações](#limitações)
8. [Estrutura do repositório](#estrutura-do-repositório)
9. [Como reproduzir](#como-reproduzir)
10. [Tecnologias utilizadas](#tecnologias-utilizadas)
11. [Autor](#autor)

## Objetivo do projeto

Com o crescimento do e-commerce, a empresa passou a lidar com um volume cada vez maior de pedidos, entregas e interações com clientes. Esse crescimento trouxe ganhos de escala, mas também revelou uma alta variabilidade no Net Promoter Score (NPS) entre diferentes perfis de consumidores.

O objetivo deste projeto é transformar dados operacionais (informações de pedidos, logística e atendimento) em insights acionáveis, respondendo à seguinte pergunta central:

> Quais fatores operacionais realmente influenciam a satisfação do cliente, e como a empresa pode agir de forma proativa para melhorar a experiência antes mesmo da aplicação da pesquisa de NPS?

## O problema de negócio

Atualmente o NPS é coletado apenas após o encerramento da jornada de compra. Isso limita a capacidade da empresa de antecipar problemas e atuar de forma preventiva, deixando a operação em uma postura reativa: o cliente insatisfeito só é identificado quando já é tarde demais para reverter a situação.

O NPS é especialmente importante para um e-commerce porque, nesse setor, o concorrente está a um clique de distância e o custo de troca para o cliente é muito baixo. Reter quem já comprou protege a margem diretamente, e o NPS funciona como um indicador antecedente de retenção e crescimento.

## Base de dados

O arquivo `desafio_nps_fase_1.csv` contém 2.500 registros, cada um representando um pedido e o cliente associado. A base reúne informações de pedido, logística, atendimento e indicadores internos de negócio.

### Dicionário de dados

| Coluna | Descrição |
|--------|-----------|
| `customer_id` | Identificador único do cliente |
| `order_id` | Identificador único do pedido |
| `customer_age` | Idade do cliente |
| `customer_region` | Região geográfica do cliente |
| `customer_tenure_months` | Tempo de relacionamento do cliente com a empresa (em meses) |
| `order_value` | Valor total do pedido |
| `items_quantity` | Quantidade de itens no pedido |
| `discount_value` | Valor de desconto aplicado ao pedido |
| `payment_installments` | Número de parcelas do pagamento |
| `delivery_time_days` | Tempo total de entrega (em dias) |
| `delivery_delay_days` | Quantidade de dias de atraso na entrega |
| `freight_value` | Valor do frete |
| `delivery_attempts` | Número de tentativas de entrega |
| `customer_service_contacts` | Número de contatos do cliente com o atendimento |
| `resolution_time_days` | Tempo para resolução de problemas (em dias) |
| `complaints_count` | Número de reclamações registradas pelo cliente |
| `repeat_purchase_30d` | Indica se houve recompra em até 30 dias (0 = não, 1 = sim) |
| `csat_internal_score` | Score interno de satisfação do cliente |
| `nps_score` | Nota de satisfação do cliente (NPS), de 0 a 10, coletada após a compra |

### Variável alvo

A variável alvo é o `nps_score`. Ela foi escolhida por ser a única variável da base que mede diretamente a percepção e a lealdade do cliente, enquanto todas as demais são dados operacionais. A partir dela foi criada uma variável categórica seguindo a metodologia oficial do NPS:

- **Detrator:** nota de 0 a 6
- **Neutro:** nota de 7 a 8
- **Promotor:** nota de 9 a 10

## Metodologia

O trabalho foi organizado em duas etapas principais, cada uma em um notebook dedicado.

### 1. Tratamento e preparação dos dados (`01_tratamento.ipynb`)

Nesta etapa a base foi carregada e passou por uma série de verificações de qualidade: checagem de tipos de dados, identificação de valores nulos, identificação de registros e identificadores duplicados, e uma verificação de consistência (sanity check) para detectar valores impossíveis, como valores negativos onde não deveria haver ou notas de NPS fora do intervalo de 0 a 10.

A base não apresentou valores nulos nem registros duplicados, e todos os valores mostraram-se dentro de faixas plausíveis. Por fim, foi criada a variável alvo categórica (`nps_category`) e a base tratada foi salva separadamente da original, preservando o dado bruto.

### 2. Análise exploratória (`02_eda.ipynb`)

A análise seguiu uma lógica que vai do geral para o específico. Primeiro foi observada a distribuição da variável alvo, depois a matriz de correlação foi usada como bússola para identificar quais variáveis merecem investigação, e em seguida cada fator relevante foi analisado em profundidade contra o NPS. Cada gráfico é acompanhado de uma interpretação de negócio, priorizando a comunicação sobre a estatística pura.

## Principais descobertas

**A base é predominantemente detratora.** Cerca de 84% dos clientes são detratores, o que evidencia um problema estrutural de satisfação.

**O perfil do cliente não influencia o NPS.** Idade, tempo de relacionamento, região e valor do pedido apresentaram correlação praticamente nula com a satisfação. O que define o NPS não é quem o cliente é, e sim a experiência operacional entregue a ele.

**O atraso na entrega é o principal fator de insatisfação.** É a variável com maior correlação negativa com o NPS. Um detalhe importante é que não é o tempo total de entrega que importa (correlação praticamente nula), e sim o atraso em relação ao prazo prometido. Existe um ponto de ruptura claro: a partir de 3 dias de atraso, o cliente já se torna detrator.

**O atraso é a regra, não a exceção.** Cerca de 89% dos pedidos são entregues com algum atraso, o que sugere que o prazo prometido está mal calibrado, e não que a operação seja necessariamente lenta.

**Reclamações e contatos com o atendimento caminham juntos.** As duas variáveis apresentam correlação alta entre si (0,75), indicando que provavelmente fazem parte do mesmo episódio de insatisfação.

**A satisfação está diretamente ligada à receita.** Nenhum detrator realizou recompra em 30 dias, enquanto 100% dos promotores recompraram. A direção dessa relação é importante: é a satisfação que leva à recompra, e não o contrário.

**O indicador interno de satisfação (CSAT) mostrou-se mal calibrado.** Ele dá forte peso ao tempo de resolução de problemas, um fator de baixo impacto no NPS, e subestima o atraso, que é o mais relevante.

## Recomendações

1. **Recalibrar o prazo de entrega prometido.** Como o problema não é a velocidade e sim o atraso em relação à promessa, ajustar o prazo prometido em cerca de 2 dias colocaria a maior parte dos pedidos dentro do prazo, sem qualquer investimento adicional em logística. O ajuste deve ser calibrado com dados reais, sem exagerar, para não reduzir a conversão de vendas.

2. **Ouvir e categorizar as reclamações.** Hoje a empresa registra que houve uma reclamação, mas não o motivo. Passar a registrar a categoria da reclamação permite atacar a causa raiz em análises futuras.

3. **Priorizar logisticamente os pedidos com maior risco de atraso elevado.** O atraso alto (3 dias ou mais) é o que causa o maior estrago no NPS.

## Limitações

- Os dados são de uma base controlada. Os padrões e as relações são confiáveis, mas alguns valores absolutos (como os 84% de detratores ou os 100% de recompra dos promotores) são mais extremos do que se observaria em um cenário real.
- A análise identifica correlações, não causalidade. As relações foram interpretadas com bom senso de negócio, mas sem afirmar mecanismos de causa.
- A recomendação de recalibrar o prazo de entrega tem um limite: prazos prometidos longos demais podem reduzir a conversão de vendas.

## Estrutura do repositório

```
nps-preditivo-ecommerce/
├── data/
│   ├── raw/                  # base de dados original, intocada
│   │   └── desafio_nps_fase_1.csv
│   └── processed/            # base tratada gerada pelo notebook de tratamento
│       └── nps_tratado.csv
├── notebooks/
│   ├── 01_tratamento.ipynb   # carga, verificação de qualidade e preparação
│   └── 02_eda.ipynb          # análise exploratória
├── reports/
│   └── figures/              # gráficos exportados da análise
├── README.md
└── requirements.txt
```

## Como reproduzir

1. Clone o repositório:

```bash
git clone https://github.com/JoaoPaulo-66/nps-preditivo-ecommerce.git
cd nps-preditivo-ecommerce
```

2. (Opcional, mas recomendado) Crie e ative um ambiente virtual:

```bash
python -m venv venv
# Windows
venv\Scripts\activate
# Linux / Mac
source venv/bin/activate
```

3. Instale as dependências:

```bash
pip install -r requirements.txt
```

4. Execute os notebooks na ordem, de cima para baixo:

- Primeiro `notebooks/01_tratamento.ipynb`, que gera a base tratada em `data/processed/`.
- Depois `notebooks/02_eda.ipynb`, que realiza a análise exploratória a partir da base tratada.

Para garantir a reprodutibilidade, recomenda-se reiniciar o kernel e executar todas as células na ordem (Restart & Run All).

## Tecnologias utilizadas

- Python 3
- pandas (manipulação de dados)
- numpy (operações numéricas)
- matplotlib e seaborn (visualização)
- Jupyter Notebook

## Autor

João Paulo Rezende de Oliveira

www.linkedin.com/in/joaopaulorezendeoliveira
