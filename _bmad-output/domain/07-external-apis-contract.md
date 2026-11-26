# 07 - Arquitetura de Ingestão e Contrato de APIs Externas (`07-external-apis-contract.md`)

Especificação arquitetural para enriquecimento do catálogo botânico, taxonomia e dados climáticos regionais via APIs abertas, preservando o determinismo e as regras de segurança da base agronômica brasileira (`01` a `06`).

---

## 1. Princípio Arquitetural: Separação de Camadas e Precedência

- **Camada A (Core Agronômico e Segurança — Arquivos `01` a `06`):** Fonte primária e soberana para regras determinísticas (`SE/ENTÃO`), dimensionamento de recipientes urbanos, substratos, carências de adubação, defensivos naturais, diagnóstico visual e bloqueios de segurança (`fail-safe`) para pets, crianças e solo urbano.
- **Camada B (Enriquecimento via APIs Externas):** Pipeline assíncrono de *seed*, normalização e geoclima. **Regra de ouro:** Nenhuma API externa pode sobrescrever regras agronômicas, dosagens, carências alimentares ou alertas de toxicidade já validados na Camada A.
- **Isolamento de Cota e Latência:** Proibidas chamadas síncronas às APIs de catálogo (`Perenual` e `Trefle`) durante a jornada crítica do usuário em tempo real. Todos os dados botânicos externos devem ser ingeridos, normalizados, traduzidos para Português e persistidos no banco de dados local.

---

## 2. Matriz de Papel e Escopo por API

| API / Base Aberta | Papel no Sistema | Campos Extraídos / Mapeados | Restrições e Regras de Precedência |
| :--- | :--- | :--- | :--- |
| **Perenual API** | Cobertura ampla de **plantas ornamentais de interior** (apartamento/escritório) e triagem inicial de toxicidade. | `common_name`, `scientific_name`, `watering` (frequência base), `sunlight` (nível de luz), `hardiness` / faixa térmica, `default_image` (fotos) e flags `poisonous_to_pets` / `poisonous_to_humans`. | Traduzir níveis de luz e rega para os enums padronizados do app em Português. Se `poisonous_to_pets` divergir da base veterinária brasileira (`04-pet-safety.md`), prevalece o status mais restritivo (`true`). |
| **Trefle API** | Padronização **taxonômica** e parâmetros ecológicos estruturais. | `scientific_name` (chave canônica), `family`, `genus`, `ph_minimum`, `ph_maximum`, `light`, `minimum_temperature`. | Usar o nome científico aceito como chave de deduplicação (`canonical_scientific_name`) entre as tabelas de `01-species-catalog.md` e a Perenual. |
| **Flora e Funga do Brasil (Reflora / JBRJ)** | Validação taxonômica oficial e ocorrência por bioma/estado para **espécies nativas brasileiras**. | Nome científico aceito, sinonímias botânicas, endemismo, forma de vida (árvore, arbusto, erva, trepadeira) e estados de ocorrência no Brasil. | Base de referência para nativas usadas em paisagismo e bonsai (ex.: pitangueira, jabuticabeira, ipês, caliandra). Não fornece dados de cultivo em vaso. |
| **Embrapa AgroAPI (Bioinsumos / Agrofit)** | Validação regulatória no Brasil (MAPA) de **bioinsumos e defensivos naturais**. | Cadastro oficial de inoculantes, agentes de controle biológico e produtos naturais registrados no Brasil, culturas alvo e recomendações oficiais. | Usada exclusivamente para validar produtos comerciais seguros sugeridos em `03-diagnostics-matrix.md` e `05-urban-horticulture.md`, respeitando os bloqueios residenciais de `04-pet-safety.md`. |
| **INMET / Open-Meteo API** | Ajuste dinâmico de alertas de rega e proteção térmica conforme o **clima local do município brasileiro**. | Temperatura atual/previsão (mín/máx), umidade relativa do ar, radiação solar e precipitação por geolocalização/CEP. | Única integração autorizada a operar de forma periódica/dinâmica para modular o intervalo base de rega de `02-watering-algorithms.md` (ex.: ondas de calor ou baixa umidade relativa). |

---

## 3. Pipeline de Ingestão, Normalização e Deduplicação (`Seed`)

1. **Chave Canônica de Identificação:** Utilizar o nome científico normalizado (`genus + species`, em minúsculas, sem autoria) como chave primária de cruzamento entre `01-species-catalog.md`, `04-pet-safety.md`, Trefle e Perenual.
2. **Fluxo de Resolução de Conflitos (`Merge Priority`):**
   - **Toxicidade (`is_pet_toxic`, `is_human_toxic`):** `04-pet-safety.md` (PUBVET / FUNED-SINITOX) > `Perenual API` > `N/A` (aplica alerta preventivo *fail-safe* até validação).
   - **Parâmetros de Cultivo Urbano / Hortaliças / Bonsai:** Base Acadêmica Brasileira (`01`, `02`, `05`, `06`) > `Trefle` / `Perenual`.
   - **Ornamentais de Interior Ausentes na Base Local:** `Perenual` (manejo, foto, toxicidade) + `Trefle` (taxonomia, pH, temperatura mínima), com marcação de origem `source = "external_api_enriched"`.
3. **Localização (i18n) na Ingestão:** Todos os campos textuais exibidos ao usuário (nomes populares brasileiros, dicas de rega, níveis de luminosidade e sintomas de toxicidade) devem ser traduzidos e revisados em Português durante o pipeline de ingestão, armazenando o termo original em Inglês apenas como metadado de auditoria.

---

## 4. Segurança de Credenciais e Governança

- **Segregação de Chaves:** Chaves (`TREFLE_API_TOKEN`, `PERENUAL_API_KEY`, credenciais Embrapa) devem residir exclusivamente em `.env` / gerenciador de segredos do backend, nunca expostas no código cliente (mobile/web) ou indexadas pelos agentes do BMAD (`.gitignore`, `.copilotignore`, `.geminiignore`).
- **Cache e Rate Limiting:** Respeitar os limites diários do plano gratuito da Perenual e Trefle executando cargas incrementais em lote (*batch*) e persistindo imagens em storage próprio ou CDN para evitar links quebrados.
