# Baseline de seguridad para usar LLMs

## Nivel 0 — Chat
- No ingresar secretos innecesarios.
- Verificar hechos importantes.
- No tratar al modelo como autoridad.

## Nivel 1 — Archivos / RAG
- Tratar documentos como contenido no confiable.
- Separar instrucciones de datos.
- Registrar provenance.
- Evitar que documentos puedan activar acciones automáticamente.

## Nivel 2 — Tools
- Least privilege.
- Allowlists.
- Separación read/write.
- Egress control.
- Logs.

## Nivel 3 — Agent
- Sandbox.
- Human approval para acciones de alto impacto.
- Secrets aislados.
- Límites de filesystem/network.
- Kill switch.
