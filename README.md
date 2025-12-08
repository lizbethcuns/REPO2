Aclaracion importante 
Instalación desde fuente (compilando código): Esto es más avanzado, si clonaste repos de GitHub (como ros2/ros_core) y usaste colcon build en un workspace (una carpeta como ~/ros2_ws). En este caso, usa el source del workspace:
source ~/tu_workspace/install/setup.bash  # Reemplaza "tu_workspace" por el nombre real, ej: ~/ros2_humble_ws

# Cuál source usar?
# Comando: source ~/turtlebot4_ws/install/setup.bash
# REPO2
# REPARA EL ROS2 HUMBLE 
sudo apt update
sudo apt install --reinstall ros-humble-ros-core ros-humble-ros-base ros-humble-desktop -y
sudo apt install -f -y
source /opt/ros/humble/setup.bash
# Cuando termine, ejecuta -------> Si te sale humble → ¡ya está arreglado!
ros2 topic list
# Después prueba: Si ya no sale el mensaje de “crash” ni “broken pipe”, ¡listo! Ya tienes ROS 2 funcionando perfectamente
ros2 topic list

# Instalación de Paquetes de Simulación". El repo recomienda clonar y compilar desde fuente (no solo paquetes Debian), así que seguiremos eso para ser exactos con sus recomendaciones.
mkdir -p ~/turtlebot4_ws/src
cd ~/turtlebot4_ws/src
git clone https://github.com/turtlebot/turtlebot4_simulator.git -b humble
git clone https://github.com/turtlebot/turtlebot4_desktop.git -b humble

# Instala dependencias con rosdep (esto baja todo lo que falta automáticamente):
cd ~/turtlebot4_ws
source /opt/ros/humble/setup.bash
rosdep update
rosdep install --from-paths src --ignore-src -r -y

# Si te pide sudo para algún paquete, dale enter con tu contraseña.

# Compila el workspace (esto construye todo):
colcon build --symlink-install

# Source el workspace (para que ROS lo reconozca; agrégalo a tu bashrc para siempre):
source ~/turtlebot4_ws/install/setup.bash
echo 'source ~/turtlebot4_ws/install/setup.bash' >> ~/.bashrc
source ~/.bashrc

# ------------------------------------------------> PASOS PARA CORREGUIR EL WORKSPACE <-----------------------------------------------
#no me aparecia el turtlebot4_description 
# Ir a la carpeta del Workspace 
cd ~/turtlebot4_ws/src
# Clonar el repositorio que falta 
git clone https://github.com/turtlebot/turtlebot4.git -b humble
# Instalar dependencias
cd ~/turtlebot4_ws
rosdep install --from-paths src --ignore-src -r -y
# Compilar
colcon build --symlink-install
source install/setup.bash
# para evitar problemas despues de compilar 
source ~/turtlebot4_ws/install/setup.bash
# verificar que existe el paquete 
ros2 pkg list | grep turtlebot4

# ------------------------------------------------> UBICACION DEL ARCHIVO PARA CAMBIAR EL RANGO  <-----------------------
~/turtlebot4_ws/src/turtlebot4/turtlebot4_description/urdf/sensors/rplidar.urdf.xacro

# ---------------------------------->Lanzar los mundos<---------------------------------------
# es común encontrarse con un error relacionado con la solicitud de mundos: "Requesting list of worlds"
# Fix para el hang en "Requesting list of worlds" (desactiva descargas online automáticas)
export IGN_GAZEBO_OFFLINE=1
export GZ_SIM_RESOURCE_PATH=~/.gz/fuel/fuel.ignitionrobotics.org/openrobotics/models  # Usa recursos locales si ya los descargaste

# ---------------------------------> Abre 2 terminales <---------------------------------------
# Terminal 1
# Siempre asegúrate de tener esto (evita el freeze la primera vez)
export IGN_IP=127.0.0.1

# 1. Mundo WAREHOUSE con modelo ESTÁNDAR
ros2 launch turtlebot4_ignition_bringup turtlebot4_ignition.launch.py world:=warehouse

# Deja la simulación en PLAY (▶️) y mueve la cámara para que se vea bien el robot.    PARA ROTAR SHIFT DERECHA MANTENER PRESIONADO + CON CLIC DERECHA MANTENER PRESIONADO

# ---------> control del TurtleBot 4 DESDE LA TERMINAL <--------------------------------------------------

Ejecuta el source (elige uno según tu instalación):text
source ~/turtlebot4_ws/install/setup.bash
Luego, lanza la simulación (todo en una línea):
ros2 launch turtlebot4_ignition_bringup turtlebot4_ignition.launch.py model:=standard world:=warehouse
Abre una terminal nueva para control:
Abre otra terminal (Ctrl+Alt+T o desde menú).
Nuevamente, ejecuta el source (mismo que en el paso 1):
source ~/turtlebot4_ws/install/setup.bash
Ahora sí, lista los tópicos (todo en una línea):text
ros2 topic list
Deberías ver /cmd_vel y otros como en el ejemplo del repo.
Envía el comando de movimiento:
En esa misma terminal nueva (después del source), publica el comando (todo en una sola línea):
ros2 topic pub /cmd_vel geometry_msgs/msg/Twist '{linear: {x: 0.7, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}' --rate 10
Si no se mueve, haz el undock primero (todo en una línea):
ros2 action send_goal /undock irobot_create_msgs/action/Undock "{}"
Luego repite el pub. El robot debería avanzar en Gazebo.
# ------------------------------------------------> CARPETA DE LOS SENSORES <-----------------------------------------------
/opt/ros/humble/share/turtlebot4_description/urdf/sensors
# ------------------------------------------------> ACTIVAR EL LIDAR <-----------------------------------------------
# Edita el Archivo con Sudo
sudo nano /opt/ros/humble/share/irobot_create_description/urdf/create3.urdf.xacro

#Busca la línea ~301 (como en tu captura): <render_engine>ogre</render_engine>
#Cámbiala a: <render_engine>ogre2</render_engine>
#Guarda: Ctrl+O (escribe), Enter (confirma), Ctrl+X (salir).

# Después del cambio, sourcea tu workspace:
source ~/turtlebot4_ws/install/setup.bash
# Rebuild si es necesario
colcon build --packages-select irobot_create_description

# ------------------------------------------------>ADICIONAL <-----------------------------------------------
# Primero lanzar SLAM 
ros2 launch turtlebot4_navigation slam.launch.py
# ------------------------------------------------>ADICIONAL <-----------------------------------------------
# COntrolar desde Terminal
# Ver el robot en RViz:
ros2 launch turtlebot4_viz view_robot.launch.py

# Ver el modelo del robot:
ros2 launch turtlebot4_viz view_model.launch.py

# Visualización del Grafo de Nodos con 
rqt_graph

# Visualizar el LiDAR
#Haz clic en los tres puntitos ⋯ (arriba a la derecha)
#Selecciona “Visualize Lidar”
#Dale a “Refresh”
#Elige el sensor rplidar
#--------------------> Deberías ver el láser rojo/azul escaneando el entorno.

# Si el robot se vuelve loco o se para (modo seguro), simplemente cierra todo y vuelve a lanzar el mundo con este comando (solución al problema de tus amigos):
ros2 launch turtlebot4_ignition_bringup turtlebot4_ignition.launch.py world:=warehouse lidar:=cpu 

# --------------->ADICIONALES<---------------------------
#Formas de girar/mover la cámara en Gazebo (elige la que más te guste):

#Con el mouse (la más rápida y cómoda)
#Clic izquierdo + arrastrar → giras la cámara (mira a los lados, arriba, abajo).
#Rueda del mouse → acercas/alejas (zoom).
#Clic derecho + arrastrar → mueves la cámara de lado (pan).
#Rueda presionada + arrastrar → órbita rápida alrededor del robot.

#Con el teclado + mouse
#Mantén presionada la tecla Shift y arrastra con clic izquierdo → giras más rápido.

#Modo “Orbit” (el mejor para grabar)
#En la barra superior de herramientas (donde están los iconos de mano, lupa, etc.) haz clic en el icono que parece una flecha circular ↺ (es el 4º o 5º icono).
#Ahora solo con mover el mouse (sin clic) la cámara orbita alrededor del robot automáticamente. Perfecto para tu video.

#Vista frontal directa del robot (si quieres que quede bonito en el video)
#Haz doble clic sobre el robot en la lista de la izquierda (donde dice “turtlebot4_standard” o similar).
#O selecciona el robot en la lista → clic derecho → “View → Follow” o “View → Orbit”.


#Prueba ahora mismo el clic izquierdo + arrastrar y verás que puedes girar 360° y ponerte justo enfrente del robot sin problema.
# --------------------------------> finaliza <-------------------------
# ---------------------------------------------------------------> CUANDO CIERRO EL PROGRAMA  <-----------------------------------------------------------
# Source el Workspace
source ~/turtlebot4_ws/install/setup.bash
# Lanza el Mapa/Simulación
ros2 launch turtlebot4_ignition_bringup turtlebot4_ignition.launch.py model:=standard world:=warehouse

# (Si quieres otro mapa, cambia world:=warehouse por depot o maze.)

# Si ves errores (ej: sobre IGN_IP) agrega antes: 
export IGN_IP=127.0.0.1. 

# lista tópicos:
ros2 topic list

# Para mover
ros2 topic pub /cmd_vel geometry_msgs/msg/Twist '{linear: {x: 0.7, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}' --rate 10
# --------------------------------> SOLUCION A PROBLEMAS <-------------------------
# Si está docked, undock
ros2 action send_goal /undock irobot_create_msgs/action/Undock "{}"

# Si sale error (ej: package not found), rebuild
cd ~/turtlebot4_ws && colcon build --symlink-install && source install/setup.bash
# --------------------------------><-------------------------
#
#
#
#
#
#
#
#
#
#
#
