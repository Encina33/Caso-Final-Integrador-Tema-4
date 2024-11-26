# Caso-Final-Integrador-Tema-3


# Análisis de Errores y Correcciones del Código

Este documento detalla los errores identificados en los archivos `.cpp` y `.h` del proyecto, así como las sugerencias para corregirlos. El objetivo es mejorar la calidad, robustez y claridad del código.

---

## Errores en el archivo `.cpp`

### 1. **Fuga de memoria en `ConsoleBox`**
- **Problema:** La instancia global de `ConsoleBox` (`consoleBox`) se crea dinámicamente con `new`, pero nunca se libera.
- **Solución:** Usa un `std::unique_ptr` o crea el objeto de forma estática para que su ciclo de vida esté controlado automáticamente.

### 2. **Nombre de función incorrecto**
- **Problema:** La función implementada como `void cargarscrip()` no coincide con la declaración en el `.h` (`void cargarscrip();`).
- **Solución:** Cambia el nombre en el `.cpp` o en el `.h` para que sean consistentes.

### 3. **Manejo incompleto de errores en archivos**
- **Problema:** Si ocurre un error después de abrir el archivo, el puntero no se libera correctamente.
- **Solución:** Usa RAII para manejar archivos o asegúrate de llamar a `fclose` en todos los caminos posibles (por ejemplo, con un `finally` simulado).

### 4. **Tipo incorrecto en `fread`**
- **Problema:** La variable `c` usa el tipo `int` para almacenar el resultado de `fread`, lo que puede ser inconsistente en algunas plataformas.
- **Solución:** Cambia el tipo de `c` a `size_t`, que es el tipo devuelto por `fread`.

### 5. **Falta de restablecimiento de colores**
- **Problema:** Después de imprimir texto con colores (`fg_blue` y `bg_white`), no se restablecen los colores predeterminados de la consola.
- **Solución:** Agrega un código ANSI para restablecer los colores (`\033[0m`) después de imprimir.

### 6. **Errores genéricos en `fopen`**
- **Problema:** Cuando `fopen` falla, se imprime un mensaje genérico sin información detallada.
- **Solución:** Usa `perror` para imprimir detalles específicos del error del sistema.


---

## Errores en el archivo `.h`

### 1. **Incompatibilidad de nombres**
- **Problema:** El nombre de la función `cargarscrip` en el `.h` no coincide con el nombre en el `.cpp`.
- **Solución:** Asegúrate de que las declaraciones y definiciones sean consistentes.

### 2. **Uso de `using namespace std`**
- **Problema:** Incluir `using namespace std` en un archivo de encabezado puede causar conflictos de nombres en otros archivos.
- **Solución:** Elimina `using namespace std` y usa prefijos explícitos como `std::string`.

### 3. **Visibilidad global no encapsulada**
- **Problema:** Todas las variables y funciones están expuestas globalmente.
- **Solución:** Usa un `namespace` para encapsular las estructuras y funciones, evitando conflictos de nombres.

### 4. **Falta de destructores**
- **Problema:** `ConsoleBox` no tiene destructor, lo que puede ser problemático si en el futuro se añaden recursos dinámicos.
- **Solución:** Agrega un destructor explícito o documenta que no es necesario porque no se usan recursos dinámicos.

### 5. **Definición de constantes como punteros**
- **Problema:** Las constantes en `ColorConsole` son punteros (`const char*`), lo cual puede ser confuso.
- **Solución:** Usa `constexpr const char*` para garantizar que sean constantes verdaderas.

### 6. **Parámetros predeterminados en sobrecarga**
- **Problema:** La función `cargarscrip` tiene parámetros predeterminados, lo que puede causar ambigüedades con la sobrecarga.
- **Solución:** Elimina los valores predeterminados en el encabezado.

---

## Cambios propuestos

### Cambios en `.cpp`
1. **Liberar memoria de `consoleBox`:**
   ```cpp
   ConsoleBox consoleBoxInstance;
   ConsoleBox* consoleBox = &consoleBoxInstance;
Restablecer colores al imprimir:

cpp
Copiar código
cout << script << "\033[0m" << endl;
Manejo robusto de archivos:

cpp
Copiar código
FILE* f = fopen(filename, "rb");
if (!f) {
    perror("Error al abrir el archivo");
    return;
}
Usar size_t para fread:

cpp
Copiar código
size_t c;
Cambios en .h
Eliminar using namespace std: Usa prefijos explícitos en lugar de:

cpp
Copiar código
string
Cambia a:

cpp
Copiar código
std::string
Encapsular todo en un namespace:

cpp
Copiar código
namespace TinyLisp {
    struct ColorConsole { ... };
    struct ConsoleBox { ... };
    extern ConsoleBox* consoleBox;
    void cargarscrip(const char* filename, bool show_script = false);
    void cargarscrip();
}
Resumen
Cambios recomendados
Liberar recursos dinámicos: Evita fugas de memoria.
Encapsular nombres: Usa namespace para prevenir conflictos.
Usar RAII: Asegura la correcta liberación de recursos.
Manejo robusto de errores: Proporciona mensajes de error más detallados.
Consistencia en nombres: Mantén coherencia entre declaraciones y definiciones.
Estos cambios mejorarán la claridad, robustez y mantenibilidad del código.

  #Correción de Encina

----




## Trabajos prácticos
### 1. Carga de scripts en tiny-lisp
El módulo Labmain.cpp define la función load_script(), que se utiliza para cargar un script en la memoria y aplicarle la coloración sintáctica. Esta función se basa en la librería estándar de C.

Implementa las funciones load_script() y load_script(filename, true) en CLion, de tal manera que puedas abrir y leer archivos de texto. El primer método toma el nombre de un archivo como entrada del usuario, mientras que el segundo muestra el contenido del archivo. Ambos métodos deben cargar el script en la consola.

void load_script(const char* filename, bool show_script = false);
void load_script();
### 2. Asegurando la robustez del código
Asegúrate de que tu código pueda manejar diferentes tipos de errores de entrada. Esto incluye, pero no se limita a:

El usuario proporciona un nombre de archivo que no existe.
El archivo proporcionado no se puede abrir por alguna razón.
Se produce un error de lectura durante la lectura del archivo.
Para manejar estos y otros errores potenciales, es posible que desees agregar comprobaciones de errores adicionales y manejar los fallos de manera más sofisticada.

## Notas
Este ejercicio no requiere el uso de funciones seguras específicas de Microsoft (las funciones con sufijo _s). En cambio, deberías usar las funciones estándar de C/C++ disponibles en todas las plataformas y compiladores, como fopen, printf y scanf.


## Rúbrica
Carga de scripts (50 puntos)

Se proporciona una implementación correcta de load_script(const char* filename, bool show_script = false) que carga correctamente el archivo dado y muestra su contenido si show_script es verdadero. (25 puntos)
Se proporciona una implementación correcta de load_script() que solicita al usuario un nombre de archivo, llama a la función load_script(const char* filename, bool show_script = false) y maneja cualquier error de manera apropiada. (25 puntos)
Manejo de errores (50 puntos)

El código tiene un manejo de errores sólido y completo para el caso de que el nombre del archivo proporcionado no exista. (15 puntos)
El código tiene un manejo de errores sólido y completo para el caso de que el archivo proporcionado no se pueda abrir por alguna razón. (15 puntos)
El código tiene un manejo de errores sólido y completo para el caso de que se produzca un error de lectura durante la lectura del archivo. (20 puntos)
Total: 100 puntos

## Propuesta de solución
En CLion, las funciones seguras de la biblioteca estándar de C (*_s funciones) generalmente no están disponibles porque estas son específicas de Microsoft y no son estándar de C/C++. Por lo tanto, en lugar de utilizar funciones como fopen_s, printf_s y scanf_s, puedes usar sus equivalentes no seguros, que son fopen, printf y scanf.

A continuación se muestra cómo se podría reescribir el código para CLion:

#include <iostream>
#include <string>
#include <cstdio>

using namespace std;

struct ColorConsole
{
    static constexpr auto fg_blue = "\033[34m";
    static constexpr auto bg_white = "\033[47m";
};

struct ConsoleBox
{
    void new_text() {/*...*/}
    void set_text(const string &text) { cout << text << endl; }
};

ConsoleBox *consoleBox = new ConsoleBox; // suponemos que ya está inicializado

void load_script(const char* filename, bool show_script = false)
{
    string script;
    FILE* f = nullptr;
    try
    {
        f = fopen(filename, "rb");
        if (!f)
        {
            cerr << "error de apertura de " << filename << endl;
            return;
        }

        int c;
        char buf[4001];
        while ((c = fread(buf, 1, 4000, f)) > 0)
        {
            buf[c] = 0;
            script.append(buf);
        }
        fclose(f);
        f = nullptr;

        if (show_script)
        {
            cout << ColorConsole::fg_blue << ColorConsole::bg_white;
            cout << script << endl;
        }
        consoleBox->new_text();
        consoleBox->set_text(script);
    }
    catch (...)
    {
        cerr << "error durante la lectura del archivo" << endl;
        if(f)
            fclose(f);
    }
}

void load_script()
{
    char filename[500];
    printf("Archivo: ");
    scanf("%499s", filename);
    load_script(filename, true);
}
Este código debería compilar y funcionar en CLion sin necesidad de agregar la directiva _CRT_SECURE_NO_WARNINGS, ya que no se están utilizando las versiones seguras específicas de Microsoft de las funciones de la biblioteca estándar de C.

Por último, ten en cuenta que este código no realiza una comprobación completa de errores y no es robusto contra varios tipos de errores de entrada. Dependiendo de tu caso de uso específico, es posible que desees agregar comprobaciones de errores adicionales y manejar los fallos de manera más sofisticada.

