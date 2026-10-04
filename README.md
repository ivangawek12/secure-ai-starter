Secure AI — guía práctica para usar IA/LLMs de forma segura
> **Don't secure the prompt. Secure the capabilities.**
Una guía abierta y práctica para personas que usan ChatGPT, Claude, Gemini, Codex, agentes, MCPs, RAG, skills y otras herramientas de IA sin necesidad de ser especialistas en seguridad.
La idea central es simple: un modelo puede ser influenciado por contenido que procesa; por eso la seguridad no puede depender únicamente de un prompt de sistema. Hay que controlar las capacidades que rodean al modelo.
¿Por qué existe este proyecto?
Los LLM dejaron de ser solamente interfaces de texto. Hoy pueden leer documentos, navegar sitios, consultar correo, acceder a archivos, ejecutar código, llamar APIs y modificar sistemas.
Eso crea una nueva superficie de ataque:
```text
contenido no confiable
        │
        ▼
      LLM / AGENTE
        │
   ┌────┴────┐
   ▼         ▼
datos      acciones
privados   externas
```
<img width="728" height="632" alt="image" src="https://github.com/user-attachments/assets/b84892e0-885c-41fa-bb5b-c55c258a62fe" />


Una instrucción maliciosa escondida en una página, email, PDF, repositorio, issue, documento o `SKILL.md` puede intentar modificar el comportamiento del agente.
El problema se vuelve especialmente serio cuando el mismo agente puede leer información privada y comunicarse externamente.
Las amenazas que cubre
Amenaza	Qué significa	Primera defensa
Prompt injection	Contenido intenta convertirse en instrucciones	Separar datos de instrucciones; validar entradas
Indirect prompt injection	La instrucción llega desde web, email, PDF, etc.	Tratar contenido externo como no confiable
Skill poisoning	Una skill contiene instrucciones o capacidades maliciosas	Auditar skills antes de instalarlas
LLM seeding	Contenido público intenta sesgar futuras respuestas de LLMs	Provenance + múltiples fuentes + verificación
Memory poisoning	Información maliciosa/falsa se vuelve persistente	Validación y límites de confianza
Tool abuse	El agente usa una herramienta de forma peligrosa	Least privilege + allowlists
Data exfiltration	Datos privados salen hacia un tercero	Control de egress
Context pollution	El contexto contiene instrucciones irrelevantes o maliciosas	Sanitización y separación de contexto
Supply-chain risk	Skill/MCP/dependencia de terceros comprometida	Provenance, revisión y versionado
Cognitive over-reliance	Delegar demasiado juicio al modelo	Mantener verificación y agencia humana
El modelo mental: la "lethal trifecta"
Simon Willison describe una combinación especialmente peligrosa de tres capacidades:
Acceso a datos privados
Exposición a contenido no confiable
Capacidad de comunicación externa / side effects
Cuando las tres aparecen juntas, un atacante puede intentar inducir al agente a leer datos privados y enviarlos fuera del sistema.
```text
             CONTENIDO NO CONFIABLE
                       │
                       ▼
                    AGENTE
                   /      \
                  /        \
                 ▼          ▼
        DATOS PRIVADOS   ACCIÓN EXTERNA
```
Regla práctica: si no necesitás una capacidad, no la expongas.
Antes de instalar una skill o conectar un MCP
Hacete estas 10 preguntas:
[ ] ¿Quién creó esto?
[ ] ¿Puedo inspeccionar el código/configuración?
[ ] ¿Qué datos puede leer?
[ ] ¿Qué herramientas expone?
[ ] ¿Puede escribir, borrar o ejecutar comandos?
[ ] ¿Puede hacer requests hacia Internet?
[ ] ¿Puede acceder a secretos o credenciales?
[ ] ¿Procesará contenido controlado por terceros?
[ ] ¿Qué ocurre si el agente interpreta una instrucción maliciosa?
[ ] ¿Puedo limitar sus permisos a una sola tarea?
Si no podés responder estas preguntas, no conectes la herramienta a información sensible todavía.
Arquitectura recomendada
```text
             ┌─────────────────────┐
             │  UNTRUSTED INPUT    │
             │ web / email / PDF   │
             │ GitHub / RAG / RSS  │
             └──────────┬──────────┘
                        │
                        ▼
             ┌─────────────────────┐
             │   SEEDING GATE      │
             │                     │
             │ injection scan      │
             │ hidden chars        │
             │ provenance          │
             │ skill audit         │
             └──────────┬──────────┘
                        │
                  trusted / blocked
                        │
                        ▼
             ┌─────────────────────┐
             │       AGENT         │
             │                     │
             │ constrained context │
             │ least privilege     │
             │ explicit policy     │
             └──────────┬──────────┘
                        │
              ┌─────────┴─────────┐
              ▼                   ▼
        READ CAPABILITIES    WRITE / ACTIONS
              │                   │
              └─────────┬─────────┘
                        ▼
               HUMAN APPROVAL
               for high impact
```
Principios
1. No confíes en el prompt como frontera de seguridad
Un prompt puede decir `no reveles secretos`. Eso no reemplaza una restricción técnica que impida leer o enviar esos secretos.
2. Least privilege
Exponé solamente las capacidades necesarias para la tarea.
```text
Necesito resumir RSS
        │
        └──► get_news()       ALLOW

        ├──► read_gmail()     DENY
        ├──► read_drive()     DENY
        ├──► send_email()     DENY
        └──► delete_file()    DENY
```
3. Separá lectura de escritura
Una herramienta que consulta información no debería tener automáticamente permisos para modificarla.
4. Tratá todo contenido externo como no confiable
Una página web puede contener texto. Un email puede contener texto. Un PDF puede contener texto. Un issue de GitHub puede contener texto. Ninguno de ellos debería convertirse automáticamente en una instrucción de alta prioridad para el agente.
5. Limitá el blast radius
No siempre podés impedir que un modelo sea manipulado. Sí podés hacer que una manipulación tenga consecuencias pequeñas.
6. Human-in-the-loop para acciones sensibles
Enviar emails, borrar archivos, publicar contenido, transferir información, modificar bases de datos o ejecutar comandos debería requerir controles adicionales.
Skills de seguridad
El repositorio está pensado para incorporar posteriormente skills reutilizables para:
revisión de prompt injection;
auditoría de `SKILL.md`;
detección de memory poisoning;
revisión de retrieval/RAG;
revisión de permisos de tools;
detección de exfiltración;
revisión de cadenas de herramientas;
análisis de secretos;
evaluación de supply chain.
Importante: una skill de seguridad también debe tratarse como software no confiable hasta haber sido revisada.
Seeding: dos problemas diferentes
En este proyecto usamos "seeding" en un sentido amplio para dos escenarios relacionados pero distintos.
A. Seeding de instrucciones dentro del entorno del agente
Ejemplos:
```text
SKILL.md
README.md
PDF
email
web page
GitHub issue
RAG document
```
El contenido puede intentar introducir instrucciones maliciosas en el contexto: LLM seeding / contaminación del conocimiento público. Una operación puede producir grandes cantidades de contenido diseñado para influir en las respuestas futuras de sistemas que recuperan información de la web. Esto es parecido al SEO, pero orientado a modelos y sistemas de respuesta.

La defensa no consiste en asumir que todo contenido es falso. Consiste en aumentar la provenance, comparar fuentes independientes y evitar que una única fuente se convierta en autoridad implícita.

Soberanía cognitiva
La seguridad de IA no es solamente una cuestión técnica: Una persona también puede delegar progresivamente su juicio, memoria, escritura, búsqueda y evaluación en un sistema automático. Por eso este repositorio incluye una capa de autonomía humana:
```text
IA como prótesis       ✓
IA como autoridad      ✗

asistir el juicio      ✓
reemplazar el juicio   ✗

verificar              ✓
aceptar automáticamente ✗
```
El objetivo no es usar menos IA. Es usar IA sin perder capacidad de decidir cuándo confiar en ella.
Qué NO promete este repositorio
No existe una defensa perfecta contra prompt injection.
Un scanner no garantiza que una skill sea segura.
Un LLM no debe considerarse una frontera de seguridad.
Un MCP no es seguro simplemente porque sea open source.
Un resultado generado por IA no es automáticamente una fuente confiable.
La meta es reducir riesgo mediante arquitectura, permisos, verificación y límites.
Estructura
```text
secure-ai/
├── README.md
├── LICENSE
├── SECURITY.md
├── CONTRIBUTING.md
│
├── checklists/
│   ├── before-installing-a-skill.md
│   ├── before-connecting-an-mcp.md
│   ├── llm-security-baseline.md
│   └── agent-security-review.md
│
├── docs/
│   ├── prompt-injection.md
│   ├── skill-seeding.md
│   ├── llm-seeding.md
│   ├── memory-poisoning.md
│   ├── lethal-trifecta.md
│   ├── capability-security.md
│   └── cognitive-autonomy.md
│
├── skills/
│   ├── prompt-injection-review/
│   ├── skill-security-audit/
│   ├── memory-poisoning-review/
│   ├── retrieval-security-review/
│   └── tool-permission-review/
│
├── examples/
│   ├── malicious-skill/
│   ├── indirect-prompt-injection/
│   └── safe-agent/
│
└── resources/
    └── sources.md
```
<h4>Fuentes y lecturas recomendadas</h4>

<small>

### Seguridad técnica

Simon Willison — [The lethal trifecta for AI agents](https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/)  
Ivan Gawek — [Secure MCPs for Secure Artifacts](https://github.com/ivangawek12/secure-mcps-for-secure-artifacts)

### Seeding e influencia

Nada Respetable — [LLM seeding como arma de guerra](https://nadarespetable.com/llm-seeding-como-arma-de-guerra/)

### Autonomía y uso humano de IA

421.news — [El futuro de la IA: Entre doomers y hyperos](https://www.421.news/es/ia-doomers-hyperos/)  
421.news — [Cómo proteger tu cerebro del algoritmo](https://www.421.news/es/como-proteger-cerebro-algoritmo-soberania-cognitiva-bioquimica/)  
421.news — [El rol de la autonomía humana en la era de los LLM](https://www.421.news/es/ia-soberania-cognitiva-low-tech-high-life/)  
421.news — [El caso Anthropic](https://www.421.news/es/filosofia-anthropic-ia-etica/)  
Nada Respetable — [Psicosis inducida por inteligencia artificial](https://nadarespetable.com/psicosis-inducida-por-inteligencia-artificial/)

### Regulaciones y etc

Marcel Pallero- https://open.spotify.com/episode/6lwyPMlZWJBLCYnKBcpMZR

### Licencia

Este proyecto puede publicarse bajo MIT para el material propio, manteniendo las atribuciones y licencias correspondientes de cualquier contenido o código de terceros.

</small>
---
Idea central:
> No intentes hacer que el modelo sea perfecto.
> Diseñá el sistema para que un modelo imperfecto no pueda hacer demasiado daño.
