# 4. Otros patrones muy usados (más allá de GoF)

Patrones de **grano fino** fuera de GoF (varios aparecen en *PoEAA* de Fowler). Se usan **dentro** de MVC/Hexagonal/Onion/Clean; no los reemplazan.

---

## 4.1 Repository, DI, DTO y Application Service

### Repository

**Problema:** el dominio no debe conocer SQL, ORM ni Mongo.  
**Idea:** interfaz tipo “colección”; la implementación habla con la BD.

```ts
interface UsuarioRepository {
  porId(id: string): Promise<Usuario | null>;
  guardar(u: Usuario): Promise<void>;
}

class UsuarioRepoPg implements UsuarioRepository {
  async porId(id: string) { /* SELECT ... */ return null; }
  async guardar(u: Usuario) { /* INSERT/UPDATE ... */ }
}
```

### DI y DIP

| Sigla | Qué es |
|-------|--------|
| **DIP** | Principio SOLID: depender de abstracciones |
| **DI** | Técnica: inyectar la dependencia (p. ej. por constructor) |

DI no es patrón GoF; ayuda a cumplir DIP.

```ts
class CrearPedido {
  constructor(private pedidos: PedidoRepository) {} // DI
  async ejecutar(datos: NuevoPedido) {
    const p = Pedido.crear(datos);
    await this.pedidos.guardar(p); // DIP: interfaz, no Postgres
  }
}
```

### DTO (Data Transfer Object)

**Problema:** no exponer entidades internas en el borde (API).  
**Idea:** objetos planos para transporte; el dominio usa entidades.

```ts
type CrearUsuarioDto = { email: string; nombre: string };

class Usuario {
  constructor(
    public readonly id: string,
    public email: string,
    public nombre: string
  ) {}
}
```

### Application Service / Caso de uso

Mismo **rol** de orquestación: en Onion suele llamarse *Application Service*; en Clean, *Use Case* / *Interactor*.

```ts
class TransferirDinero {
  constructor(
    private cuentas: CuentaRepository,
    private uow: UnitOfWork
  ) {}
  async ejecutar(from: string, to: string, monto: number) {
    const a = await this.cuentas.porId(from);
    const b = await this.cuentas.porId(to);
    a.debitar(monto);
    b.acreditar(monto);
    await this.uow.commit();
  }
}
```

---

## 4.2 Otros patrones

| Patrón / técnica | Definición |
|------------------|--------------|
| **Unit of Work** | Agrupar escrituras y confirmarlas juntas (transacción) |
| **Mapper** | Traducir formas de datos (DTO ↔ entidad, fila ↔ objeto) |
| **ACL** (DDD) | Barrera ante un sistema externo; el dominio no habla el modelo ajeno |
| **CQRS** | Separar comandos (escriben) de consultas (leen); no para todo CRUD |
| **Domain Events** | Publicar hechos de negocio; otros módulos reaccionan sin acoplarse |
| **Null Object** | Objeto “vacío” con la misma interfaz para evitar `null` por todos lados |
| **Outbox** | Patrón de mensajería: guardar el evento en la misma transacción de BD y publicarlo después |

---

## 4.3 De GoF al día a día

| Si conoces… | En la industria suele verse como… |
|-------------|-----------------------------------|
| Observer | Event bus, webhooks, reactive streams |
| Strategy | Policies, plugins |
| Adapter / Facade | Integraciones, SDKs, BFF |
| Factory Method / Simple Factory | Factories de clientes HTTP |

## 4.4 Checklist

- Persistencia → **Repository** (+ Unit of Work si hay transacciones)  
- Tests sin BD → **DI** + interfaces  
- API HTTP → **DTO** en el borde  
- Flujo de negocio → **Application Service / Use Case**  
- Sistema externo raro → **ACL** (+ Mapper)

---

**Anterior:** [03-patrones-gof.md](03-patrones-gof.md) · **Siguiente:** [05-comparaciones.md](05-comparaciones.md)
