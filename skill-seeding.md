# Skill seeding / skill poisoning

Una skill puede parecer documentación, pero en un sistema agentic también puede modificar el comportamiento del agente al entrar en su contexto.

Por eso una `SKILL.md` debe tratarse como un artefacto de software y de seguridad.

## Señales de alerta

- instrucciones para ignorar reglas anteriores;
- solicitudes de secretos;
- acceso a herramientas que no corresponden con la tarea;
- ejecución de comandos inesperados;
- llamadas HTTP innecesarias;
- instrucciones ocultas o codificadas;
- cambios de configuración sin aprobación;
- instrucciones que intentan persistir información maliciosa.

## Regla

> Una skill de seguridad no es segura solamente porque se llame "security".
