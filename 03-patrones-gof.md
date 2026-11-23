# 3. Patrones de diseño según GoF

**GoF** (*Gang of Four*): Erich Gamma, Richard Helm, Ralph Johnson y John Vlissides. Libro: *Design Patterns: Elements of Reusable Object-Oriented Software* (1994).

Catalogaron **23 patrones** en 3 familias.

---

## 3.1 Patrones frecuentes

### Strategy

**Problema:** varias formas de hacer lo mismo; los `if/switch` crecen.  
**Idea:** encapsular cada algoritmo e intercambiarlos.

```ts
interface Descuento {
  aplicar(precio: number): number;
}

class Vip implements Descuento {
  aplicar(p: number) { return p * 0.8; }
}
class Empleado implements Descuento {
  aplicar(p: number) { return p * 0.7; }
}

function cobrar(precio: number, d: Descuento) {
  return d.aplicar(precio);
}

cobrar(100, new Vip()); // 80
```

### Factory Method

**Problema:** el cliente no debe saber qué clase concreta crear.  
**Idea (GoF):** una clase *Creator* define un método de fabricación; las **subclases** deciden el producto.

```ts
interface Notificador { enviar(msg: string): void; }
class EmailNotificador implements Notificador {
  enviar(msg: string) { /* SMTP... */ }
}
class SmsNotificador implements Notificador {
  enviar(msg: string) { /* SMS... */ }
}

abstract class ServicioAviso {
  abstract crearNotificador(): Notificador; // Factory Method
  avisar(msg: string) {
    this.crearNotificador().enviar(msg);
  }
}

class ServicioEmail extends ServicioAviso {
  crearNotificador() { return new EmailNotificador(); }
}

new ServicioEmail().avisar("Pedido confirmado");
```

> Una función `crearX(...)` con `if/switch` es **Simple Factory** (muy usada), pero **no** es el Factory Method GoF.

### Adapter

**Problema:** tienes una interfaz A; tu código espera B.  
**Idea:** un adaptador traduce A → B.

> Distinto del “adaptador” de Hexagonal (§1.2): aquí es el patrón GoF de traducción de interfaces; allí es el componente del borde (HTTP, SQL…).

```ts
class PagoLegacy {
  cobrarEnCentavos(cents: number) { /* ... */ }
}

interface PasarelaPago {
  cobrar(monto: number): void;
}

class AdaptadorLegacy implements PasarelaPago {
  constructor(private legacy: PagoLegacy) {}
  cobrar(monto: number) {
    this.legacy.cobrarEnCentavos(Math.round(monto * 100));
  }
}
```

### Observer

**Problema:** varios componentes deben reaccionar a un cambio.  
**Idea:** el sujeto notifica a observadores suscritos.

```ts
type Listener = (dato: string) => void;

class Pedido {
  private listeners: Listener[] = [];
  suscribir(l: Listener) { this.listeners.push(l); }
  cambiarEstado(e: string) {
    this.listeners.forEach(l => l(e));
  }
}

const p = new Pedido();
p.suscribir(e => console.log("Email:", e));
p.suscribir(e => console.log("SMS:", e));
p.cambiarEstado("enviado");
```

### Decorator

**Problema:** añadir responsabilidades sin mil subclases.  
**Idea:** envolver el objeto con capas.

```ts
interface Cafe { costo(): number; }

class CafeSimple implements Cafe {
  costo() { return 5; }
}

class ConLeche implements Cafe {
  constructor(private base: Cafe) {}
  costo() { return this.base.costo() + 1.5; }
}

const cafe = new ConLeche(new CafeSimple()); // 6.5
```

### Facade

**Problema:** un subsistema complejo.  
**Idea:** una fachada simple que orquesta por detrás.

```ts
class RegistroFacade {
  registrar(email: string, pass: string) {
    // valida → guarda usuario → envía email de bienvenida
  }
}
```

---

## 3.2 Otros patrones GoF

### Creacionales

| Patrón | Definición |
|--------|--------------|
| **Singleton** | Una sola instancia compartida (usar con cuidado: complica tests) |
| **Builder** | Construir un objeto complejo paso a paso |
| **Abstract Factory** | Familias de objetos relacionados sin fijar clases concretas |
| **Prototype** | Crear objetos clonando un prototipo |

### Estructurales

| Patrón | Definición |
|--------|--------------|
| **Proxy** | Sustituto que controla el acceso a otro objeto |
| **Composite** | Árboles parte-todo (tratar hoja y compuesto igual) |
| **Bridge** | Separar abstracción de su implementación |
| **Flyweight** | Compartir estado intrínseco para ahorrar memoria |

### Comportamiento

| Patrón | Definición |
|--------|--------------|
| **Command** | Encapsular una petición como objeto (`ejecutar`, a veces `deshacer`) |
| **Template Method** | Esqueleto de algoritmo en la base; pasos en las hijas |
| **State** | El comportamiento cambia según el estado interno |
| **Chain of Responsibility** | Cadena de manejadores hasta que uno atiende |
| **Mediator** | Objeto central que coordina colegas sin acoplarlos |
| **Memento** | Guardar y restaurar estado interno |
| **Iterator** | Recorrer una colección sin exponer su estructura |
| **Visitor** | Nueva operación sobre una estructura sin cambiar las clases |
| **Interpreter** | Representar y evaluar una gramática simple |

---

## 3.3 Mapa de los 23

| Familia | Patrones |
|---------|----------|
| Creacional | Abstract Factory, Builder, Factory Method, Prototype, Singleton |
| Estructural | Adapter, Bridge, Composite, Decorator, Facade, Flyweight, Proxy |
| Comportamiento | Chain of Responsibility, Command, Interpreter, Iterator, Mediator, Memento, Observer, State, Strategy, Template Method, Visitor |

## 3.4 Observación

Implementar un patrón **antes** de sentir el dolor suele sobrar. Si el `if` es claro y estable, déjalo. El patrón justifica su complejidad cuando el cambio es frecuente o el acoplamiento duele.

---

**Anterior:** [02-patrones-de-diseno.md](02-patrones-de-diseno.md) · **Siguiente:** [04-otros-patrones.md](04-otros-patrones.md)
