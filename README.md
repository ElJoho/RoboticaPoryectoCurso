# Proyecto Final de Robótica

**Integrantes:**
- Catalina Criollo
- Cristian Cuestas
- Diego Malagón
- Johan López

## Modelo
![Esquema Phantom](esquemaPhantom1.png)
# Cinemática Directa

## Tabla de parámetros DH del Phantom

| Link | αᵢ  | a  | d  | θ  | offset |
|------|-----|----|----|----|--------|
| 1    | 90° | 0  | L1 | q1 | 0      |
| 2    | 0   | L2 | 0  | q2 | 90°    |
| 3    | 0   | L3 | 0  | q3 | 0      |
| 4    | 0   | L4 | 0  | q4 | 0      |

## Visualización en Matlab

![Matlab Phantom](matlabPhantom1.png)
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

![Inversa](INVERSA.jpg)

Matriz de Transformación Homogénea

```math
T_T = \begin{bmatrix} n_x & o_x & a_x & x_T \\ n_y & o_y & a_y & y_T \\ n_z & o_z & a_z & z_T \\ 0 & 0 & 0 & 1 \end{bmatrix}
```

donde \( x_T, y_T, z_T \) representan la posición del efector final y la orientación está dada por los vectores de la matriz de rotación.
   
El primer ángulo $q_1$ se obtiene a partir de la proyección de la posición del efector final en el plano XY:

```math
q_1 = \text{atan2}(y_T, x_T)
```
  
Dado que el robot cuenta con un efector final con orientación determinada, se calcula la posición de la muñeca restando la contribución del último eslabón:

```math
PosW = \begin{bmatrix} x_W \\ y_W \\ z_W \end{bmatrix} = \begin{bmatrix} x_T \\ y_T \\ z_T \end{bmatrix} - l_4 \begin{bmatrix} a_x \\ a_y \\ a_z \end{bmatrix}
```

donde $l_4$ es la longitud del último eslabón.

Se define la distancia radial \( r \) y la altura \( h \) de la muñeca respecto a la base:

```math
r = \sqrt{x_W^2 + y_W^2}, \quad h = z_W - l_1
```

```math
\theta_3 = \cos^{-1} \left( \frac{r^2 + h^2 - l_2^2 - l_3^2}{2 l_2 l_3} \right)
```

El ángulo $q_2$ se determina considerando la geometría del sistema:

```math
q_2 = -\frac{\pi}{2} + \text{atan2}(h, r) - \text{atan2}(l_3 \sin(\theta_3), l_2 + l_3 \cos(\theta_3))
```

El ángulo $q_3$ está dado por:

```math
q_3 = \theta_3
```

Se utiliza la matriz de rotación del efector para determinar el ángulo de pitch:

```math
pitch = \text{atan2}(R_{31}, R_{11})
```

Finalmente, el ángulo de la muñeca se obtiene como:

```math
q_4 = pitch - q_2 - q_3
```

## Solución Creada

Descripción de la solución creada, el proceso de preparación y programación, a manera de bitácora del desarrollo del proyecto. No olviden ser detallados y describir el proceso como quieren que sea visto por sus pares.

## Diagrama de Flujo de las Acciones de los Robots

Diagrama de flujo de las acciones de los robots, con sus respectivas descripciones.

Descripción, plano de planta y descripción detallada de la teleoperación de los robots y el trabajo colaborativo de los mismos.

# Código

## Conexión de un Control de PS4 a ROS 2 y Lectura de Joystick/Botones

Este repositorio explica cómo conectar un control de PlayStation 4 (PS4) a un sistema basado en ROS 2 para leer los valores de los joysticks y botones.

### Requisitos

Antes de comenzar, se debe contar con lo siguiente:
- Un sistema con **ROS 2** instalado (Iron, Humble, o Foxy recomendado).
- Un control de **PS4** (DualShock 4).
- Un adaptador **Bluetooth** o cable USB.
- Paquetes `joy` y `teleop_twist_joy` instalados en ROS 2:

```bash
sudo apt install ros-<distro>-joy ros-<distro>-teleop-twist-joy
```
(Reemplazar `<distro>` con la versión de ROS 2 que se esté utilizando, como `humble` o `foxy`).

## Paso 1: Conectar el Control de PS4

El control de PS4 se puede conectar de dos maneras:

### Opción 1: Conexión por Bluetooth
1. Se debe poner el control en modo de emparejamiento presionando los botones **PS** + **Share** hasta que la luz parpadee rápidamente.
2. En el PC, ejecutar:
   ```bash
   bluetoothctl
   ```
3. Luego, en la consola de `bluetoothctl`, escribir:
   ```bash
   scan on
   ```
   y buscar la dirección MAC del control (ejemplo: `XX:XX:XX:XX:XX:XX`).
4. Conectar con:
   ```bash
   pair XX:XX:XX:XX:XX:XX
   connect XX:XX:XX:XX:XX:XX
   trust XX:XX:XX:XX:XX:XX
   ```
5. Salir del modo interactivo escribiendo:
   ```bash
   exit
   ```

### Opción 2: Conexión por USB
El control se conectó por medio de un cable USB al PC.

## Paso 2: Lanzar el Nodo `joy`

Para comenzar a leer los datos del control, se debe ejecutar el siguiente comando:
```bash
ros2 launch teleop_twist_joy teleop-launch.py joy_config:='ps3'
```
⚠️ **Nota:** Aunque el control es un PS4, el argumento `ps3` es el que mejor funciona en muchas configuraciones de ROS 2.

Este comando inicia el nodo `joy_node`, que captura la información de los botones y joysticks.

## Paso 3: Leer los Datos del Control
Para visualizar los valores del joystick y los botones en tiempo real, se debe ejecutar:
```bash
ros2 topic echo /joy
```
Este comando mostrará un mensaje con la siguiente estructura:
```yaml
header:
  stamp:
    sec: 123456
    nanosec: 789000000
  frame_id: ''
axes: [0.0, 0.0, -1.0, 0.0, 0.0, 0.0]
buttons: [0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0]
```
- `axes`: Valores de los joysticks.
- `buttons`: Estado de los botones (1 = presionado, 0 = no presionado).

## Paso 4: Detener el Nodo
Para detener la lectura del control, se debe usar:
```bash
Ctrl + C
```
Esto cerrará el nodo de ROS 2.

## Referencias
- [Documentación oficial de joy para ROS 2](https://docs.ros.org/en/iron/p/joy/)
- [Teleop Twist Joy](https://github.com/ros2/teleop_twist_joy)

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
