# Grafana com Protheus utilizando Docker

## Introdução
A observabilidade é um conceito essencial para monitoramento e análise de sistemas modernos, especialmente em arquiteturas distribuídas e baseadas em microserviços. Este documento explora o uso do Grafana integrado ao Protheus para visualização e análise de métricas utilizando Docker no Windows.

## Grafana com Protheus

### 1. Qual problema a tecnologia resolve?

#### Contextualização
Em sistemas empresariais como o Protheus, é fundamental monitorar métricas de desempenho, disponibilidade e integridade do sistema. A falta de um monitoramento adequado pode levar a problemas como:
- Dificuldade na identificação de falhas e gargalos de desempenho.
- Falta de visibilidade sobre a utilização dos recursos e processos críticos.
- Tempo elevado para diagnóstico de incidentes.

#### Problemas resolvidos
- **Monitoramento centralizado**: O Grafana permite a visualização consolidada de métricas em painéis interativos.
- **Alertas e notificações**: Identificação proativa de problemas antes que afetem os usuários.
- **Correlação de dados**: Integração com diferentes fontes de dados para análise aprofundada.
- **Histórico de desempenho**: Registro e análise de tendências ao longo do tempo.

### 2. Como ela resolve?

#### Grafana
- Plataforma open-source para monitoramento e visualização de métricas.
- Suporta múltiplas fontes de dados como Prometheus, InfluxDB e bancos relacionais.
- Permite criação de painéis personalizados com gráficos interativos.

#### Integração com Protheus
- O Protheus pode expor métricas através de logs, APIs ou banco de dados.
- Utilização de ferramentas como **Prometheus** para coletar métricas do Protheus.
- Configuração de dashboards no Grafana para monitorar KPIs do sistema.

### 3. Quais as alternativas e comparações?

| Tecnologia       | Coleta de Dados  | Visualização | Alertas  | Facilidade de Integração |
|----------------|----------------|-------------|---------|------------------------|
| Grafana + Prometheus | Métricas em tempo real | Sim | Sim | Alta |
| Zabbix        | Agentes e SNMP   | Sim | Sim | Média |
| Kibana        | Logs             | Sim | Não | Alta |

### 4. Demonstração prática utilizando Docker

### 4.1. Instalação e Configuração do Ambiente

#### 4.1.1 Instalar Docker
1. Baixe e instale o [Docker Desktop](https://www.docker.com/products/docker-desktop/).
2. Certifique-se de que o Docker esteja em execução.

#### 4.1.2 Criar um arquivo `docker-compose.yml`
1. Em um diretório de sua escolha, crie um arquivo chamado `docker-compose.yml` com o seguinte conteúdo:

```yaml
services:
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    networks:
      - monitoring
  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    ports:
      - "3001:3000"
    environment:
      GF_SECURITY_ADMIN_PASSWORD: "admin"
    depends_on:
      - prometheus
    networks:
      - monitoring
  node_exporter:
    image: prom/node-exporter:latest
    container_name: node_exporter
    ports:
      - "9100:9100"
    networks:
      - monitoring
      
networks:
  monitoring:
    driver: bridge

```

2. No mesmo diretório, crie um arquivo `prometheus.yml` com o seguinte conteúdo:

```yaml
global:
  scrape_interval: 15s  

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['prometheus:9090']  
  - job_name: 'node'
    static_configs:
      - targets: ['node_exporter:9100'] 

```

3. Execute o seguinte comando para iniciar os containers:

```sh
docker-compose up -d
```

4. Verifique se os containers estão rodando:

```sh
docker ps
```

5. Acesse o Prometheus em `http://localhost:9090` e o Grafana em `http://localhost:3000`.


### 4.2. Configuração do Grafana

1. Acesse `http://localhost:3000`.
2. Entre com:
   - **Usuário:** admin
   - **Senha:** admin
3. No Grafana, clique em **Connections > Add new connection**.
4. Busque por **Prometheus**.
5. Clique em **Add new data source**, no canto superior direito.
6. No campo **URL**, digite `http://prometheus:9090`.
7. Clique em **Save & Test** para confirmar a conexão.
 
### 4.3. Configuração Painel Grafana

1. No menu lateral do Grafana, clique em **Dashboards > Create dashboard**.
2. Clique em **Add new visualization**.
3. Na seção de consultas, selecione a fonte de dados **Prometheus**.
4. Digite a métrica desejada, como `node_cpu_seconds_total`.
5. Adicione **labels** para melhor filtragem.
6. Clique em **Run queries** para visualizar no gráfico.
7. Adicione multiplas queries clicando em **Add query**.
7. Salve o Dashboard.

### 5. Laboratório
1. Agora que sabemos criar um dashboard vamos adicionar um alerta, para verificar se o uso da CPU passa de um determinado limite.
2. Abra o dashboard e clique em **edit**.
3. Agora vá no painel e clique em **edit** novamente.
4.  Clique em **Alert** e depois em **New alert rule**.
5.  No tópico 2 **clique na lixeira** para apagar as expressions existentes.
6.  Clique em **Add expression** e **Classic condition(legacy)** e clique em **Set as alert condition**.
7.  E configure para alertar quando o valor for acima de 40.
8.  Adicione um folder novo nos tópicos 3 e 4.
9.  No tópico 5 clique em **View or create contact points** e clique em **Create contact point**.
10.  Selecione **Webhook**.
11.  Visite o site https://webhook.site e copie **Your unique URL**
12.  Volte ao **Grafana**, coloque no campo **URL** e aperte em **Test**.
13.  Volte ao site e veja se chegou alguma notificação.
14.  Clique em **Save contact point**.
15.  Na página home, clique em **Save rule and exit**.
16.  Verifique se houve um alerta no **webhook.site**

## Conclusão
- O Grafana oferece uma solução robusta para visualização e monitoramento de métricas do Protheus.
- O uso de Docker facilita a implantação e gerenciamento das ferramentas.
- Dashboards e alertas melhoram a eficiência operacional e reduzem o tempo de resposta a incidentes.
