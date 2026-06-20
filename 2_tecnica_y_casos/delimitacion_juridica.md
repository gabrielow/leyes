# Delimitación Jurídica y Compatibilidad Tecnológica: Leyes.ar

Este documento analiza el marco legal, doctrinario y normativo de la República Argentina aplicable a la adopción de **Leyes.ar**, fundamentando la validez del control de versiones criptográfico frente a la tradición jurídica nacional.

---

## 1. Preservación del Derecho de Fondo y los Códigos Sustanciales

Es una premisa jurídica fundamental de **Leyes.ar** que la adopción de herramientas informáticas no altera en absoluto la naturaleza constitucional del derecho.

*   **Inalterabilidad de los Códigos Clásicos:** Cuerpos jurídicos fundamentales como el **Código Civil y Comercial de la Nación**, el **Código Penal**, el **Código Aduanero** o el **Código de Minería** conservan plenamente su jerarquía, significado doctrinal, jurisprudencia acumulada y hermenéutica de interpretación clásica.
*   **Finalidad Instrumental:** El sistema no interviene en la valoración ética ni política de la ley. Su único propósito es reordenar científicamente la *estructura física del texto* y el *seguimiento temporal de sus enmiendas* para erradicar la inseguridad jurídica formal (referencias rotas, fe de erratas dudosas o dispersión de enmiendas).

---

## 2. La Ley como Algoritmo Lógico-Estructural

El concepto de "Derecho como Código" (*Law as Code*) o la interpretación de la legislación como un algoritmo no pretende suplantar el criterio y la prudencia de los jueces, sino reconocer la estructura lógica intrínseca de la norma:

### Lógica Condicional Preexistente
Desde la escuela de la teoría general del derecho (Kelsen, Cossio), la norma jurídica se ha definido como un juicio hipotético de estructura condicional:

$$\text{Si ocurre el Supuesto de Hecho (Hecho Imponible/Infracción)} \longrightarrow \text{Se aplica la Consecuencia Jurídica (Sanción/Efecto)}$$

Esta estructura es idéntica a una instrucción condicional en programación (`if-then-else`). Al mapear esta lógica a herramientas como Git, el corpus legal se vuelve legible para sistemas informáticos sin perder su valor interpretativo humano.

### Validación Sintáctica y Formal (Linters Jurídicos)
La interpretación del texto como algoritmo permite ejecutar validaciones automatizadas (linters) con un enfoque netamente técnico y consultivo:
*   **Coherencia de referencias:** El validador detecta automáticamente si un proyecto de ley cita un artículo derogado del Código Civil y Comercial.
*   **Consistencia sintáctica:** Detecta errores formales de numeración o vacíos de redacción formales.
*   **Poder Político Inalterado:** Estas pruebas actúan como informes técnicos. El cuerpo legislativo soberano siempre mantiene la facultad de sancionar una norma por encima de las alertas informáticas, respetando el principio constitucional de soberanía legislativa.

---

## 3. Compatibilidad con la Ley de Firma Digital N° 25.506

La seguridad y autenticidad del digesto digital de **Leyes.ar** se fundamenta en la criptografía asimétrica utilizada por Git, la cual encaja perfectamente en el marco de la **Ley Nacional de Firma Digital N° 25.506** y sus modificatorias:

### Firma Digital vs. Firma Electrónica en Git
La legislación argentina distingue dos categorías con efectos jurídicos diferenciados:

1.  **Firma Digital (Art. 2):** Requiere un certificado digital emitido por un certificador licenciado por el Estado (ONTI / Autoridad de Aplicación). Cuenta con presunciones legales de *autoría* (se presume que pertenece al firmante) e *integridad* (se presume que el documento no fue alterado tras la firma).
2.  **Firma Electrónica (Art. 5):** Carece de alguna de estas presunciones legales. En caso de ser desconocida, la carga de la prueba sobre su validez recae en quien la invoca.

### El Rol de la Firma GPG en Git
Git utiliza firmas criptográficas **GPG** (GNU Privacy Guard) para validar la autoría de cada commit (registro de cambio) y merge (fusión de ramas):

*   **Aplicación Práctica:** El cuerpo técnico del digesto (Consolidadores Técnicos autorizados) asocia su firma GPG a cada enmienda integrada a la rama principal (`main`).
*   **Encuadre Legal:**
    *   Si las llaves GPG de los Consolidadores se emiten a través de los sistemas oficiales del Estado (ej. usando los tokens de la Firma Digital de la Administración Pública Nacional), los merges firmados en Git adquieren el rango de **Firma Digital plena** con valor legal y fe pública técnica absoluta.
    *   Si se utilizan llaves GPG independientes del circuito de licencias estatales, califican legalmente como **Firma Electrónica robusta**, garantizando la inmutabilidad matemática del repositorio y la trazabilidad de auditoría interna de forma inviolable.
*   **Efecto de Seguridad:** Nadie puede inyectar un cambio de redacción en el texto de una ley consolidada sin que el sistema detecte la falta de firma autorizada, neutralizando alteraciones maliciosas o "fe de erratas" informales en el Boletín Oficial.
