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

# ---------------------------------->Lanzar los mundos<---------------------------------------
# Abre 2 terminales
# Terminal 1
# Siempre asegúrate de tener esto (evita el freeze la primera vez)
export IGN_IP=127.0.0.1

# 1. Mundo WAREHOUSE con modelo ESTÁNDAR → 2 puntos
ros2 launch turtlebot4_ignition_bringup turtlebot4_ignition.launch.py world:=warehouse

# Deja la simulación en PLAY (▶️) y mueve la cámara para que se vea bien el robot.    PARA ROTAR SHIFT DERECHA MANTENER PRESIONADO + CON CLIC DERECHA MANTENER PRESIONADO

# Ver el robot en RViz:
ros2 launch turtlebot4_viz view_robot.launch.py

# Ver el modelo del robot:
ros2 launch turtlebot4_viz view_model.launch.py

# Visualización del Grafo de Nodos con 
rqt_graph

# Visualizar el LiDAR
Haz clic en los tres puntitos ⋯ (arriba a la derecha)
Selecciona “Visualize Lidar”
Dale a “Refresh”
Elige el sensor rplidar
→ Deberías ver el láser rojo/azul escaneando el entorno.

# Si el robot se vuelve loco o se para (modo seguro), simplemente cierra todo y vuelve a lanzar el mundo con este comando (solución al problema de tus amigos):
ros2 launch turtlebot4_ignition_bringup turtlebot4_ignition.launch.py world:=warehouse lidar:=cpu 
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
#
