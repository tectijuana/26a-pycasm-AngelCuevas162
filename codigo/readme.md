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
