# Informe Comparativo y Técnico: Ley Abierta (España) vs. Leyes.ar (Argentina)

Este informe presenta un análisis detallado del proyecto español **Ley Abierta** (disponible en [github.com/leyabierta](https://github.com/leyabierta) y [leyabierta.es](https://leyabierta.es)), evaluando sus similitudes, diferencias, arquitectura tecnológica y lecciones aplicables para **Leyes.ar**. 

Además, incluye al final del documento una transcripción completa de la documentación fundamental del proyecto (Manifiesto de Visión y Caso de Estudio Técnico) para posterior consulta local.

---

## 1. Ficha del Proyecto: Ley Abierta
*   **Creador/Autor:** Alejandro Martínez (Ingeniero de Software, Valencia, España - [alexmj.dev](https://alexmj.dev)).
*   **Estado:** En producción y completamente funcional (190 años de legislación española).
*   **Volumen del Corpus:** 12.275 leyes consolidadas y 43.919 commits (historial de reformas desde 1835 hasta 2026).
*   **Jurisdicciones:** 18 (Estado Nacional de España + 17 Comunidades Autónomas).
*   **Licencia:** AGPL-3.0 (Software libre recíproco para evitar la privatización SaaS de la infraestructura legal).

---

## 2. Comparación Estructural: Similitudes y Diferencias

### A. Similitudes Conceptuales
1.  **Git como Persistencia Primaria:** Ambos proyectos descartan las bases de datos relacionales tradicionales (SQL) como único motor histórico y eligen Git como el "Single Source of Truth" (Fuente Única de Verdad). Cada reforma es mapeada a un commit histórico con diferencias (*diffs*) visibles.
2.  **Formato de Texto Plano Estructurado:** Uso de **Markdown** para el cuerpo de la ley y **YAML Frontmatter** para el almacenamiento de metadatos extensibles (id, título, jurisdicción, fecha de promulgación, estado).
3.  **Filosofía Open Source y Datos Abiertos:** Ambos proyectos entienden la legislación como un bien común (*commons*) que debe ser accesible y legible por humanos e Inteligencia Artificial de forma gratuita.
4.  **Preparación para RAG (Retrieval-Augmented Generation):** Estructurar las leyes en Markdown permite vectorizarlas limpiamente, evitando los errores de lectura de PDFs (OCR) y facilitando consultas a LLMs libres de alucinaciones.

---

### B. Diferencias Fundamentales (Estrategia y Arquitectura)

| Dimensión | Ley Abierta (España) | Leyes.ar (Argentina) |
| :--- | :--- | :--- |
| **Objetivo Principal** | **Portal de Consulta y Búsqueda Histórica (Lectura):** Ingesta automática del Boletín Oficial del Estado (BOE) para reconstruir la historia legislativa hacia atrás. | **Plataforma de Co-creación y Redacción (Escritura):** Mesa de entrada digital y entorno colaborativo para debatir y refinar textos *antes* de su sanción. |
| **Flujo de Trabajo** | **Retrospectivo y Centralizado:** Un *pipeline* automatizado descarga XMLs oficiales y los convierte a Markdown firmando los commits. | **Activo y Descentralizado:** Legisladores abren ramas (*branches*), proponen cambios (*Pull Requests*) y los técnicos firman digitalmente (*GPG*) las fusiones (*merges*). |
| **Gobierno y Federalismo** | **Repositorio Único Centralizado:** Un solo monorepo que estructura las 18 jurisdicciones mediante carpetas basadas en el estándar ELI (Identificador Europeo de Legislación). | **Red Federada de Repositorios:** Cada provincia y municipio administra su propio nodo autónomo en una red Git distribuida utilizando estándares comunes. |
| **Validaciones (Linting)** | **Posteriori (Información):** Analiza inconsistencias y dependencias rotas en textos ya publicados oficialmente. | **Apriori (Asistencia Técnica):** Ejecuta un linter legislativo (*Linter-AR*) en comisiones para alertar de errores formales antes de la votación en el recinto. |

---

## 3. Análisis de la Pila Tecnológica (Tech Stack) de Ley Abierta

Alejandro Martínez diseñó una arquitectura de altísimo rendimiento orientada a la eficiencia de costos (correr todo en una única máquina virtual económica y sin GPUs):

```
       [ BOE API Abierta ]
                │
                ▼
  [ Pipeline: TS + Bun + Git ]  ──>  Genera Repo de Leyes (Markdown)
                │
                ▼
    [ Cache JSON de Trabajo ]
                │
                ▼
   [ API: Elysia + SQLite FTS5 ]
    ├── Búsqueda BM25 (FTS5)
    └── Búsqueda Semántica (486k Vectores)
         └── Custom C SIMD int8 Cosine Kernel (AVX2/NEON)
                │
                ▼
   [ Web: Astro SSG + CF Pages ] ──> leyabierta.es (Zero-JS client)
```

### Componentes Clave:
1.  **Runtime (Bun + TypeScript):** Utilizado para el pipeline de ingesta y parseo de XML a Markdown. Bun provee una velocidad de arranque significativamente mayor que Node.js para tareas repetitivas de consola.
2.  **Base de Datos y Búsqueda Tradicional (SQLite + FTS5):** Motor de base de datos ligero que almacena los metadatos y gestiona la búsqueda por palabras clave mediante el módulo FTS5 (BM25 full-text search).
3.  **El Kernel C SIMD Personalizado (Optimizando la Búsqueda Vectorial):**
    *   *El Desafío:* Buscar en 486.000 embeddings en una única CPU sin usar bases de datos vectoriales pesadas ni GPUs.
    *   *La Solución:* Un kernel escrito a mano en **C** que realiza búsquedas de similitud coseno utilizando instrucciones **SIMD** (Single Instruction, Multiple Data) cuantizadas a **int8**.
    *   *Impacto:* Redujo el tamaño del índice de embeddings de **7.6 GB a 1.4 GB (-81%)** y bajó la latencia de búsqueda de 2.1s a 0.8s en la CPU de la VM. Cuenta con rutas optimizadas para arquitecturas x86_64 (AVX2+FMA) y arm64 (NEON).
4.  **Frontend (Astro SSG + Cloudflare Pages):** La web leyabierta.es se genera de forma estática en tiempo de compilación leyendo directamente el repositorio de Markdown. Esto permite tiempos de carga inferiores a 0.3 segundos, seguridad total (sin base de datos expuesta al cliente) y costo de hosting cero en Cloudflare.

---

## 4. Lecciones y Recomendaciones para Leyes.ar

### Lección 1: El peligro de la "Explosión OR" en búsquedas legales (BM25)
*   **Problema:** En el desarrollo de Ley Abierta, se detectó que el algoritmo de búsqueda de texto tradicional (BM25) fallaba con consultas en lenguaje natural ciudadano. Por ejemplo, si un ciudadano buscaba *"horas extras que no me pagan"*, el motor SQLite FTS5 expandía la consulta y arrojaba ruido histórico del siglo XIX que contenía palabras sueltas.
*   **Solución aplicable a Leyes.ar:** Utilizar un **analizador de consultas con LLM** previo a la búsqueda que extraiga la intención del ciudadano y sesgue la consulta hacia leyes recientes, priorizando la semántica sobre la coincidencia literal de palabras.

### Lección 2: Tracing local de RAG (Opik)
*   **Recomendación:** Integrar una herramienta de trazabilidad RAG de código abierto y auto-hospedada (como **Opik**) en el pipeline de la API de Leyes.ar. Esto permite analizar la latencia y precisión de cada paso (generación de embeddings, consulta SQLite, reranking de Qwen/LLM, síntesis) localmente, garantizando que ninguna consulta confidencial o borrador de ley en debate sea enviado a servicios SaaS externos de monitoreo.

### Lección 3: Astro para portales de transparencia municipal
*   **Recomendación:** Para el piloto municipal de Leyes.ar, el portal público de cara al ciudadano debe ser estático (usando **Astro**). Al igual que Ley Abierta, esto permite que municipios con recursos de infraestructura limitados ofrezcan un servicio de consulta ultra-rápido, sin costos de servidores de base de datos activos en el frontend.

---

## 5. Respaldos Fundacionales de Ley Abierta (Transcripción Local)

Para garantizar su disponibilidad en caso de cambios en el repositorio externo, se transcriben a continuación los documentos clave de Ley Abierta.

### Documento A: Manifiesto de Visión (`VISION.md`)
*Fuente original:* [github.com/leyabierta/leyabierta/blob/main/VISION.md](https://github.com/leyabierta/leyabierta/blob/main/VISION.md)

```markdown
# Visión de Ley Abierta

## Por qué existe este proyecto

La ley es algo abstracto y difícil de entender. El lenguaje jurídico es necesariamente técnico y estricto, pero eso crea una barrera: millones de personas desconocen sus derechos o no entienden qué se está legislando, cuando todas las leyes nos aplican a todos.

Ley Abierta nace para cerrar esa brecha. No para sustituir el lenguaje legal, sino para traducirlo: que tanto una persona de 90 años como una adolescente de 14 puedan informarse correctamente sobre qué leyes existen, qué se está legislando y cómo les afecta.

## Principios

### 1. Neutralidad factual

Ley Abierta informa, no opina. Nunca diremos si una ley es buena o mala. Presentamos los hechos — el texto, los cambios, las relaciones entre normas — y cada persona forma su propia valoración.

Por ejemplo, si detectamos que una reforma incluye artículos no relacionados con su objeto principal, lo señalaremos como dato objetivo. No diremos que hay algo oculto; simplemente que no va en relación con el resto. Cada ciudadano sacará sus conclusiones.

### 2. Accesibilidad universal

La legislación es de todos. Su evolución debería ser visible, comprensible y gratuita. Ley Abierta debe funcionar para cualquier ciudadano, independientemente de su edad, formación o conocimientos técnicos.

### 3. Privacidad mínima

Solo recabamos los datos estrictamente necesarios para dar el servicio. No vendemos datos, no hacemos perfiles, no monetizamos a los usuarios. Si necesitas un email para recibir notificaciones, eso es todo lo que pedimos.

### 4. Código abierto, siempre

Todo el código, los datos y las herramientas de Ley Abierta son públicos. Usamos la licencia AGPL-3.0 para garantizar que cualquier versión derivada mantenga este compromiso. Si alguien construye algo con este código, ese algo también tiene que ser abierto.

### 5. Sin ánimo de lucro

Este proyecto no busca beneficio monetario. Busca hacer un bien a la comunidad. Si aceptamos donaciones o ayudas, será exclusivamente para cubrir costes operativos (servidores, IA), y nunca condicionarán la visión ni la independencia del proyecto.

## Para quién

- **Ciudadanos** que quieren entender qué leyes les afectan sin necesitar un abogado
- **Periodistas** que investigan cambios legislativos y necesitan datos comparables
- **Juristas** que quieren una herramienta abierta para consultar y comparar versiones
- **Investigadores** que estudian la evolución del ordenamiento jurídico
- **Organizaciones civiles** que trabajan en transparencia y rendición de cuentas
- **Desarrolladores** que quieren construir herramientas sobre datos legislativos abiertos

## Hacia dónde vamos

Estas son ideas que nos gustaría explorar:

- **Flujo legislativo completo** — Seguir una ley desde su propuesta hasta su aprobación, con todo el proceso parlamentario visible
- **PRs de leyes** — Visualizar las reformas pendientes como si fueran pull requests: qué cambia, qué se añade, qué se elimina
- **Más jurisdicciones** — El foco actual es España, pero la arquitectura está diseñada para que cualquiera pueda adaptarla a su país

## Cómo contribuir

Ley Abierta es de todos. No hace falta ser desarrollador para contribuir:

- **Juristas**: revisad los resúmenes, señalad errores, sugerid mejores formas de explicar conceptos legales
- **Periodistas**: documentad casos de uso, ayudad a definir qué información es más relevante
- **Ciudadanos**: reportad errores, proponed mejoras, contadnos qué no se entiende
- **Desarrolladores**: mejorad el código, añadid funcionalidades, optimizad el rendimiento
```

---

### Documento B: Caso de Estudio Técnico (`cases/leyabierta`)
*Fuente original:* [alexmj.dev/cases/leyabierta](https://alexmj.dev/cases/leyabierta)

```markdown
# Ley Abierta — 190 years of Spanish law as Git

## The Brief: Public law that's practically inaccessible
Spanish legislation is nominally public: the BOE exposes every law via an XML API. But the raw feed is a stream of documents, not a navigable history. No diff between versions, no graph of which reform amended which article, no way to ask "what changed in labor law in 2018?" and get a useful answer. The official search returns keyword matches in legal prose, not structured documents. Ley Abierta treats the law as source code. Each reform is a commit, each jurisdiction a folder, each version a checkable diff. The result is 190 years of legislation that's queryable and diffable, with full version history.

## Technical Stats
- Status: Live at leyabierta.es (AGPL-3.0)
- Corpus: 12,275 laws, 43,919 commits, 18 jurisdictions (1835–2026)
- Index Optimization: 7.6 GB → 1.4 GB (-81% size) using custom int8 SIMD C kernel
- Search: Hybrid BM25 + Vector Retrieval, RRF Fusion, Qwen LLM rerank & synthesis
- Inference: Hosted Qwen stack on Nan (EU servers, no-log, flat-rate unmetered)

## Key Decisions

### 1. Git as the storage layer
Laws are versioned documents. Git is the right tool: `git log -- Codigo_Penal.md` is a meaningful civic interface, `git diff` between two commits is a legislative audit trail. Pre-1970 laws are clamped to `1970-01-02` with the real publication date preserved in the YAML frontmatter (Git's commit timestamp is Unix-epoch only). The repo is the product.

### 2. Custom int8 SIMD C kernel
Off-the-shelf vector libraries were too slow at 486k vectors on a single VM. A hand-rolled int8 cosine kernel with SIMD intrinsics (dual path AVX2+FMA for x86_64 and NEON for arm64, two-way unrolled accumulator) cut the index from 7.6 GB to 1.4 GB (-81%) and brought the vector-search stage p50 from 2.1s to 0.8s. A `SharedArrayBuffer` worker pool ensures the index lives once in memory across 4 Bun Workers. No GPU and no managed vector DB.

### 3. The BM25 regression that taught the stack
BM25 looked obvious for legal text. The early hybrid (BM25 + RRF over Gemini dense embeddings) actually regressed on real citizen queries: FTS5 OR-expansion on "horas extras que no me pagan" matched noise across centuries of legal prose. The fix wasn't tuning BM25 weights. It was a modern-bias prompt on Qwen embeddings that biases retrieval toward recent statutes over 19th-century codes. A reproducible eval harness against citizen and omnibus question sets caught the regression before it reached prod.

### 4. Full Qwen stack over Gemini + Cohere
The original stack used Gemini embeddings, Gemini Flash Lite as the query analyzer, and Cohere Rerank 4 Pro, all routed through OpenRouter at ~$2 per 1,000 queries with every request leaving the server. A/B evaluation replaced each component with Qwen on Nan, an EU-based inference provider with a no-log policy: embeddings, query analyzer, LLM reranker, and synthesis. Queries stay in the EU, nothing is logged on the inference side.

### 5. Self-hosted Opik per-span RAG tracing
Every `/v1/ask` call creates an Opik trace with spans for `embed_query`, `bm25`, `vector_knn`, `aggregate_pool`, `rrf_fusion`, `rerank`, and `synthesis`. Tracing is fail-safe. Span failures never break the pipeline. Opik runs self-hosted in the same VM (full backend + frontend + Python OSS stack), so per-stage latency is debuggable in prod without exfiltrating queries to a SaaS. The BM25 OR-explosion bug was identified at the `bm25` span before it tanked recall.

### 6. AGPL-3.0
Public legal infrastructure shouldn't be capturable by a SaaS. AGPL guarantees that any deployment that serves Ley Abierta over a network must publish its modifications. The license matches the project's politics: if public law is a commons, so is the engine that makes it searchable.
```
