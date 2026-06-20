# Ledger de Desarrollo y Hoja de Ruta: Leyes.ar

Este documento centraliza la gestión del desarrollo del proyecto **Leyes.ar**, sirviendo como registro de decisiones técnicas, propuestas de mejora, delimitación del alcance y registro de cambios (changelog).

---

## 1. Delimitación del Proyecto (Scope)

Para garantizar la viabilidad técnica e institucional de **Leyes.ar**, definimos claramente qué forma parte del sistema y qué queda fuera de su alcance:

### DENTRO del Alcance (In Scope)
*   **Gestión Estructural y Trazabilidad:** Seguimiento preciso, inmutable y criptográficamente firmado (GPG) de las modificaciones de texto de todo el cuerpo normativo argentino (Nacional, Provincial y Municipal).
*   **Texto Plano Estructured (Markdown + YAML):** Conversión de las normas de formatos analógicos (PDF) a texto plano enriquecido con metadatos estructurados para facilitar la búsqueda, enlazado y procesamiento por IA.
*   **Automatización de Asistencia Técnica (CI/CD):** Procesos automatizados y no vinculantes de análisis sintáctico formal (linters), control de correlatividad y alertas de referencias jurídicas rotas.
*   **Abstracción de Complejidad (UI):** Interfaces visuales que ocultan los comandos de consola de Git para redactores legislativos y ciudadanos.

### FUERA del Alcance (Out of Scope)
*   **Alteración del Derecho de Fondo:** No se modifica el significado sustancial, alcance jurisprudencial ni interpretación dogmática del Código Civil y Comercial, Código Penal o cualquier código legislativo.
*   **Bloqueo del Debate Democrático:** Las alertas de errores formales generadas por automatizaciones (CI/CD) operan como informes de asistencia técnica; el parlamento soberano siempre retiene el poder político de sancionar cualquier norma, incluso con errores de referencias.
*   **Plataforma de Voto Directo:** El sistema registra y co-crea los textos debatidos en comisión, pero no reemplaza las votaciones constitucionales ni las asambleas físicas tradicionales del Congreso o legislaturas.

---

## 2. Registro de Decisiones de Diseño (ADRs)

Documentamos las justificaciones de arquitectura tecnológica seleccionadas para el proyecto:

### ADR-001: Git como Motor en lugar de Base de Datos Relacional (SQL)
*   **Contexto:** Los digestos de leyes tradicionales usan bases de datos SQL centralizadas.
*   **Decisión:** Adoptar **Git** como motor primario de persistencia de textos históricos.
*   **Justificación:** Git provee de forma nativa e inmutable:
    *   Trazabilidad línea por línea (`git blame`).
    *   Auditoría de autoría criptográfica y descentralización nativa para el federalismo argentino (cada provincia puede tener su nodo).
    *   Manejo natural de bifurcaciones (proyectos de ley en debate) y fusiones (sanciones).

### ADR-002: Markdown + YAML Frontmatter como Estándar de Formato
*   **Contexto:** Se requiere estructurar los textos normativos de forma digital.
*   **Decisión:** Adoptar **Markdown** con encabezados **YAML** en lugar de formatos pesados como XML (Akoma Ntoso) en primera fase.
*   **Justificación:** Markdown es de fácil lectura humana (perfecto para juristas) y se integra nativamente en el ecosistema de Git sin generar conflictos de fusión ruidosos. YAML permite agregar metadatos extensibles (id, jurisdicción, fecha) de forma simple y legible.

### ADR-003: Firmas Criptográficas (GPG / Firma Digital Nacional) para Merges
*   **Contexto:** Se debe evitar la adulteración clandestina del Boletín Oficial o del Digesto.
*   **Decisión:** Exigir firma digital obligatoria en los commits de fusión (`merge`) a la rama principal (`main`).
*   **Justificación:** Asegura la fe pública y correspondencia fiel de que el texto consolidado y subido al repositorio coincide con el acta del debate legislativo firmado digitalmente por la autoridad del Digesto (Mesa de Consolidación).

---

## 3. Backlog de Propuestas y Futuras Funcionalidades

Ideas registradas para el desarrollo tecnológico del ecosistema:

*   **[Propuesta #001] Linter Legislativo Argentino (Linter-AR):** Desarrollo de un parser automático que reconozca modismos y jerarquías del derecho de nuestro país (ej. "Derógase", "Modifícase") y verifique automáticamente que el artículo citado exista.
*   **[Propuesta #002] Editor Web Soberano (WYSIWYG):** Interfaz visual tipo procesador de textos donde el legislador redacta y que, por detrás, genera automáticamente commits y ramas en Git sin exponer la consola de comandos.
*   **[Propuesta #003] API RAG-Ready:** Exportación del repositorio a una base de datos vectorial de forma continua para que cualquier IA del ámbito público o privado consulte leyes vigentes sin alucinaciones de OCR o PDFs.

---

## 4. Registro de Correcciones y Mejoras (Changelog)

Historial consolidado de la evolución metodológica del proyecto:

*   **Fase 1 (Consolidación Inicial):**
    *   Creación del ecosistema conceptual en Markdown.
    *   Redacción de los 9 escenarios prácticos de uso gubernamental.
    *   Definición de las 5 fases de despliegue estratégico.
*   **Fase 2 (Alineación Rioplatense y Glosario):**
    *   Corrección lingüística al español de la Argentina (remoción de modismos extranjeros).
    *   Inserción sistemática de glosarios y aclaraciones sencillas entre paréntesis para todos los tecnicismos informáticos y de Git.
*   **Fase 3 (Delimitación Conceptual Jurídica):**
    *   Aclaración explícita sobre la inalterabilidad sustancial de los Códigos de fondo tradicionales.
    *   Desarrollo conceptual de la convergencia de la ley como algoritmo estructural.
*   **Fase 4 (Limpieza del Historial):**
    *   Consolidación y squashing de 15 commits en un único commit inicial limpio para mayor claridad en el repositorio público.
*   **Fase 5 (Reestructuración Optimizada):**
    *   Reorganización física de los documentos en carpetas conceptuales (`1_estrategia`, `2_tecnica_y_casos`, `3_comunicacion`).
    *   Creación del Ledger de Desarrollo (`ROADMAP.md`).
*   **Fase 6 (Ajuste Terminológico Estratégico):**
    *   Reemplazo de la expresión "Derecho como Código" por "Codificación digital de la legislación" en todo el corpus documental para mitigar resistencia semántica y alinear el proyecto con la tradición jurídica continental.
*   **Fase 7 (Alineación Constitucional y Mitigación de Sesgo Técnico):**
    *   Corrección conceptual en los documentos para desvincular los términos técnicos de Git (Merge, Pull Request) de los actos constitucionales soberanos (Sanción, Promulgación).
    *   Remoción de analogías de programación informales ("bugs", "variables y subrutinas") reemplazándolas por conceptos de la ciencia jurídica formal (antinomias, lagunas normativas, sistemas de reglas interconectadas).
