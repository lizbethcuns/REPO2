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
# Abre 3 terminales
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
#
#
#
#
#
#
#
