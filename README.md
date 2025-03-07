## 📌 Trabajo Práctico 3: Carrito de Compras con Context

### 🎯 **Objetivo**
Construir una aplicación de carrito de compras utilizando **React, useContext, useState y useEffect**, reforzando el manejo de estado global y persistencia de datos con Local Storage.

---

### 📋 **Requisitos**
1. **Lista de productos**: Mostrar una lista de productos predefinidos.
2. **Agregar al carrito**: Permitir a los usuarios agregar productos al carrito.
3. **Evitar productos duplicados**: Si un producto ya está en el carrito, en lugar de duplicarlo, aumentar la cantidad.
4. **Modificar cantidad de productos**: Permitir a los usuarios aumentar o disminuir la cantidad de cada producto en el carrito.
5. **Eliminar del carrito**: Permitir a los usuarios eliminar productos del carrito.
6. **Mostrar el total del carrito**: Calcular y mostrar el precio total de todos los productos en el carrito.
7. **useContext**: Utilizar `useContext` para manejar el carrito de compras.
8. **Persistencia con Local Storage**: Guardar el carrito en Local Storage para que no se pierda al recargar la página.
9. **Modo Claro/Oscuro**: Implementar un botón para cambiar el tema utilizando `useContext`.
10. **Despliegue en Netlify**: Subir el proyecto a Netlify y compartir el enlace.

---

### 🛠 **Guía y Pistas**

#### 📌 **Estructura Sugerida del Proyecto**
```plaintext
/src
 ├── components
 │   ├── ProductList.jsx   # Muestra los productos disponibles
 │   ├── Cart.jsx          # Muestra los productos en el carrito con cantidad y total
 │   ├── ThemeButton.jsx   # Botón para cambiar el tema
 ├── context
 │   ├── CartContext.jsx   # Maneja el estado global del carrito
 │   ├── ThemeContext.jsx  # Maneja el tema claro/oscuro
 ├── App.jsx               # Componente principal
 ├── main.jsx              # Renderiza la aplicación
```

#### 🛒 **Lista de Productos Sugeridos**
```js
const products = [
  { id: 1, name: "Laptop", price: 1000 },
  { id: 2, name: "Auriculares", price: 200 },
  { id: 3, name: "Mouse", price: 50 }
];
```

#### 🎨 **Ejemplo de Uso de `useContext` para el Carrito Mejorado**
```js
import { createContext, useState, useEffect } from "react";

export const CartContext = createContext();

export const CartProvider = ({ children }) => {
  const [cart, setCart] = useState(() => {
    return JSON.parse(localStorage.getItem("cart")) || [];
  });

  useEffect(() => {
    localStorage.setItem("cart", JSON.stringify(cart));
  }, [cart]);

  const addToCart = (product) => {
    setCart((prevCart) => {
      const existingProduct = prevCart.find((item) => item.id === product.id);
      if (existingProduct) {
        return prevCart.map((item) =>
          item.id === product.id ? { ...item, quantity: item.quantity + 1 } : item
        );
      } else {
        return [...prevCart, { ...product, quantity: 1 }];
      }
    });
  };

  const removeFromCart = (id) => {
    setCart((prevCart) => prevCart.filter((product) => product.id !== id));
  };

  const updateQuantity = (id, quantity) => {
    setCart((prevCart) =>
      prevCart.map((product) =>
        product.id === id ? { ...product, quantity: Math.max(1, quantity) } : product
      )
    );
  };

  const totalPrice = cart.reduce((total, product) => total + product.price * product.quantity, 0);

  return (
    <CartContext.Provider value={{ cart, addToCart, removeFromCart, updateQuantity, totalPrice }}>
      {children}
    </CartContext.Provider>
  );
};
```

#### ✅ **Criterios de Evaluación**
- [ ] La aplicación muestra una lista de productos.
- [ ] Se pueden agregar productos al carrito sin duplicados.
- [ ] Se pueden aumentar o disminuir las cantidades de los productos en el carrito.
- [ ] Se pueden eliminar productos del carrito.
- [ ] Se calcula correctamente el precio total del carrito.
- [ ] Se utiliza `useContext` para manejar el estado del carrito.
- [ ] Se guarda el estado del carrito en Local Storage.
- [ ] Se implementa el cambio de tema claro/oscuro con `useContext`.
- [ ] El código está limpio, ordenado y bien estructurado.
- [ ] Los estilos son coherentes y bien aplicados en la interfaz.
- [ ] El proyecto está desplegado en Netlify y se comparte el enlace.

---

¡Buena suerte y a programar! 🚀

