# Deep Modules

Aus „A Philosophy of Software Design":

**Deep Module** = kleines Interface + viel Implementation

```
┌─────────────────────┐
│   Kleines Interface │  ← Wenige Methoden, einfache Parameter
├─────────────────────┤
│                     │
│                     │
│  Tiefe Implementation│  ← Komplexe Logik versteckt
│                     │
│                     │
└─────────────────────┘
```

**Shallow Module** = großes Interface + wenig Implementation (vermeiden)

```
┌─────────────────────────────────┐
│       Großes Interface          │  ← Viele Methoden, komplexe Parameter
├─────────────────────────────────┤
│  Dünne Implementation           │  ← Leitet nur durch
└─────────────────────────────────┘
```

Beim Designen von Interfaces fragen:

- Kann ich die Anzahl der Methoden reduzieren?
- Kann ich die Parameter vereinfachen?
- Kann ich mehr Komplexität dahinter verbergen?
