# Install basic environment
## nv env version (JetPack 5.1.4 ubuntu20-04)

## 0. prepare folder for install
```shell
cd 
mkdir -p libs
mkdir -p driver/livox
mkdir -p driver/realsense
```

## 1. ros & vscode install
```shell
cd 
wget http://fishros.com/install -O fishros && . fishros
```

## 1.1 zsh 安装（Ubuntu体验++）
```shell
sudo apt install zsh
chsh -s /bin/zsh

# 安装 Oh My Zsh
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
# 以上命令可能不好使，可使用如下两条命令
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh
bash ./install.sh

# 安装zsh插件 自动提示和语法高亮
cd

git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

### 1.1.1 .zshrc 修改
```shell
# 设置字体模式以及配置命令行的主题，语句顺序不能颠倒
ZSH_THEME="ys"

# 启动错误命令自动更正
ENABLE_CORRECTION="true"

# 在命令执行的过程中，使用小红点进行提示
COMPLETION_WAITING_DOTS="true"

# 启用已安装的插件
plugins=(
  git zsh-autosuggestions zsh-syntax-highlighting
)

alias cc="clear"
alias px4="sh /home/nv/WS_MAIN/src/Gdrone250Software/script/px4_lio.sh"
alias main="roslaunch mission_fsm drone_real.launch"
alias bridge="roslaunch mission_fsm bridge_drone.launch"
alias ctrl="roslaunch px4ctrl run_ctrl.launch"
alias cam="source ~/driver/realsense/ws_realsense/devel/setup.zsh && roslaunch realsense2_camera rs_camera.launch"

export PATH=/usr/local/cuda-11.4/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-11.4/lib64:$LD_LIBRARY_PATH

# >>> fishros initialize >>>
source /opt/ros/noetic/setup.zsh
# <<< fishros initialize <<<
source # todo : livox dir
sourde # todo : main program dir
```

### 1.1.2 install tmux 
```shell
sudo apt-get install tmux

# config tmux (hot-key config!) write these into ~/.tmux.conf 
# remap prefix from 'C-b' to 'C-x'
unbind C-b
set-option -g prefix C-x
bind-key C-x send-prefix

# split panes using | and -
bind = split-window -h
bind - split-window -v
unbind '"'
unbind %

# switch panes using Alt-arrow without prefix
bind -n M-Left select-pane -L
bind -n M-Right select-pane -R
bind -n M-Up select-pane -U
bind -n M-Down select-pane -D

# Enable mouse mode (tmux 2.1 and above)
set -g mouse on

# bind hot key 's' to sync-input
bind-key a setw synchronize-panes
```

## 2. Install Tools : poltjugger / htop / net-tools / zmqpp / ...

```shell
sudo apt-get install ros-noetic-plotjuggler ros-noetic-plotjuggler-ros 
sudo apt-get install libzmqpp-dev net-tools htop
```

## 3. mavros (需要耐心)

```shell 
sudo apt-get install ros-noetic-mavros
cd /opt/ros/noetic/lib/mavros
sudo ./install_geographiclib_datasets.sh
```

## 4. realsense driver

```shell
#install
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-key  F6E65AC044F831AC80A06380C8B3A55A6F3EFCDE || sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-key  F6E65AC044F831AC80A06380C8B3A55A6F3EFCDE
sudo add-apt-repository "deb https://librealsense.intel.com/Debian/apt-repo $(lsb_release -cs) main" -u
sudo apt-get install librealsense2-dkms librealsense2-utils librealsense2-dev librealsense2-dbg

# test
realsense-viewer
```
## 5. livox driver (mid360)

```shell

# step 1
cd ~/driver/livox
git clone https://github.com/Livox-SDK/Livox-SDK2.git
cd Livox-SDK2/
mkdir build && cd build
cmake .. 
make -j
sudo make install

# step 2
mkdir -p ~/driver/livox/ws_livox/src
cd ~/driver/livox/ws_livox 
git clone https://github.com/Livox-SDK/livox_ros_driver2.git ws_livox/src/livox_ros_driver2
cd ws_livox/src/livox_ros_driver2
./build.sh ROS1

# step 2.5 修改bashrc

# step 3
# 修改ws_livox/src/livox_ros_driver2/config/MID360_config.json中主机IP cmd_data_ip和雷达IP，
# 其中雷达#IP 192.168.1.1XX，后两位为雷达S/N码（可以在雷达包装盒和雷达下面找到）的最后两位

"cmd_data_ip" : "192.168.1.50",
"ip" : "192.168.1.140",
```
## 6. clash (x64)

```shell

# step 1
#下载压缩包
https://bhpan.buaa.edu.cn/link/AA4CE517FAF9E349A6AB3486F8A35C642E
文件名：Clash.for.Windows-0.20.39-x64-linux.tar.gz
有效期限：永久有效

# step 2
tar -zxvf Clash.for.Windows-0.20.39-x64-linux.tar.gz

# step 3
cd Clash for Windows-0.20.39-x64-linux
./cfw

# step 4
#更改ubuntu系统代理
#进入ubuntu设置-网络-手动，修改如下参数
#"HTTP代理（H）" : 127.0.0.1 7890
#"HTTPS代理（T）" : 127.0.0.1 7890
#"Socks主机（S）": 127.0.0.1 7891

# step 5
#与windows下的clash使用方法相同，在cfw-Profiles-import导入节点

# step 5.5
#在cfw-Proxies中选择节点

# step 6
#如果可以使用微信（通过cfw-logs查看），但是不能使用浏览器，则还需要更改浏览器的代理
#进入浏览器设置-代理-手动配置代理，参数值同step 4
```
