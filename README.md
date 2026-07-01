# Projest: Sistema de Gestão de Recursos e Análises de Custos para Projetos

O **Projest** é uma solução em Engenharia de Dados voltada para a modelagem, implantação e consulta de bancos de dados relacionais estruturados. O sistema foi concebido para resolver um dos principais desafios de empresas prestadoras de serviços (como escritórios de engenharia, arquitetura e consultorias): a alocação eficiente, o rastreamento de uso e a auditoria de custos de recursos humanos e físicos em múltiplos projetos.

A arquitetura do banco de dados foi desenhada para centralizar o apontamento de horas (*timesheets*) e despesas operacionais com precisão de minutos, transformando dados brutos de rotina em métricas consolidadas de *Business Intelligence* e análise de viabilidade financeira.

---

## 🎯 OBJETIVO

O objetivo principal do projeto foi modelar e estruturar um banco de dados relacional robusto capaz de automatizar o controle de insumos e fornecer respostas ágeis para quatro pilares críticos de tomada de decisão executiva:

1. **Previsibilidade de Custos:** Mensurar a quantidade exata de horas trabalhadas por cargo/função em cada projeto para refinar orçamentos e propostas comerciais futuras.
2. **Subsídio de Remuneração:** Consolidar o total de horas dedicadas por colaborador dentro de qualquer janela temporal configurável para automatizar processos de folha de pagamento e reembolsos.
3. **Eficiência Operacional (Capacity Planning):** Fornecer dados para analisar a alocação de pessoal, identificando gargalos de sobrecarga ou ociosidade na organização.
4. **Análise de Desvio Financeiro (*Budget vs. Actual*):** Comparar em tempo real o status de evolução física dos projetos em relação ao orçamento financeiro e de recursos originalmente contratados.

---

## 🛠️ TECNOLOGIAS E INFRAESTRUTURA

Para garantir escalabilidade, segurança e acessibilidade para equipes distribuídas geograficamente (trabalho remoto e fiscalização de campo), a infraestrutura do projeto foi desenhada sob as seguintes especificações:

* **SGBD Core:** MySQL (Solução open-source de alta performance para operações transacionais).
* **Hospedagem em Nuvem:** Cloud Computing via AWS (Amazon Web Services), garantindo disponibilidade global e conexões simultâneas seguras (a implementar).
* **Segurança e Integridade:** Aplicação rigorosa de restrições de chaves primárias (PK), chaves estrangeiras (FK) e integridade referencial para evitar duplicidade ou corrupção de dados históricos.
* **Evolução de Arquitetura:** Modelagem lógica escalável documentada, prevendo uma futura migração nativa para ambientes corporativos mais robustos (como Microsoft SQL Server) à medida que o volume de registros de RH se expandir.

<p align="center">
  <img src="./images/Projest-Modelo conceitual.jpg" alt="Diagrama Entidade Relacionamento - Projest" width="850">
</p>

---

## 📊 MODELAGEM CONCEITUAL E REQUISITOS DO SISTEMA

O banco de dados foi normalizado para refletir com fidelidade as regras de negócio de uma estrutura corporativa:

* **Clientes e Contratos:** Suporte a relacionamentos de um para muitos (1:N), onde um único cliente pode manter múltiplos projetos simultâneos.
* **Projetos e Gerenciamento:** Controle de escopo contendo metas de orçamento (*budget*), gerentes designados e atualização periódica do percentual de evolução física.
* **Estrutura de Tarefas e Recursos:** Decomposição de projetos em atividades padronizadas e mapeamento de recursos físicos (softwares, frotas, insumos) vinculados.
* **Matriz de Custo por Hora:** Registro histórico de cargos e ocupações com seus respectivos valores de hora/trabalho, viabilizando o cálculo retroativo de custos operacionais mesmo diante de reajustes salariais.
* **Apontamento de Timesheet:** Rastreabilidade fina de intervalos de tempo trabalhados, documentando o momento exato de início e término de cada atividade por colaborador.

---

## 📐 MODELO LÓGICO E DICIONÁRIO DE DADOS

O banco de dados foi normalizado e estruturado para garantir a integridade referencial através de 7 tabelas interligadas. Abaixo está a especificação da arquitetura das tabelas mapeadas no diagrama:

<p align="center">
  <img src="./images/Projest-Modelo Logico.jpg" alt="Modelo Lógico do Banco de Dados - Projest" width="900">
</p>

### 🗂️ Estrutura das Tabelas (Esquema Relacional)

*   **CLIENTES:** Armazena o cadastro básico de clientes contratantes.
    *   `ID_CLIENTE` (PK, INT, NOT NULL)
    *   `NOME` (VARCHAR(50), NOT NULL), `ENDERECO` (VARCHAR(50)), `EMAIL` (VARCHAR(50))
*   **PROJETOS:** Centraliza os escopos, orçamentos e gerentes responsáveis.
    *   `ID_PROJETO` (PK, INT, NOT NULL)
    *   `NOME` (VARCHAR(50), NOT NULL), `VALOR_ORCADO` (DECIMAL(10,2)), `ANDAMENTO` (DECIMAL(5,4))
    *   `ID_CLIENTE` (FK vinculada a CLIENTES)
    *   `ID_GERENTE` (FK vinculada a PESSOAS, definindo o líder do projeto)
*   **TAREFAS:** Catálogo padronizado de atividades operacionais da empresa.
    *   `ID_TAREFA` (PK, INT, NOT NULL)
    *   `NOME` (VARCHAR(50), NOT NULL)
*   **CARGOS:** Matriz de custo-hora baseada na senioridade ou função jurídica.
    *   `ID_CARGO` (PK, INT, NOT NULL)
    *   `NOME` (VARCHAR(50), NOT NULL), `VALOR_HORA` (DECIMAL(5,2), NOT NULL)
*   **PESSOAS:** Cadastro de colaboradores vinculados à organização.
    *   `ID_PESSOA` (PK, INT, NOT NULL)
    *   `NOME` (VARCHAR(20), NOT NULL), `SOBRENOME` (VARCHAR(20), NOT NULL), `EMAIL` (VARCHAR(100))
    *   `ID_CARGO` (FK vinculada a CARGOS, amarrando o custo financeiro ao profissional)
*   **USO_RH (Timesheet):** Tabela de junção de alta cardinalidade para apontamento de horas com precisão temporal.
    *   `ID_USO_RH` (PK, INT, NOT NULL)
    *   `HORA_INICIO` (TIMESTAMP, NOT NULL), `HORA_FIM` (TIMESTAMP, NOT NULL)
    *   `ID_TAREFA` (FK), `ID_PESSOA` (FK), `ID_PROJETO` (FK)
*   **USO_RF:** Controle de alocação de insumos físicos (softwares, frotas, licenças) em cada projeto.
    *   `ID_USO_RF` (PK, INT, NOT NULL)
    *   `QUANTIDADE` (DECIMAL(6,2))
    *   `ID_TAREFA` (FK), `ID_PROJETO` (FK), `ID_RF` (FK)
*   **RECURSOS_FISICOS:** Cadastro técnico de insumos materiais e operacionais.
    *   `ID_RF` (PK, INT, NOT NULL)
    *   `NOME` (VARCHAR(50), NOT NULL), `UNIDADE` (VARCHAR(10)), `CUSTO_UNITARIO` (DECIMAL(6,2)), `DATA_RF` (DATE)

---

## 🧠 APRENDIZADOS EM ENGENHARIA DE DADOS

O desenvolvimento do sistema Projest consolidou competências avançadas em modelagem de dados relacionais e extração de inteligência de negócios através da linguagem SQL:

* **Abstração e Modelagem de Entidades (DER):** Capacidade de traduzir regras complexas de gestão de projetos físicos e fluxo de caixa corporativo em tabelas lógicas normalizadas.
* **Desenvolvimento de Consultas Avançadas (DML):** Criação de scripts SQL estruturados utilizando junções complexas (`JOIN`), funções de agregação (`SUM`, `COUNT`, `AVG`) e filtros temporais baseados em manipulação de campos de data e hora.
* **Raciocínio Analítico Aplicado:** O entendimento técnico de que o design correto de um banco de dados relacional é o alicerce fundamental para qualquer análise preditiva de custos futura.
