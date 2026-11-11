# MODGAMES ZSTD Container

**MODGAMES ZSTD Container** es una herramienta para Windows orientada al empaquetado de juegos de PS5 mediante **AMPR ZSTD**, con una interfaz gráfica simple y enfocada en un único flujo de trabajo.

El programa analiza la carpeta del juego, muestra información relevante del título, detecta posibles backports, prepara los paquetes AMPR ZSTD, instala el runtime requerido y genera un contenedor final con extensión `.zstd`.

> **Estado del proyecto:** experimental. Se recomienda conservar siempre una copia original del juego antes de realizar cualquier conversión.

---

## Características

- Interfaz gráfica moderna y responsive.
- Detección automática de:
  - nombre del juego;
  - Title ID;
  - versión;
  - firmware declarado;
  - cantidad de archivos;
  - portada;
  - posibles backports.
- Empaquetado mediante **AMPR ZSTD**.
- Niveles ZSTD configurables del 1 al 22.
- Presets:
  - **Rápido**;
  - **Medio**;
  - **Máximo**.
- División de datos en chunks independientes.
- Uso de datos RAW cuando la compresión no reduce el tamaño.
- Generación y verificación de los índices y paquetes AMPR ZSTD.
- Copia automática del runtime ZAMPR a `fakelib/libSceAmpr.sprx`.
- Sobrescritura automática del runtime anterior cuando sea necesario.
- Verificación del runtime mediante SHA-256 antes de crear el contenedor final.
- Progreso en tiempo real.
- Velocidad y estado de la tarea.
- Registro técnico para diagnóstico.
- Verificación de contenedores ya creados.
- Tema oscuro/claro.
- Compilación en un único ejecutable mediante **Nuitka Onefile**.

---

## Flujo de trabajo

```text
Carpeta del juego
        ↓
Análisis del juego
        ↓
Preparación AMPR ZSTD
        ↓
Generación de índices y paquetes
        ↓
Verificación AMPR ZSTD
        ↓
Copia de zampr/libSceAmpr.sprx
        ↓
fakelib/libSceAmpr.sprx
        ↓
Verificación SHA-256
        ↓
Creación del contenedor .zstd
        ↓
Verificación final
```

La creación del contenedor final **no comienza** si el runtime requerido no pudo copiarse y verificarse correctamente.

---

## Runtime ZAMPR

El proyecto incluye el runtime requerido dentro de:

```text
zampr/
└── libSceAmpr.sprx
```

Durante el proceso, la herramienta crea `fakelib/` si es necesario y copia el runtime a:

```text
fakelib/libSceAmpr.sprx
```

Si ya existe un archivo con ese nombre, será reemplazado.

La interfaz informa visualmente cuando el runtime ha sido copiado y verificado correctamente.

---

## Detección de Backport

MODGAMES ZSTD Container intenta identificar si el juego utiliza un backport comparando información como:

- SDK detectado en `eboot.bin`;
- firmware/SDK declarado por el juego;
- módulos adicionales presentes en `fakelib/`.

Ejemplo:

```text
BACKPORT detectado
EBOOT SDK 4.00 frente a declaración 13.20
4 módulos adicionales en fakelib
```

`libSceAmpr.sprx` no se utiliza por sí solo como evidencia de backport para evitar falsos positivos.

La detección es informativa y no modifica los archivos del juego.

---

## Requisitos

### Ejecutar desde código fuente

- Windows 10 u 11 de 64 bits.
- Python.
- PySide6.
- zstandard.

Instala las dependencias con:

```bat
py -m pip install -r requirements.txt
```

Después ejecuta:

```bat
EJECUTAR.bat
```

O directamente:

```bat
py app.py
```

---

## Compilar un único EXE

El proyecto utiliza **Nuitka** para generar un ejecutable Onefile.

Instala las dependencias:

```bat
py -m pip install -r requirements.txt
```

Compila con:

```bat
COMPILAR_EXE.bat
```

O:

```bat
py build.py
```

La salida esperada es:

```text
dist/
└── MODGAMES_ZSTD_CONTAINER.exe
```

El ejecutable incluye los recursos necesarios de la aplicación, la interfaz QML, assets, módulos ZSTD y el runtime ubicado en `zampr/`.

---

## Estructura principal

```text
MODGAMES_ZSTD_CONTAINER/
│
├── app.py
├── build.py
├── requirements.txt
│
├── engines/
│   ├── zstd/
│   └── zstd_container/
│
├── zampr/
│   └── libSceAmpr.sprx
│
├── qml/
│   └── Main.qml
│
├── assets/
│   ├── modgames.ico
│   └── modgames.png
│
└── módulos auxiliares
```

El proyecto está organizado de forma modular para facilitar el mantenimiento y permitir que el motor ZSTD, la interfaz y las herramientas auxiliares evolucionen de forma independiente.

---

## Importante

- Trabaja siempre sobre una **copia** del juego.
- No elimines el origen hasta verificar el resultado.
- Un proceso interrumpido puede dejar archivos temporales o parciales.
- El ahorro final depende del contenido de cada juego.
- Archivos ya comprimidos pueden ofrecer poca o ninguna reducción adicional.
- Este proyecto se encuentra en desarrollo y necesita pruebas en diferentes juegos y configuraciones.

---

## Uso responsable

Esta herramienta está destinada a investigación, desarrollo, preservación y uso con contenido que poseas legalmente o para el cual tengas autorización.

El proyecto no incluye juegos ni contenido propiedad de terceros.

---

## Créditos

### ps5-dump-forge — quer3q

Proyecto de referencia y base técnica importante para el trabajo con dumps de PS5 y empaquetado AMPR ZSTD.

https://github.com/quer3q/ps5-dump-forge

`ps5-dump-forge` es un toolkit multiplataforma para trabajar con dumps de juegos de PS5 e incluye soporte para empaquetado de assets ZSTD y LZ4.

### ampr-emu-ng — quer3q

Runtime utilizado como referencia y componente fundamental para la lectura de paquetes AMPR ZSTD en PS5.

https://github.com/quer3q/ampr-emu-ng

`ampr-emu-ng` es un hard fork de `ampr_emu` orientado a rendimiento y estabilidad, con soporte para juegos ZSTD, LZ4 y sin paquetes.

### DLPS Team

Agradecimientos al **DLPS Team** por su trabajo, investigación y aportes a la comunidad de PS5.

https://x.com/DLPSTeam

---

## Licencias y proyectos de terceros

Los proyectos `ps5-dump-forge` y `ampr-emu-ng` se publican bajo **GPL-3.0**.

Si distribuyes versiones de MODGAMES ZSTD Container que incorporen, modifiquen o deriven código o binarios de estos proyectos, debes conservar los avisos correspondientes y cumplir sus términos de licencia.

Revisa siempre los archivos `LICENSE` y los avisos de terceros incluidos en cada proyecto original.

---

## Agradecimientos

Gracias a todos los desarrolladores e investigadores de la comunidad PS5 que publican documentación, herramientas y código abierto que permiten seguir aprendiendo y mejorando este tipo de proyectos.

---

## Autor

**MODGAMES44**

Proyecto desarrollado para investigación, pruebas y desarrollo de herramientas relacionadas con empaquetado ZSTD en PS5.
