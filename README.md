# Trabajo Práctico - Sprint 3: La Tienda

---

## 📌 Antes de arrancar

Lo que vimos en el sprint: **arquitectura de carpetas, `useContext`, React Hook Form y deploy en Netlify**.

**Hay un solo TP.**

### ⚠️ Este TP es la tercera entrega, así que subo un poco la vara

Dos cosas que cambian respecto al TP2, y las digo de frente para que nadie se sorprenda en la Review:

1. **Voy a mirar el código, no solo que funcione.** En el Sprint 2 alcanzaba con que la app hiciera lo que pedía. Ahora hay criterios de **cómo** está hecho, y son medibles: los podés chequear vos antes de entregar.
2. **En la defensa te voy a pedir un cambio en vivo** y te voy a preguntar por líneas puntuales. Si hay código en tu TP que no podés explicar, **sacalo antes de entregar**. Un TP más simple y entendido vale más que uno lleno de cosas que no sabés qué hacen.

> 🎯 **Pero tranquilos: el TP es más corto de lo que parece.** Reusás tu catálogo, tu `useLocalStorage` y varios componentes del TP2. Lo nuevo son las cantidades, los contextos y el formulario.

### 📂 De dónde partís

**Repo nuevo**, copiando de tu TP2:

| Copiá | Por qué |
|---|---|
| `src/data/items.js` | Tu catálogo. Le vas a agregar dos campos |
| `src/hooks/useLocalStorage.js` | **Sin tocarle una línea.** Es la prueba de que quedó genérico |
| Tus componentes de card, lista, navbar, modal | Los vas a adaptar, no rehacer |

**¿Por qué repo nuevo y no seguir el del TP2?** Porque el historial de commits del TP3 tiene que contar la historia del TP3. Y porque tu TP2 ya está corregido: si lo seguís tocando, se pierde la foto de lo que entregaste.

---

## 🎯 Qué vas a construir

Una **tienda con carrito y checkout**. El usuario recorre el catálogo, agrega productos, **ajusta cantidades**, ve el total, pasa a una pantalla de checkout, completa un formulario con validaciones, confirma, y ve la confirmación de su pedido.

### 🎬 El tema es el mismo del TP2

Tu watchlist se convierte en tienda. Si eran películas, ahora vendés entradas o el Blu-ray. Si eran libros, es una librería. Si eran recetas, vendés los ingredientes.

**Lo único que necesitás es que los items tengan precio.** Si tu tema no lo soporta (por ejemplo "lugares para visitar"), cambialo por algo que se venda: pasajes, excursiones, lo que sea.

### 🆕 Los campos nuevos del catálogo

```js
// src/data/productos.js
export const productos = [
  {
    id: 1,
    nombre: "Interstellar",
    categoria: "Sci-Fi",
    destacado: true,
    precio: 18500,    // 👈 NUEVO: número entero, sin decimales ni símbolos
    stock: 5,         // 👈 NUEVO: cuántos hay disponibles
  },
  // ...tus 20 items
];
```

> ⚠️ **El precio va como número**, no como `"$18.500"`. Si es un string no lo podés multiplicar ni sumar. Para mostrarlo con formato está tu `formatearPrecio`.

### 🗺️ Las tres pantallas

```
TIENDA                    CHECKOUT                  CONFIRMACIÓN
catálogo + buscador  →    resumen del pedido   →    "Gracias {nombre}"
carrito en un modal       + formulario              carrito vacío
con cantidades            con validaciones
```

---

## 🧱 Estructura sugerida

Esta vez **sí es bastante obligatoria**, porque el Bloque A la evalúa.

```
src/
├── main.jsx                  🚪 acá van los Providers
├── App.jsx                   🏠 layout + qué vista se muestra. NADA MÁS
├── index.css                 🎨 Tailwind + @theme
│
├── data/
│   ├── productos.js          📦 tu catálogo (20 items, con precio y stock)
│   └── vistas.js             🗺️ las constantes de las 3 vistas
│
├── context/
│   ├── CarritoContext.jsx    🛒 obligatorio
│   └── ThemeContext.jsx      🌗 obligatorio
│
├── hooks/
│   ├── useLocalStorage.js    🪝 copiado del TP2, sin tocar
│   ├── useCarrito.js         🛒 la lógica del carrito
│   └── useToggle.js          🔘 del TP2
│
├── views/
│   ├── Tienda.jsx            🖥️ catálogo + buscador
│   ├── Checkout.jsx          🖥️ resumen + formulario
│   └── Confirmacion.jsx      🖥️ el gracias
│
├── components/
│   ├── ui/
│   │   └── Modal.jsx         🧩 genérico, con children
│   ├── layout/
│   │   └── Navbar.jsx        🧭 logo + tema + botón carrito
│   ├── ProductoList.jsx
│   ├── ProductoCard.jsx
│   ├── CarritoModal.jsx
│   └── CarritoItem.jsx       🆕 una fila con los botones +/-
│
└── utils/
    └── formato.js            🔧 formatearPrecio
```

---

## 🗂️ BLOQUE A — Arquitectura y limpieza *(Clase 01)*

Este bloque **no agrega ninguna funcionalidad**. Es orden. Y es el más rápido de todos.

### Requisitos (son 4)

1. ✅ **La estructura de arriba**, con `data/`, `context/`, `hooks/`, `views/`, `components/` (con `ui/` y `layout/`) y `utils/`. Ningún archivo suelto en `src/` que debería estar en una carpeta.

2. ✅ **`App.jsx` es layout y decisión de vista, nada más.** No puede tener: `localStorage`, `JSON.parse`, `JSON.stringify`, filtros del catálogo, ni lógica del carrito.

3. ✅ **`utils/formato.js` con `formatearPrecio`**, usado en **todos** los precios de la app. Ningún precio se muestra crudo con un `$` pegado a mano.
   ```js
   export function formatearPrecio(numero) {
     return numero.toLocaleString('es-AR', {
       style: 'currency', currency: 'ARS', maximumFractionDigits: 0,
     });
   }
   ```

4. ✅ **`data/vistas.js` con las constantes**, y usadas en todas las comparaciones. Cero strings sueltos tipo `vista === 'checkout'`.
   ```js
   export const VISTAS = { TIENDA: 'tienda', CHECKOUT: 'checkout', CONFIRMACION: 'confirmacion' };
   ```

### Y también

- ✅ **Cero código comentado.** Si no se usa, se borra. Para eso está Git.
- ✅ **Cero imports sin usar** y **cero `console.log`** olvidados *(el del pedido en el checkout es la única excepción, ese va)*.
- ✅ Respetar las convenciones de nombres: componentes en PascalCase, hooks con `use`, constantes en MAYÚSCULA.
- ✅ Nada en `hooks/` que no use hooks adentro. Si es JavaScript común, va en `utils/` y **sin** `use` en el nombre.

> 🎯 **Cómo lo chequeo:** abro tu `App.jsx`. Si en 20 segundos entiendo qué hace la app, el bloque está aprobado.

---

## 🛒 BLOQUE B — Carrito con cantidades y Context *(Clase 02)*

**El bloque troncal del TP.** Si entregás solo A y B, tenés un TP defendible.

### Requisitos (son 5)

1. ✅ **`hooks/useCarrito.js`** que usa `useLocalStorage` por dentro y expone, más o menos, esto:
   ```js
   const { carrito, cantidadTotal, total, estaEnElCarrito,
           agregar, cambiarCantidad, quitar, vaciar } = useCarrito();
   ```
   Los nombres los elegís vos. Lo que importa es que **toda** la lógica del carrito viva acá.

2. ✅ **Cantidades, sin mutar.** Cada item del carrito tiene `cantidad`. Se puede subir y bajar con botones `+` y `-`.
   - Agregar algo que **ya está** en el carrito le suma 1, **no lo duplica**.
   - Bajar la cantidad a **0 saca el item** del carrito.
   - **No se puede pasar del `stock`** del producto. El botón `+` se deshabilita o no hace nada.
   - Se modifica con **`map` + spread**. Cero `push`, `splice`, `sort()`, y cero `find` + asignación.

3. ✅ **`context/CarritoContext.jsx`** con el patrón completo:
   - Exporta el **Provider** y un **hook consumidor** (`useCarritoContext`).
   - **No exporta el contexto.**
   - El hook consumidor tiene el **guardia** con un mensaje de error claro si se usa afuera del Provider.
   - El Provider usa tu `useCarrito()` por dentro. **No reescribís la lógica ahí.**

4. ✅ **`context/ThemeContext.jsx`** con el mismo patrón, para el modo oscuro persistido. Usa tu `useLocalStorage`, así que la persistencia sale gratis.

5. ✅ **Cero prop drilling.** Ningún componente recibe una prop que **solo** le pasa al hijo. Esto se chequea archivo por archivo y es el criterio principal del bloque.

### Y también

- ✅ **El total y la cantidad total son derivados.** `reduce` y `reduce`. Si hay un `useState` para el total, está mal.
- ✅ **El buscador NO está en un contexto.** Es un `useState` en `views/Tienda.jsx`.
- ✅ El catálogo se importa, **no** es un `useState`.
- ✅ El carrito **sobrevive al F5** (te sale gratis por usar `useLocalStorage`).
- ✅ Los `Provider` están en `main.jsx`, envolviendo `App`.
- ✅ **Empty state** del carrito vacío, con texto humano.

> 🎯 **Cómo lo chequeo, y es objetivo:**
> - Busco `useState` del carrito en todo tu `src/`: solo puede estar dentro de `useCarrito.js`.
> - Busco `localStorage`: solo puede estar dentro de `useLocalStorage.js`.
> - Abro tres componentes al azar y cuento props que pasan de largo. Tienen que ser **cero**.

---

## 📝 BLOQUE C — Checkout con React Hook Form *(Clase 03, asincrónica)*

### Requisitos (son 5)

1. ✅ **Las tres vistas funcionando** con el estado de vista y tus constantes de `VISTAS`. Se puede ir del carrito al checkout, volver atrás, y del checkout a la confirmación.

2. ✅ **El formulario con React Hook Form** y **7 campos**:

   | Campo | Tipo | Validación |
   |---|---|---|
   | Nombre completo | texto | obligatorio + mínimo 3 caracteres |
   | Email | email | obligatorio + `pattern` |
   | Teléfono | texto | obligatorio + solo números + mínimo 8 |
   | Método de envío | radio | uno obligatorio (con `defaultValues`) |
   | Dirección | texto | **condicional**: obligatoria solo si es a domicilio |
   | Notas | textarea | opcional, máximo 200 caracteres |
   | Acepto los términos | checkbox | obligatorio |

3. ✅ **Cero `useState` para los campos del formulario.** Y cero `value` + `onChange` en esos inputs. Si quedó alguno, el bloque no está hecho.

4. ✅ **La dirección aparece y desaparece con `watch`**, según el método de envío.

5. ✅ **El pedido se arma a mano en `onSubmit`**, y la dirección va en `null` si eligió retiro en el local.
   ```js
   direccion: datos.metodoEnvio === 'domicilio' ? datos.direccion : null,
   ```
   *(Esto es por la trampa de la clase: RHF no borra el valor de un campo que se desmonta. Si mandás los datos crudos, le mandás una dirección a alguien que eligió retirar.)*

### Y también

- ✅ **Los mensajes de error se muestran debajo de su campo**, con texto humano en español. No "Error en el campo email".
- ✅ **El resumen del pedido sale del contexto**, no de props. Muestra cada item con su cantidad y subtotal, y el total.
- ✅ **Al confirmar pasan tres cosas:** se hace `console.log` del pedido, **se vacía el carrito**, y se muestra la confirmación con el nombre de la persona.
- ✅ **No se puede entrar al checkout con el carrito vacío.** El botón se deshabilita.
- ✅ Cada `input` tiene su `<label>` con `htmlFor` que coincide con el `id`.
- ✅ **Accesibilidad en los errores:** al menos **dos** campos con `aria-invalid` y `aria-describedby` apuntando al mensaje.
- ✅ Cero `event.preventDefault()` escrito a mano: eso lo hace `handleSubmit`.

---

## 🚀 BLOQUE D — Deploy *(Clase 04)*

El bloque más corto del TP y el que más te sirve para tu portfolio.

### Requisitos (son 3)

1. ✅ **`pnpm run build` corre sin errores** en tu máquina. Este es el paso que nadie hace y el que rompe todos los deploys.
2. ✅ **La app desplegada en Netlify** (o Vercel), conectada a tu repo de GitHub.
3. ✅ **El link del deploy en el README**, arriba de todo y clickeable.

### Y también

- ✅ **Probá el deploy de verdad**, no solo que cargue: agregá productos, cambiá cantidades, hacé el checkout completo, recargá con F5.
- ✅ Que ande en el celular. Abrilo con tu teléfono, es la prueba más rápida.

> ⚠️ **El error que se van a comer:** en Windows, `import Card from './components/card'` funciona aunque el archivo se llame `Card.jsx`. En el servidor de Netlify (que es Linux) **no**. Revisen que cada import tenga **exactamente** las mismas mayúsculas que el nombre del archivo. Te lo vengo avisando desde el Sprint 1. 😄

---

## 🚫 Lo que NO va

`fetch` a APIs · `useReducer` · React Router · Redux / Zustand / Jotai · TypeScript · `Controller` de RHF · librerías de UI (MUI, shadcn, Chakra) · pasarelas de pago · `useMemo` / `useCallback` · `forwardRef`.

**Los requisitos obligatorios se hacen con lo que vimos.** Si tu TP tiene un `useReducer`, te voy a pedir que me lo expliques línea por línea, y si no podés, cuenta como requisito no cumplido. No es maldad: es que la IA te lo mete sin preguntarte y quiero que lo puedas frenar.

> 💡 **Dos excepciones, y las dos son bonus:**
> - **Motion** (`pnpm i motion`) para animar el modal o las vistas. Eso es diseño, no lógica.
> - **Zod** para validar el formulario. Es el 🧪 **Bonus de autonomía**, tiene su propia sección más abajo y reglas distintas al resto.

---

## ✨ Calidad

- **Consola limpia:** cero errores, cero warnings de `key`, cero warnings de inputs controlados/no controlados.
- **HTML semántico:** `header`, `nav`, `main`, `section`, `form`, `fieldset`, `ul`/`li`, `button`. Nada de `<div onClick>`.
- **Accesibilidad:**
  - `label` + `htmlFor` en **todos** los campos del formulario.
  - `aria-label="Cerrar"` en los botones de cerrar el modal.
  - Los botones `+` y `-` con `aria-label` que se entienda (`"Sumar uno"`, no solo el símbolo).
  - Si el único indicador de algo es el color, no alcanza.
- **Responsive:** probalo en **375px** antes de entregar. El checkout es el que más se rompe.
- **Sin markup repetido:** si copiaste una card o una fila del carrito, falta un `.map()`.
- **Tailwind con tus tokens en `@theme`.** Cero valores arbitrarios tipo `text-[#e50914]` desparramados.

---

## 🎁 Bonus (opcional, suma en la Review)

Solo si terminaste los cuatro bloques. **No compensan requisitos faltantes.**

### 🏆 El bonus recomendado: `ToastContext`

Este es el que más te va a servir en la defensa, porque es el que demuestra que entendiste **para qué** sirve Context y no solo cómo se escribe.

La idea: un contexto que **no persiste nada** y cuyo único trabajo es "avisá desde cualquier lado, que se muestra en un solo lugar". El caso de uso se cae de maduro con el `stock`:

> Intentás agregar un producto, no hay más stock, y aparece un cartelito arriba a la derecha que dice "No hay más stock de Interstellar" y se va solo a los 3 segundos.

El aviso **se dispara en la card** y **se muestra en el layout**. Con props eso es imposible sin cablear media app.

Si lo entregás, te voy a preguntar dos cosas y con eso alcanza:
- ¿Por qué esto necesita un contexto y no props?
- ¿Dónde está el `setTimeout` que lo hace desaparecer, y dónde está su limpieza?

### 🧪 El bonus de autonomía: validar con Zod

**Este bonus es distinto a todos los demás, y por eso tiene su propia sección.**

En los otros bonus te doy la receta. **Acá no te doy nada.** No hay código para copiar, no está en ninguna clase, y el video de la clase 03 no lo menciona. La idea es exactamente esa: **que aprendas algo solo, leyendo la documentación oficial.**

> 🎯 **Por qué te pido esto:** en dos meses arranca tu proyecto final, y después de eso, tu primer trabajo. En los dos escenarios te van a pedir cosas que nadie te enseñó. **La habilidad que se evalúa acá no es Zod: es poder aprender una herramienta nueva sin que nadie te la explique.** Zod es la excusa.

#### 🤔 Qué es y qué problema resuelve

En el formulario del TP, las validaciones están **desparramadas entre los inputs**:

```jsx
{...register('email', { required: 'Obligatorio', pattern: { value: /.../, message: '...' } })}
{...register('telefono', { required: 'Obligatorio', minLength: { value: 8, message: '...' } })}
```

Funciona, pero mirá lo que pasa: **las reglas viven en el JSX**, mezcladas con la presentación. Si querés saber qué se valida, tenés que leer todo el formulario.

**Zod** te deja escribir las reglas en **un solo lugar**, separadas del JSX, en algo que se llama **esquema**:

```js
// la idea, para que veas la forma. NO es código para copiar
const esquemaCheckout = z.object({
  nombre: z.string().min(3, 'Al menos 3 caracteres'),
  // ...
});
```

Y después le decís a `useForm` que valide con ese esquema en vez de con las reglas de `register`.

**Las tres ventajas reales:**

| Ventaja | Por qué importa |
|---|---|
| Las reglas están juntas y separadas del JSX | Leés el esquema y sabés qué valida el formulario, sin leer el componente |
| El esquema es **reutilizable** | En el Sprint 6, el **backend** puede validar con el mismo esquema |
| Es el estándar del mercado | Vas a ver `zod` en el `package.json` de casi cualquier proyecto React serio |

#### 📚 Tu trabajo: investigar

**Las fuentes, en este orden:**

1. 🥇 **La documentación oficial de Zod:** [zod.dev](https://zod.dev). Empezá por *Basic usage* y después mirá *Strings* y *Numbers*.
2. 🥇 **La documentación oficial de RHF sobre validación con esquemas:** [react-hook-form.com/get-started#SchemaValidation](https://react-hook-form.com/get-started#SchemaValidation). Es corta y tiene el ejemplo completo.
3. 🥈 **Un video.** Buscá en YouTube "react hook form zod" y mirá **uno** de 15 o 20 minutos. Ojo con la fecha: si es de 2023 o antes, puede estar desactualizado.
4. 🥉 **La IA, y recién al final.** Reglas de uso más abajo.

**El paquete que hace de puente** entre Zod y RHF se llama `@hookform/resolvers`. Ese dato te lo doy porque es lo único que no se deduce leyendo Zod:

```bash
pnpm add zod @hookform/resolvers
```

#### 🔎 Las preguntas que tenés que poder contestar

No te doy el código, te doy **el mapa**. Si contestás estas seis, el bonus está hecho:

1. ¿Qué es un **esquema** de Zod y con qué función se crea uno para un objeto?
2. ¿Qué hace `zodResolver` y **dónde exactamente** se lo pasás a `useForm`?
3. ¿Cómo se escribe un mensaje de error personalizado en Zod? *(Pista: es distinto a RHF.)*
4. **¿Cuál es la forma recomendada HOY de validar un email con Zod?** ⚠️ Ojo con esta: la que aparece en la mayoría de los tutoriales y la que te va a sugerir la IA **está deprecada**. La forma actual está en la doc oficial. **Si en tu TP aparece la vieja, sé que no leíste la doc.**
5. Cuando usás un esquema, **¿qué pasa con las reglas que tenías en `register`?** ¿Siguen funcionando? ¿Conviene dejarlas?
6. ¿Cómo se valida un **checkbox que tiene que estar tildado** con Zod? *(Es menos obvio de lo que parece.)*

#### 🎁 Y el desafío de verdad

Si querés que este bonus valga el doble: **hacé la dirección condicional con Zod.** O sea, que la dirección sea obligatoria **solo si** el método de envío es a domicilio, **desde el esquema**.

Eso no se resuelve con las validaciones básicas. Buscá en la doc de Zod cómo validar **un objeto entero** en vez de un campo suelto. *(Pista: la sección se llama algo parecido a "Refinements".)*

#### 🤖 Las reglas del uso de IA en este bonus

**No te prohíbo la IA.** Te pido que la uses en el orden correcto, porque acá el objetivo es aprender, no entregar.

| ✅ Sí | ❌ No |
|---|---|
| "Explicame qué es un esquema de Zod y para qué sirve" | "Hacéme el esquema del checkout" |
| "¿Por qué esta línea de mi esquema no valida?" | Pegar el formulario y pedir que lo convierta a Zod |
| "¿Qué diferencia hay entre estas dos formas que vi en la doc?" | Copiar el código y no leer nada |
| Pedirle que te haga preguntas para chequear si entendiste | 🏆 esto es lo mejor que podés hacer con una IA |

> 🗣️ **Y te lo digo derecho:** si le pedís el código de una, lo vas a tener andando en cuatro minutos. **Y en la Review se va a notar en la primera pregunta.** Este bonus no suma por tenerlo, suma por poder explicarlo. Si no lo vas a entender, no lo entregues y hacé otro bonus: no pasa nada.

#### 📝 Qué entregar

1. El formulario validando con Zod, andando.
2. El esquema en su propio archivo, por ejemplo `src/schemas/checkoutSchema.js`.
3. **En el README, una sección `## Bonus: Zod`** con:
   - Qué es un esquema, **en tus palabras**. Dos o tres líneas.
   - Qué cambió en tu código al pasar de las reglas de `register` al esquema.
   - **Qué fuentes usaste** (links a la doc, al video que miraste) y **qué te costó entender**.

> 🎓 Esa última parte no es relleno. Poder decir "esto me costó y así lo resolví" es literalmente lo que vas a hacer en una daily de tu primer trabajo.

#### 🗣️ Cómo lo voy a evaluar en la Review

Cuatro preguntas, y con eso alcanza:

- Mostrame tu esquema y **explicame dos líneas** que yo elija.
- ¿Qué pasa si le saco el `resolver` del `useForm`? *(Predecí antes de correr.)*
- ¿Por qué el mensaje de error sigue apareciendo en `formState.errors` si ahora valida Zod y no RHF?
- ¿Dónde más, en el futuro, podrías reusar este esquema?

---

### El resto

- 🔀 **Filtro por categoría**, con la lista **derivada de los datos**: `['Todas', ...new Set(productos.map(p => p.categoria))]`.
- 📊 **Ordenar** por precio o nombre. Ojo: `sort()` muta, así que `[...productos].sort(...)`.
- ⌨️ **Cerrar el modal con `Escape`** (si no lo hiciste en el TP2).
- 💾 **Historial de pedidos** con tu `useLocalStorage`: guardás cada pedido confirmado y los mostrás en una lista. Es la mejor prueba de que tu hook quedó genérico.
- 🎫 **Cupón de descuento:** un input que si escribís `NODO10` descuenta 10% del total. Suena tonto y es un buen ejercicio de estado derivado.
- ✅ **`mode: 'onBlur'`** en `useForm`, para que valide al salir de cada campo. Una línea y se siente mucho mejor.
- 🎬 **Animar** las vistas y el modal con Motion.

---

## 🚀 Recetas

Las cuatro partes difíciles, resueltas. El resto es tuyo.

### 🔹 1. Cambiar la cantidad sin mutar *(Bloque B)*

```js
const cambiarCantidad = (id, delta) => {
  setCarrito((prev) =>
    prev
      .map((item) =>
        item.id === id
          ? { ...item, cantidad: item.cantidad + delta }   // copia modificada
          : item                                           // los demás, intactos
      )
      .filter((item) => item.cantidad > 0)                 // el que llegó a 0, se va
  );
};
```

### 🔹 2. Agregar, sumando si ya está *(Bloque B)*

```js
const agregar = (producto) => {
  setCarrito((prev) => {
    const yaEsta = prev.find((item) => item.id === producto.id);

    if (yaEsta) {
      if (yaEsta.cantidad >= producto.stock) return prev;   // no hay más stock
      return prev.map((item) =>
        item.id === producto.id ? { ...item, cantidad: item.cantidad + 1 } : item
      );
    }

    return [...prev, { ...producto, cantidad: 1 }];
  });
};

// derivados: NO son useState
const total = carrito.reduce((acc, item) => acc + item.precio * item.cantidad, 0);
const cantidadTotal = carrito.reduce((acc, item) => acc + item.cantidad, 0);
```

### 🔹 3. El contexto completo *(Bloque B)*

```jsx
// src/context/CarritoContext.jsx
import { createContext, useContext } from 'react';
import useCarrito from '../hooks/useCarrito';

const CarritoContext = createContext(null);      // 👈 sin export

export function CarritoProvider({ children }) {
  const valor = useCarrito();                    // 👈 tu hook, sin reescribirlo

  return (
    <CarritoContext.Provider value={valor}>
      {children}
    </CarritoContext.Provider>
  );
}

export function useCarritoContext() {
  const contexto = useContext(CarritoContext);

  if (!contexto) {
    throw new Error('useCarritoContext() tiene que usarse adentro de <CarritoProvider>');
  }

  return contexto;
}
```

### 🔹 4. El campo condicional y el pedido *(Bloque C)*

```jsx
const { register, handleSubmit, watch, formState: { errors } } = useForm({
  defaultValues: { metodoEnvio: 'domicilio' },
});

const metodoEnvio = watch('metodoEnvio');

// ...en el JSX:
{metodoEnvio === 'domicilio' && (
  <>
    <label htmlFor="direccion">Dirección</label>
    <input id="direccion" {...register('direccion', { required: 'La dirección es obligatoria' })} />
    {errors.direccion && <p className="text-red-500">{errors.direccion.message}</p>}
  </>
)}
```

```js
const onSubmit = (datos) => {
  const pedido = {
    cliente: {
      nombre: datos.nombre,
      email: datos.email,
      telefono: datos.telefono,
      direccion: datos.metodoEnvio === 'domicilio' ? datos.direccion : null,
    },
    envio: datos.metodoEnvio,
    notas: datos.notas,
    items: carrito,
    total,
  };

  console.log('Pedido:', pedido);
  vaciar();
  onConfirmar(pedido);
};
```

---

## 📦 Entrega

⚠️ **El TP se entrega subido a un repositorio.** ⚠️

1. **Un repo público en GitHub, nuevo**, solamente con el proyecto (no una carpeta que adentro tenga una carpeta que adentro tenga el proyecto).
2. **`.gitignore` con `node_modules`.** Si lo subís, lo primero que te voy a pedir es que lo saques.
3. **El link por el formulario de la plataforma.**
4. **Commits que se entiendan.** Mínimo uno por bloque: `chore: estructura de carpetas`, `feat: carrito con cantidades`, `refactor: carrito al context`, `feat: checkout con react-hook-form`, `chore: deploy`.
5. **Un `README.md`** con estas secciones:

```markdown
# Nombre de tu tienda

🔗 **Demo:** https://tu-app.netlify.app

## Qué es
Dos líneas.

## Cómo correrlo
pnpm install && pnpm run dev

## Mis contextos
Por cada uno: qué guarda, quién lo consume, y **por qué es global**.
(Tres líneas cada uno. Esta es la sección más importante del README.)

## Mis hooks
Qué hace cada uno y qué devuelve. Dos líneas cada uno.

## Decisiones de estado
Qué estado NO puse en un contexto y por qué.

## Prop drilling: antes y después
Cuántas props pasaban de largo antes de Context y cuántas ahora.

## Qué generé con IA
Qué partes generaste, qué corregiste a mano y por qué.

## Bonus: Zod  (solo si hiciste ese bonus)
Qué es un esquema en tus palabras, qué cambió en tu código,
qué fuentes usaste y qué te costó entender.
```

> 🗣️ **La sección "Mis contextos" es la que más peso tiene.** Si la podés escribir sin mirar el código, la defensa la tenés aprobada.

---

## 🎯 Cómo se evalúa

Con todo esto en verde, el TP está aprobado.

### Funciona

- [ ] Levanta con `pnpm install && pnpm run dev` sin errores.
- [ ] Agrego productos y el contador del navbar acompaña.
- [ ] Agregar algo que ya está **suma cantidad**, no duplica la fila.
- [ ] Los botones `+` y `-` andan, y bajar a 0 **saca el item**.
- [ ] No puedo pasarme del stock.
- [ ] El total es correcto (hacé la cuenta a mano una vez).
- [ ] Recargo con F5 y el carrito sigue ahí.
- [ ] El modo oscuro anda y **se acuerda** después del F5.
- [ ] No puedo entrar al checkout con el carrito vacío.
- [ ] El formulario no me deja enviar con errores y los mensajes se ven.
- [ ] La dirección aparece y desaparece según el método de envío.
- [ ] Al confirmar: se vacía el carrito y veo mi nombre en la confirmación.
- [ ] Se ve bien en 375px.
- [ ] **El deploy funciona** y hago todo el flujo ahí.

### Está bien hecho

- [ ] La estructura de carpetas es la del Bloque A.
- [ ] `App.jsx` es layout y decisión de vista, nada más.
- [ ] `localStorage` aparece **solo** dentro de `useLocalStorage.js`.
- [ ] El `useState` del carrito está **solo** dentro de `useCarrito.js`.
- [ ] **Cero componentes que reciban props que solo pasan al hijo.**
- [ ] Los contextos exportan Provider + hook consumidor, y **no** exportan el contexto.
- [ ] Los hooks consumidores tienen el **guardia** con mensaje claro.
- [ ] Cero mutaciones: ni `push`, ni `splice`, ni `sort()` sobre el estado, ni `find` + asignación.
- [ ] El total y la cantidad total son **derivados**.
- [ ] El buscador **no** está en un contexto.
- [ ] **Cero `useState` para los campos del formulario.**
- [ ] La dirección va en `null` si eligió retiro.
- [ ] Todos los precios pasan por `formatearPrecio`.
- [ ] Cero magic strings para las vistas.
- [ ] Cero código comentado, imports sin usar y `console.log` de más.
- [ ] Consola limpia.
- [ ] Nada de la lista de "Lo que NO va".
- [ ] Repo público, sin `node_modules`, commits que se entienden.
- [ ] README completo, con **"Mis contextos"**.

### 🗣️ La defensa en la Review (Semana 9)

Reunión 1 a 1, 15 minutos. Compartís pantalla. Tres partes:

**1. Contame el mapa (5 min).** Qué contextos tenés, qué guarda cada uno, y **por qué ese dato es global**. Y lo inverso: qué dejaste afuera de los contextos y por qué.

**2. Un cambio en vivo (5 min).** 
<!-- - "Mostrá la cantidad total de productos en el footer." *(Con Context bien puesto es una línea. Si tardás, algo está cableado con props.)*
- "Agregá un campo 'código postal' al formulario, obligatorio y de 4 números."
- "Hacé que el `+` no se pueda apretar si el producto no tiene stock."
- "Cambiá el `10%` del cupón por `15%`." *(si hiciste ese bonus)* -->

**3. Predecí antes de correr (5 min).** Te voy a pedir que rompas algo y **me digas qué va a pasar antes de guardar**:
- "Saco el `<CarritoProvider>` de `main.jsx`. ¿Qué pasa?"
- "Muevo el Provider adentro de `App`, envolviendo solo la vista de la tienda, y dejo el `Navbar` afuera. ¿Qué se rompe?"
- "En el `Navbar` llamo `useCarrito()` directo en vez de `useCarritoContext()`. ¿Qué pasa?"
- "Le saco el `.filter()` a tu `cambiarCantidad`. ¿Qué pasa si bajo a 0?"

> 🎣 **Sobre las preguntas:** algunas tienen truco a propósito. **Decir "no sé" es una respuesta válida y no baja nota.** Lo que baja nota es inventar cualquier cosa para zafar.

### 🔍 Y las preguntas sobre lo generado con IA

No te voy a preguntar si usaste IA, porque **quiero** que uses IA. Te voy a preguntar por estas cosas, así que **revisalas antes de entregar**:

| Si en tu código hay... | Te voy a preguntar |
|---|---|
| `useCallback` / `useMemo` | "¿Qué pasa si lo borro?" *(Respuesta: nada. Entonces borralo.)* |
| `import React from 'react'` | "¿Dónde usás `React` en este archivo?" |
| `// eslint-disable-next-line` | "¿Qué error estaba tapando?" |
| `datos?.cliente?.nombre` | "¿Cuándo puede ser `undefined` esto?" |
| Un `useEffect` que solo hace `set` | "¿Con qué cosa que no es React está hablando?" |
| `try/catch` donde nada puede fallar | "¿Qué excepción esperás acá?" |
| Un `useReducer` | "Explicame esto línea por línea." |
| Zod *(el bonus)* | Las cuatro preguntas de la sección del bonus. Y ojo con la forma deprecada del email. |

> 🎯 **El criterio de siempre:** **si no lo podés explicar, no lo podés entregar.** Y ahora la versión práctica: **si hay código que no podés defender, sacalo.** Simplificar también es una decisión profesional.

---

## 🚩 Errores que bajan nota (se arreglan en 5 minutos)

- ❌ `node_modules` subido al repo.
- ❌ `carrito.push(producto)` o `item.cantidad++`. Es el bug número uno del sprint.
- ❌ `find` + asignación al objeto y después `setCarrito([...carrito])`. **El array es nuevo pero el objeto lo mutaste.**
- ❌ Un `useState` para el total en vez de un `reduce`.
- ❌ Llamar `useCarrito()` en dos componentes en vez de `useCarritoContext()`. **Bug silencioso:** no explota, simplemente el dato no se actualiza.
- ❌ Exportar el contexto y usar `useContext` suelto en cada componente.
- ❌ El hook consumidor sin el guardia.
- ❌ Un contexto para el buscador o para "el modal está abierto".
- ❌ `useState` + `value` + `onChange` en los campos del formulario **teniendo** RHF.
- ❌ `required: true` sin mensaje, y después mensajes de error inventados a mano en otro lado.
- ❌ Mandar `datos` crudo del formulario, con la dirección de alguien que eligió retirar.
- ❌ `event.preventDefault()` escrito a mano en el submit.
- ❌ Precios mostrados como `${producto.precio}` sin formatear.
- ❌ `vista === 'checkout'` con el string suelto.
- ❌ `key={index}` en el carrito, del que se borran items.
- ❌ `<div onClick>` en vez de `<button>`.
- ❌ Inputs sin `label`.
- ❌ Imports con mayúsculas distintas al nombre del archivo. **Anda local y explota en el deploy.**
- ❌ Un solo commit llamado "final".
- ❌ El proyecto de la verdulería de la clase entregado tal cual.

---

## 🗓️ Cómo repartir el trabajo

Para que no llegues a la última noche:

| Cuándo | Qué |
|---|---|
| Después de la Clase 01 | Bloque A completo. Es el más rápido, hacelo de una |
| Después de la Clase 02 | Bloque B. **Es el más largo, dale el tiempo** |
| Después de la Clase 03 *(el video)* | Bloque C |
| Después de la Clase 04 | Bloque D + README + los bonus si llegás |

> 🆘 **Si vas justo de tiempo, este es el orden de prioridad:** A y B primero (son los que se evalúan más), después C, y D al final. Un TP con A, B y C bien hechos y sin deploy aprueba. Un TP con deploy y el carrito mutando arrays, no.
>
> Y si te trabás, **preguntá antes de entregar, no después.** La semana de consulta está para eso.

---

## 📚 Recursos

- **Docs oficiales de React, en español:**
  [Pasar datos con Context](https://es.react.dev/learn/passing-data-deeply-with-context) · [Escalar usando Reducer y Context](https://es.react.dev/learn/scaling-up-with-reducer-and-context) · [Actualizar arrays en el estado](https://es.react.dev/learn/updating-arrays-in-state) · [Reutilizar lógica con hooks propios](https://es.react.dev/learn/reusing-logic-with-custom-hooks)
- [React Hook Form: `useForm`](https://react-hook-form.com/docs/useform) · [`register` y sus reglas](https://react-hook-form.com/docs/useform/register) · [`formState`](https://react-hook-form.com/docs/useform/formstate)
- [`Number.toLocaleString` (MDN)](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString)
- [Netlify: desplegar un sitio](https://docs.netlify.com/site-deploys/create-deploys/)
- [Tailwind CSS v4](https://tailwindcss.com)

**Para el bonus de Zod** *(leelos vos, no se los pases a la IA)*:
- [Zod: documentación oficial](https://zod.dev) — arrancá por *Basic usage*
- [React Hook Form: validación con esquemas](https://react-hook-form.com/get-started#SchemaValidation)
- [`@hookform/resolvers`](https://github.com/react-hook-form/resolvers) — el puente entre los dos

> ⚠️ **Cuidado al googlear React Hook Form:** los tutoriales de la v6 usan `ref={register}`. Eso ya no funciona. La forma correcta es siempre `{...register('campo')}`.

---

📌 **¡Manos a la obra!** Dudas por la plataforma o en la clase de consulta. Y si algo se rompe, mandá el error completo, no "no me funciona". 🛒
