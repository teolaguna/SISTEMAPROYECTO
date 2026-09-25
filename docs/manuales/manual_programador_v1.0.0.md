# Manual del Programador
## Módulo: `EntidadProyecto` — Modelado con Struct y Punteros

---

## 1. Propósito del módulo

Este código es una plantilla base para representar cualquier entidad del Proyecto Integrador Anual (un sensor, un motor, un componente de hardware, etc.) usando una estructura (`struct`) y pasaje de datos por **puntero**. Está pensado para ser **reutilizado y adaptado**, no solo ejecutado.

---

## 2. Estructura de datos: `EntidadProyecto`

```cpp
struct EntidadProyecto {
    int id;
    char nombre[50];
    float metrica;
};
```

| Miembro   | Tipo         | Tamaño aprox. | Uso previsto                                  |
|-----------|--------------|---------------|------------------------------------------------|
| `id`      | `int`        | 4 bytes       | Identificador único de la entidad              |
| `nombre`  | `char[50]`   | 50 bytes      | Descripción o etiqueta (cadena estilo C, no `std::string`) |
| `metrica` | `float`      | 4 bytes       | Valor numérico decimal (lectura, avance, consumo, etc.) |

**Por qué `char[50]` y no `std::string`:** el ejercicio busca practicar cadenas estilo C y su lectura con `cin.getline()`, que es compatible con arreglos de caracteres. Si se decide migrar a `std::string`, hay que cambiar `cin.getline(ptr->nombre, 50)` por `getline(cin, ptr->nombre)`.

**Cómo agregar un nuevo miembro** (por ejemplo, una unidad de medida):
1. Agregar el campo dentro del `struct`.
2. Inicializarlo en el valor por defecto de `miEntidad` en `main()`.
3. Agregar su lectura dentro de `cargarDatos()`.
4. Agregar su impresión en el bloque de verificación de `main()`.

---

## 3. Flujo del programa

```
main()
  │
  ├─ Declara e inicializa "miEntidad" (valores por defecto, en Stack)
  │
  ├─ Llama a cargarDatos(&miEntidad)   ← se envía la DIRECCIÓN, no una copia
  │      │
  │      └─ cargarDatos() escribe directamente sobre "miEntidad"
  │           usando el operador flecha (ptr->campo)
  │
  └─ Imprime los datos ya modificados + la dirección de memoria (&miEntidad)
```

**Punto clave:** como se pasa un puntero (`EntidadProyecto* ptr`) y no la estructura completa, `cargarDatos()` no trabaja sobre una copia. Cualquier cambio que haga se refleja inmediatamente en `miEntidad` dentro de `main()`.

---

## 4. Detalle de funciones

### `void cargarDatos(EntidadProyecto* ptr)`

- Recibe la dirección de memoria de una `EntidadProyecto`.
- Usa `ptr->id`, `ptr->nombre`, `ptr->metrica` para leer y escribir sobre el original.
- **Orden de lectura obligatorio:**
  1. `cin >> ptr->id;`
  2. `cin.ignore();` → limpia el salto de línea (`\n`) que queda pendiente en el buffer tras `cin >>`.
  3. `cin.getline(ptr->nombre, 50);`
  4. `cin >> ptr->metrica;`

  Si se elimina el `cin.ignore()`, el `getline()` va a leer una cadena vacía en lugar de esperar al usuario.

- **Si se agregan más lecturas de texto después de la métrica**, va a hacer falta otro `cin.ignore()` antes de cualquier `getline()` posterior, ya que `cin >> ptr->metrica` también deja un `\n` pendiente.

### `int main()`

- Inicializa `miEntidad` con valores neutros (`0`, `"Vacio - ..."`, `0.0f`) antes de cargar datos reales. Esto es buena práctica para evitar leer memoria no inicializada (basura) si algo falla en la carga.
- Imprime `&miEntidad` para mostrar la dirección hexadecimal en RAM donde vive la estructura. **Este valor cambia en cada ejecución** — no es un error, depende del sistema operativo.

---

## 5. Cómo extender este código a otra entidad del proyecto

Para reutilizar esta plantilla con otro componente (por ejemplo, un sensor IR, un motor, o un driver), seguir estos pasos:

1. Renombrar el `struct` si se desea (opcional, ej. `SensorIR`).
2. Ajustar el significado de `metrica` en el comentario y en los textos de `cout`/`cin` (por ejemplo, "Valor de reflectancia" en vez de "Metrica de Operacion").
3. Si la entidad necesita más de un valor numérico (por ejemplo, posición **y** lectura), agregar un nuevo miembro al `struct` siguiendo el punto 2 de la sección 2.
4. Si se van a manejar **varias entidades a la vez** (por ejemplo, un array de sensores), declarar un arreglo de structs:
   ```cpp
   EntidadProyecto sensores[8];
   for (int i = 0; i < 8; i++) {
       cargarDatos(&sensores[i]);
   }
   ```
   Esto es exactamente el mismo patrón, aplicado en bucle.

---

## 6. Buenas prácticas y errores a evitar

| Práctica incorrecta | Por qué falla | Corrección |
|---|---|---|
| `ptr.id` | El operador punto no funciona sobre punteros | Usar `ptr->id` |
| Omitir `cin.ignore()` antes de `getline()` | Queda un `\n` pendiente en el buffer | Agregar `cin.ignore();` después de cada `cin >>` seguido de `getline()` |
| Usar `cin.getline(ptr->nombre, 100)` cuando el arreglo es `char nombre[50]` | Se puede escribir fuera de los límites del arreglo (buffer overflow) | El segundo parámetro de `getline()` nunca debe superar el tamaño real del arreglo |
| Pasar la estructura por copia (`void cargarDatos(EntidadProyecto ptr)`) | Duplica la memoria y los cambios no se reflejan en el original | Pasar siempre por puntero (`EntidadProyecto* ptr`) o por referencia (`EntidadProyecto&`) |
| Declarar `float metrica` y luego ingresar texto | `cin` entra en estado de error y deja de leer correctamente | Validar que el usuario ingrese solo números en ese campo |

---

## 7. Posibles mejoras futuras (no implementadas en esta versión)

- Reemplazar `char nombre[50]` por `std::string` para mayor seguridad y flexibilidad.
- Agregar validación de entrada (por ejemplo, rechazar métricas negativas si no tienen sentido físico).
- Usar un array o `std::vector<EntidadProyecto>` para manejar múltiples entidades (por ejemplo, todos los sensores del array de línea) en lugar de una sola variable.
- Separar la definición del `struct` en un archivo de cabecera (`.h`) propio si el proyecto crece y se reutiliza en varios `.cpp`.