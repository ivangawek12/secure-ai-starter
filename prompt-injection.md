# Prompt injection

Prompt injection ocurre cuando contenido controlado por un atacante contiene instrucciones que intentan influir en el comportamiento de un LLM o agente.

## Directa

El usuario intenta cambiar las instrucciones del sistema.

## Indirecta

El usuario pide al agente que procese contenido externo y ese contenido contiene instrucciones.

```text
usuario → agente → web → contenido malicioso → agente
```

### Defensa

No asumir que el modelo distinguirá perfectamente entre una instrucción legítima y una instrucción encontrada dentro de contenido externo.

La defensa más fuerte es limitar las capacidades disponibles cuando se procesa contenido no confiable.
