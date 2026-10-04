# Capability security

El modelo produce decisiones y texto. Las tools convierten esas decisiones en capacidades operativas.

Por eso una arquitectura segura debería controlar:

```text
MODEL
  ↓
POLICY
  ↓
TOOLS
  ↓
PERMISSIONS
  ↓
SIDE EFFECTS
```

El prompt puede expresar una política, pero los permisos técnicos deben reforzarla.
