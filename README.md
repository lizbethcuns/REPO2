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

ros2 pkg list | grep turtlebot4

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

~/turtlebot4_ws/src/turtlebot4/turtlebot4_description/urdf/sensors/rplidar.urdf.xacro

# ---------------------------------->Lanzar los mundos<---------------------------------------
# es común encontrarse con un error relacionado con la solicitud de mundos: "Requesting list of worlds"
# Fix para el hang en "Requesting list of worlds" (desactiva descargas online automáticas)
export IGN_GAZEBO_OFFLINE=1
export GZ_SIM_RESOURCE_PATH=~/.gz/fuel/fuel.ignitionrobotics.org/openrobotics/models  # Usa recursos locales si ya los descargaste

# Abre 2 terminales
# Terminal 1
# Siempre asegúrate de tener esto (evita el freeze la primera vez)
export IGN_IP=127.0.0.1

# 1. Mundo WAREHOUSE con modelo ESTÁNDAR → 2 puntos
ros2 launch turtlebot4_ignition_bringup turtlebot4_ignition.launch.py world:=warehouse

# Deja la simulación en PLAY (▶️) y mueve la cámara para que se vea bien el robot.    PARA ROTAR SHIFT DERECHA MANTENER PRESIONADO + CON CLIC DERECHA MANTENER PRESIONADO

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
#
#
#
#
#
#
#
#
#
