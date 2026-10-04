# Checklist: antes de instalar una skill

## Provenance
- [ ] ¿Quién mantiene la skill?
- [ ] ¿El repositorio tiene historial y actividad razonables?
- [ ] ¿El código puede inspeccionarse?
- [ ] ¿Las dependencias están identificadas?

## Instructions
- [ ] ¿La `SKILL.md` contiene instrucciones que intentan modificar las reglas del agente?
- [ ] ¿Pide ignorar instrucciones anteriores?
- [ ] ¿Pide secretos, tokens o credenciales?
- [ ] ¿Pide ejecutar comandos que no son necesarios?
- [ ] ¿Usa texto oculto, Unicode extraño o instrucciones codificadas?

## Capabilities
- [ ] ¿Qué tools necesita?
- [ ] ¿Qué tools intenta habilitar?
- [ ] ¿Tiene acceso a filesystem, shell, browser, email o red?
- [ ] ¿Puede escribir o borrar información?

## Decision
- [ ] Instalar solamente con permisos mínimos.
- [ ] Probar primero en un entorno aislado.
- [ ] Revocar capacidades que no sean necesarias.
