# Proyecto Integrador Anual

## Modelado de Struct con Punteros — `EntidadProyecto`

---

## 👥 Integrantes

| Nombre y Apellido       | Rol / Aporte                        |
|--------------------------|--------------------------------------|
| Malek Cheheid         | [Elaboración del Informe y Manual de programador]      |
| [Teo Laguna]           | [Repositorio y Ejecución del código]   |
| [Santiago Osma]           | [Redacción teórica y cuestionario]   |

**Institución:** EEST 1
**Materia:** Laboratorio de Programacion
**Docente:** Yamil Ganduglia, Mansilla Muñoz York Elías
**Fecha:** 25 de Septiembre de 2026

---

## 📌 Descripción del modelo

Este proyecto implementa una estructura (`struct`) en C++ llamada `EntidadProyecto`, pensada como modelo base para representar cualquier componente del Proyecto Integrador Anual (un sensor, un motor, un dispositivo de hardware, etc.) de forma organizada.

La estructura agrupa tres datos relacionados bajo un mismo nombre:

- **`id`** *(int)* — identificador numérico de la entidad.
- **`nombre`** *(char[50])* — descripción o etiqueta de la entidad.
- **`metrica`** *(float)* — valor numérico asociado (lectura de sensor, consumo, avance, etc.).

Los datos se cargan mediante una función que recibe un **puntero** a la estructura (`EntidadProyecto* ptr`), lo que permite modificar la entidad original directamente en memoria, sin generar copias innecesarias. El acceso a sus miembros se realiza con el **operador flecha** (`ptr->campo`).

Este modelo es la base conceptual utilizada para representar, dentro del hardware del robot del proyecto, entidades como los sensores del array de línea, motores u otros componentes que combinan datos heterogéneos (números y texto) en una sola unidad lógica.

---

## 📂 Contenido del repositorio

- `main.cpp` — código fuente del modelo con struct y puntero.
- `Cuestionario_Tecnico_Control_1.docx` — informe técnico con las respuestas al cuestionario.
- `Manual_del_Programador.md` — documentación técnica del código para desarrolladores.