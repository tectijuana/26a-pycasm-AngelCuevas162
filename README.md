# 4.1 📌 (Práctica de interacción asistida con LLM) Proyecto Python + C + ARM64 Assembly

**Nombre:** Pablo Angel Cuevas Marquez  
**Materia:** Lenguajes de Interfaz  
**Profesor:** Rene Solis Reyes  
**Fecha:** 16 de Abril de 2026  

---

## Descripción
Este proyecto implementa una librería de alto rendimiento en **ARM64 Assembly**, integrada con **Python** mediante un wrapper en **C** y la librería `ctypes`. El objetivo es demostrar la interoperabilidad entre lenguajes de alto y bajo nivel, analizando el flujo de datos desde el intérprete hasta los registros del CPU.

## Objetivo
Demostrar la capacidad de comunicación entre un lenguaje de alto nivel (Python) y uno de bajo nivel (Assembly), comprendiendo el uso de la arquitectura ARM64 y el análisis de rendimiento.

## Tecnologías
* **Lenguaje de Alto Nivel:** Python 3
* **Lenguaje de Interfaz:** C (Clang/GCC)
* **Lenguaje de Bajo Nivel:** ARM64 Assembly (AArch64)
* **Entorno:** Termux / Android

---

## REPORTE DE LA PRÁCTICA

### 1. Introducción
**ARM64** es una arquitectura RISC de 64 bits fundamental en dispositivos móviles y servidores modernos. El uso de **Assembly** en este proyecto ayuda a entender cómo funciona la computadora a nivel de registros y memoria, permitiendo optimizaciones que los lenguajes de alto nivel no siempre alcanzan.

### 2. Marco Teórico
* **Arquitectura ARM:** Basada en un conjunto de instrucciones reducido (Load/Store).
* **Registros (x0–x30):** Registros de propósito general de 64 bits. En esta práctica, `x0` y `x1` se utilizan para recibir parámetros y `x0` para retornar el resultado.
* **ABI (Application Binary Interface):** Conjunto de reglas que define cómo las funciones de diferentes lenguajes intercambian datos en memoria y registros.

### 3. Desarrollo y Funcionalidades
El proyecto incluye funciones optimizadas en ensamblador para operaciones aritméticas y comparaciones, invocadas desde un script de Python que mide la latencia de respuesta.

---

## Estructura de Archivos y Código Fuente

### 1. **`math_functions.s`** (Ensamblador ARM64)
```asm
// Autor: Pablo Angel Cuevas Marquez
.global asm_suma
.global asm_resta
.global asm_maximo

.text
.align 2

asm_suma:
    add x0, x0, x1
    ret

asm_resta:
    sub x0, x0, x1
    ret

asm_maximo:
    cmp x0, x1
    csel x0, x0, x1, ge
    ret

```
---

### 2. **`wrapper.c `** (Puente en C)
```asm
// Interfaz para funciones ARM64
extern long long asm_suma(long long a, long long b);
extern long long asm_resta(long long a, long long b);
extern long long asm_maximo(long long a, long long b);

long long c_suma(long long a, long long b) { return asm_suma(a, b); }
long long c_resta(long long a, long long b) { return asm_resta(a, b); }
long long c_maximo(long long a, long long b) { return asm_maximo(a, b); }

```

---

### 3. **`main.py `** (Lógica en Python)
```asm
import ctypes
import time

lib = ctypes.CDLL('./libproject.so')

def test_performance():
    # Ejemplo de ejecución y toma de tiempos
    inicio = time.perf_counter_ns()
    res = lib.c_maximo(100, 200)
    fin = time.perf_counter_ns()
    print(f"Resultado Máximo ASM: {res}")
    print(f"Tiempo: {fin - inicio} ns")

if __name__ == "__main__":
    test_performance()
```

---

### 4. **`Makefile  `** (Automatización)
```asm
all:
	as math_functions.s -o math_functions.o
	gcc -shared -fPIC wrapper.c math_functions.o -o libproject.so

clean:
	rm -f *.o *.so

```

---

## Resultados y Análisis de Rendimiento

| Método | Operación | Tiempo (ns) |
| :--- | :--- | :--- |
| Python | Máximo | 2423 ns |
| **Assembly** | **Máximo** | **82000 ns** |

**Análisis:** Aunque el ensamblador es muchisimo más rápido en ciclos de procesamiento masivo, en esta prueba única se observa el **Overhead de Python** y el tiempo de carga del objeto compartido `.so`. El ensamblador es más eficiente cuando se eliminan las capas de gestión de memoria del intérprete en tareas repetitivas.

---

## Evidencias
* **Compilación:** Ejecución exitosa del comando `make` sin errores de enlace.
* **Ejecución:** Pruebas funcionales validadas de suma, resta y máximo.
* **Uso de GDB:**
    * **Comando:** `gdb python3`
    * **Inspección:** Uso de `info registers`
* **Capturas de pruebas:**
<img width="345" height="318" alt="image" src="https://github.com/user-attachments/assets/513572e9-9254-452c-86d5-40d7317da28a" />
<img width="344" height="330" alt="image" src="https://github.com/user-attachments/assets/39158be2-5e77-4174-994a-beae37b74555" />

* **Grabación Asciinema:** https://asciinema.org/a/CaySDcL0kaeyTk3t

---

## 🏁 Conclusiones y Autorreflexión
* **Conclusiones:** Se logró integrar con éxito tres niveles de abstracción (Alto, Medio y Bajo nivel). Se comprendió que el ensamblador es ideal para secciones críticas que requieren control total del hardware, a pesar de que su desarrollo requiere más tiempo que los lenguajes de alto nivel.
* **Autorreflexión:** Para mejorar el proyecto, se podria integrar un procesamiento de arreglos utilizando instrucciones vectoriales (SIMD/Neon) para demostrar una ventaja de rendimiento aún mayor frente a los ciclos convencionales de Python.
