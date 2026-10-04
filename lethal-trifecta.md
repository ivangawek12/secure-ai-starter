# Lethal trifecta

La combinación de:

1. acceso a datos privados;
2. exposición a contenido no confiable;
3. comunicación externa o side effects.

crea una ruta potencial de exfiltración.

La estrategia de diseño más simple es romper el triángulo.

```text
PRIVATE DATA ─────────────┐
                         │
UNTRUSTED CONTENT ───────┼──► riesgo elevado
                         │
EXTERNAL COMMUNICATION ──┘
```

No hace falta eliminar las tres capacidades del sistema completo. Muchas veces alcanza con impedir que un mismo agente las combine sin controles.
