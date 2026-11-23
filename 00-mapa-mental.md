<p align="center">
  <img src="assets/pascual-bravo.png" alt="Pascual Bravo" width="320" />
</p>

<p align="center"><strong>Nociones patrones arquitectónicos y diseño</strong> · Pascual Bravo · Versión 2026-II</p>

# 0. Mapa mental — antes de empezar

Panorama de los temas, antes de profundizar.

## Vista general

```mermaid
mindmap
  root((Arquitectura<br/>de Software<br/>disciplina))
    Patrones arquitectónicos
      MVC
        Presentación
      Hexagonal
        Ports
        Adapters
      Onion
        Domain al centro
      Clean
        Entities
        Use Cases
        Adapters
      Cuándo usar cada uno
        MVC → UI / CRUD
        Hex → integraciones
        Onion → dominio rico
        Clean → longevidad / tests
    Patrones de diseño
      Qué son / qué no son
      SOLID
      GoF
        Creacionales
        Estructurales
        Comportamiento
      Otros / PoEAA
        Repository
        DTO
        Unit of Work
        ACL
        CQRS
      SOLID / DIP
      DI (técnica)
    Comparaciones
      Arquitectónico vs diseño
      MVC vs Clean
      Hex Onion Clean — idea común
```

## Orden de estudio

```mermaid
flowchart LR
  A[Panorama] --> B[MVC Hex Onion Clean]
  B --> C[Qué es un patrón de diseño]
  C --> D[GoF]
  D --> E[Repository DI DTO…]
  E --> F[Comparar y decidir]
  F --> G[Cheatsheet]
```

## Relación entre conceptos

```mermaid
flowchart TB
  Disc[Arquitectura de software<br/>disciplina]
  Disc --> PA[Patrones arquitectónicos<br/>MVC · Hex · Onion · Clean]
  Disc --> PD[Patrones de diseño GoF]
  Disc --> PoEAA[Patrones de aplicación<br/>Repository · DTO…]
  PD -.->|se usan dentro| PA
  PoEAA -.->|se usan dentro| PA
```

Primero se parte la aplicación (patrón arquitectónico). Después, dentro de esas capas, se resuelven problemas locales con patrones de diseño y de aplicación.

---

**Siguiente →** [01-patrones-arquitectonicos.md](01-patrones-arquitectonicos.md)
