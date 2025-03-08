# Proyecto Final de Robótica

**Integrantes:**
- Catalina Criollo
- Cristian Cuestas
- Diego Malagón
- Johan López

## Modelo

# Cinemática Directa

## Tabla de parámetros DH del Phantom

| Link | αᵢ  | a  | d  | θ  | offset |
|------|-----|----|----|----|--------|
| 1    | 90° | 0  | L1 | q1 | 0      |
| 2    | 0   | L2 | 0  | q2 | 90°    |
| 3    | 0   | L3 | 0  | q3 | 0      |
| 4    | 0   | L4 | 0  | q4 | 0      |

## Visualización en Matlab

### Código

```matlab
l = [14.5, 10.7, 10.7, 9]; % Longitud de los eslabones
% Definición de links y robot
L(1) = Link('revolute','alpha',pi/2,'a',0, 'd',l(1),'offset',0, 'qlim',[-3*pi/4 3*pi/4]);
L(2) = Link('revolute','alpha',0, 'a',l(2),'d',0, 'offset',pi/2, 'qlim',[-3*pi/4 3*pi/4]);
L(3) = Link('revolute','alpha',0, 'a',l(3),'d',0, 'offset',0, 'qlim',[-3*pi/4 3*pi/4]);
L(4) = Link('revolute','alpha',0, 'a',l(4),'d',0, 'offset',0, 'qlim',[-3*pi/4 3*pi/4]);

PhantomX = SerialLink(L,'name','Px');

% Orientación de la herramienta
PhantomX.tool = [0 0 1 l(4); -1 0 0 0; 0 -1 0 0; 0 0 0 1];

PhantomX.teach()
```

# Cálculo de Cinemática Directa

## Aplicación de la Cinemática Directa en la Solución

_Incluyendo resultados numéricos que validen el cálculo de la posición y orientación del efector final._

## Cinemática Inversa

Describir el proceso de cálculo y aplicación de la cinemática inversa para determinar los valores articulares a partir de una posición objetivo, mostrando resultados numéricos que evidencien la solución obtenida.

## Solución Creada

Descripción de la solución creada, el proceso de preparación y programación, a manera de bitácora del desarrollo del proyecto. No olviden ser detallados y describir el proceso como quieren que sea visto por sus pares.

## Diagrama de Flujo de las Acciones de los Robots

Diagrama de flujo de las acciones de los robots, con sus respectivas descripciones.

Descripción, plano de planta y descripción detallada de la teleoperación de los robots y el trabajo colaborativo de los mismos.

# Código

Código en Matlab o Python comentado y discutido del módulo utilizado para el desarrollo del proyecto.

# Comparación de la Teleoperación Manual y Automática

Comparación objetiva de la teleoperación manual y automática realizada por los robots.

# Video Presentación

Video tipo presentación con duración entre 7 a 10 minutos, deberá incluir el clip de introducción de LabSIR, seguido de un banner de la Universidad Nacional de Colombia, autores, profesores, curso, año, etc.

**Intro LabSIR, el video debe incluir:**
- Video de implementación del proyecto donde todos los integrantes del grupo participen y expliquen la solución planteada.
- Video de la simulación en Rviz y/o CoppeliaSim y Python o Matlab (pueden usar las herramientas de la librería de Peter Corke).

# Errores Presentados

A continuación, se describen los errores y problemas presentados en la realización del laboratorio, junto con las soluciones posibles a estos.

# Instalando ROS2

La descarga e instalación de ROS2 a través de mamba y conda fue interrumpida en varias ocasiones debido a cortes en la conexión a internet. Esto obligaba a reiniciar el proceso desde el inicio.

## Soluciones:

### Descarga alternativa y preconfiguración:
- En lugar de descargar directamente desde la terminal, se puede optar por descargar los paquetes ROS2 manualmente en otro equipo con mejor conexión y luego transferirlos mediante un USB o una red local.
- En Windows, los paquetes pueden descargarse de antemano desde la página oficial de ROS2 y luego instalarlos manualmente.

### Uso de gestores de descargas:
- `wget -c` permite reanudar descargas interrumpidas.
- En Windows, herramientas como IDM (Internet Download Manager) pueden ayudar a gestionar mejor la descarga.

### Verificar requisitos de instalación:
- Algunos errores pueden deberse a la falta de dependencias previas en el sistema, como `vc_redist` en Windows o `colcon` en Ubuntu. Se recomienda seguir las instrucciones específicas para la versión de ROS2 elegida.

# Instalación de CoppeliaSim

CoppeliaSim es un simulador potente, pero su integración con ROS2 en Windows puede ser complicada debido a dependencias y problemas de compatibilidad. Entre los errores detectados se encuentran los siguientes:

- **La licencia de Coppelia EDU tardó 15 días en llegar**, lo que retrasó el proceso.  
  **Solución:** Solicitar el código con anticipación o verificar si hay versiones trial que permitan iniciar sin espera.

- **Problema con ROS2.dll:** Aunque el archivo `ROS2.dll` ya venía con la instalación, CoppeliaSim no lo reconocía. Se tuvo que recurrir a un repositorio externo para obtenerlo, pero este no funcionó correctamente.  
  **Solución:** Asegurar que la biblioteca `ROS2.dll` está en la ruta correcta y añadirla manualmente a la variable de entorno. Comprobar la compatibilidad de la versión de `ROS2.dll` con la instalación de ROS2.

- **Errores al compilar paquetes de ROS2 para la conexión con Coppelia:**
  - **Error de direccionamiento:** El sistema no encontraba ciertos archivos.
  - **Errores en CMake al compilar:** El monitor indicaba que la instalación de ROS2 estaba incompleta.
  - **Falta de dependencias de Python:** Algunas dependencias eran incompatibles con la versión instalada.

## Soluciones:
- **Error de direccionamiento:** Definir correctamente la variable de entorno con la ruta de los archivos necesarios.
- **Errores en CMake:** Asegurarse de que `colcon` y los compiladores necesarios están correctamente instalados.
- **Falta de dependencias en Python:** Instalar manualmente las dependencias necesarias con:
  ```bash
  pip install numpy scipy matplotlib xacro
# Compilación de paquetes para la comunicación Phantom X - ROS

## Problemas encontrados
- Falta de las librerías `os` y `xacro`: Al intentar ejecutar los scripts, la terminal indicaba que estas librerías no estaban instaladas.
- Errores de permisos en Windows: Algunos scripts requerían permisos elevados y fallaban si no se ejecutaban como administrador.
- Confusión en la ruta de los archivos ejecutables: Se asumía que la ruta debía apuntar a la carpeta del ejecutable, cuando en realidad debía incluir el nombre completo del ejecutable.
- Error de direccionamiento de CoppeliaSim: En Windows, la presencia de espacios en los nombres de las carpetas impedía que el programa se ejecutara correctamente.

## Soluciones
- **Para `os` y `xacro`**: Instalar manualmente las librerías en Python:
  ```sh
  pip install xacro
  ```
  Asegurar que Python 3.11 está correctamente instalado y en el PATH.
- **Para permisos en Windows**: Ejecutar la terminal como administrador:
  ```sh
  cmd /c start /b python script.py
  ```
- **Para errores en la ruta de archivos ejecutables**: Comprobar que la ruta proporcionada en el código sea la correcta, incluyendo el ejecutable.
- **Para el error en el direccionamiento de CoppeliaSim**:
  - Se identificó que el error podría deberse a que se presentaban algunos espacios en las carpetas que contenían `coppelia`, por lo que se intentó instalar en otra ubicación. Sin embargo, al instalar el programa, no se permitía cambiar su ubicación, por lo que esta solución se descartó.
  - Finalmente, este error se debía a un problema de sintaxis por el sistema operativo. La dirección en el código debía empezar con la palabra `start` y el archivo en cuestión debía estar con la terminación `.exe`.

## Migración a Ubuntu
**Razón de la migración**: Windows presentó múltiples problemas con permisos y compatibilidad. La documentación de CoppeliaSim en ROS2 es escasa y está centrada en Ubuntu.

ROS2 es una herramienta poderosísima para aplicaciones en ingeniería en especial en el área de robótica, sin embargo, su completo entendimiento requiere de mucho tiempo y práctica, esto entonces deja en evidencia la importancia que tiene aprender a usar este entorno para de esta manera mantenerse competitivo en el mercado laboral.
