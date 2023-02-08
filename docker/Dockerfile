FROM osrf/ros:noetic-desktop-full
MAINTAINER Nuria Diaz <ndiaz@robontik.es>

# Non Root user
ARG user_name=robot
ARG user_uid=1000
ARG user_home=/home/$user_name
ARG user_shell=/bin/bash
ARG ck_dir=$user_home/catkin_ws
ARG ck_src_dir=$ck_dir/src

RUN useradd -m -d $user_home -s $user_shell -u $user_uid $user_name \
	&& echo "PS1='\[\e]0;\u@\h: \w\a\]${debian_chroot:+($debian_chroot)}\[\033[01;33m\]\u\[\033[00m\]@\[\033[01;31m\]\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '" >> ~/.bashrc

ENV DEBIAN_FRONTEND noninteractive

# setup keys
RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654

RUN apt-get update \
	&& apt-get install -q -y \
	--no-install-recommends \
		wget \
		apt-utils \
		# dialog \
		sudo \
	&& apt-get clean -q -y \
	&& apt-get autoremove -q -y \
	&& rm -rf /var/lib/apt/lists/* \
	&& echo '%robot ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

RUN apt-get update \
	&& apt upgrade -y \
	&& apt-get clean -q -y \
	&& apt-get autoremove -q -y \
	&& rm -rf /var/lib/apt/lists/*

RUN apt-get update \
	&& apt-get install -q -y \
	--no-install-recommends \
		python3-vcstool \
		python3-catkin-tools \
		git \
	&& apt-get clean -q -y \
	&& apt-get autoremove -q -y \
	&& rm -rf /var/lib/apt/lists/*

COPY docker/ros-requirements.txt /tmp

RUN apt-get update \
	&& apt-get install -q -y \
	--no-install-recommends \
		$(eval "echo $(cat /tmp/ros-requirements.txt | xargs)") \
	&& apt-get clean -q -y \
	&& apt-get autoremove -q -y \
	&& rm -rf /var/lib/apt/lists/* \
	&& rm /tmp/ros-requirements.txt


COPY docker/ros_entrypoint.sh /

USER $user_name

RUN mkdir -p $ck_src_dir
RUN true \
	&& echo "PS1='\[\e]0;\u@\h: \w\a\]${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u\[\033[00m\]@\[\033[01;31m\]\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '" >> ~/.bashrc \
	&& echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc \
	&& echo "source $ck_dir/devel/setup.bash" >> ~/.bashrc

ARG gazebo_model_path=$user_home/.gazebo
ARG gazebo_models_url=https://github.com/osrf/gazebo_models.git
RUN true \
	&& mkdir -p $gazebo_model_path/models \
	&& chown -R $user_name: $gazebo_model_path \
	&& git clone --depth 1 $gazebo_models_url $gazebo_model_path/models \
	&& rm -rf $gazebo_model_path/models/.git \
	&& true

WORKDIR $ck_dir

# COPY --chown=$user_name \
# 	rb1_base_gazebo \
# 	$ck_src_dir/rb1_base_gazebo

# COPY --chown=$user_name \
# 	rb1_base_sim \
# 	$ck_src_dir/rb1_base_sim

# COPY --chown=$user_name \
# 	rb1_base_sim_bringup \
# 	$ck_src_dir/rb1_base_sim_bringup

ARG repo_file=rb1_base_sim.repos

COPY --chown=$user_name \
	repos/$repo_file \
	/tmp/

ARG repo_file_list_to_use=/tmp/$repo_file
ARG fresh_download_of_git_repos=no

RUN true \
	&& vcs import --input $repo_file_list_to_use \
	&& rosdep update \
	&& echo 'debconf debconf/frontend select Noninteractive' | sudo debconf-set-selections \
	&& sudo apt-get update \
	&& rosdep install --from-paths src --ignore-src -y \
	&& sudo apt-get clean -q -y \
	&& sudo apt-get autoremove -q -y \
	&& sudo rm -rf /var/lib/apt/lists/*

RUN true \
	&& . /opt/ros/noetic/setup.sh \
	&& export ROS_PARALLEL_JOBS=-j$(nproc --all) \
	&& catkin build

ARG ign_version=4_4.0.0
ARG ign_cfg_url=https://raw.githubusercontent.com/ignitionrobotics/ign-fuel-tools/ignition-fuel-tools$ign_version/conf/config.yaml
ARG ign_cfg_dir=$user_home/.ignition/fuel
ARG ign_cfg_file=config.yaml
ARG ign_cfg_path=$ign_cfg_dir/$ign_cfg_file
RUN mkdir -p $ign_cfg_dir \
	&& wget $ign_cfg_url -O $ign_cfg_path

ENV NVIDIA_VISIBLE_DEVICES \
    ${NVIDIA_VISIBLE_DEVICES:-all}
ENV NVIDIA_DRIVER_CAPABILITIES \
    ${NVIDIA_DRIVER_CAPABILITIES:+$NVIDIA_DRIVER_CAPABILITIES,}graphics

ENV USE_GPU_FOR_SIMULATION true

ENV ROS_BU_PKG "rb1_base_sim_bringup"
ENV ROS_BU_LAUNCH "rb1_base_complete.launch"
ENV CATKIN_WS $ck_dir
ENV RBK_CATKIN_PATH $ck_dir

CMD bash -c "/ros_entrypoint.sh roslaunch ${ROS_BU_PKG} ${ROS_BU_LAUNCH}"

RUN sed -i \
	's/<cylinder_inertia/<xacro:cylinder_inertia/' \
	$ck_src_dir/rb1_base_common/rb1_base_description/urdf/wheels/rubber_wheel.urdf.xacro \
	$ck_src_dir/rb1_base_common/rb1_base_description/urdf/wheels/omni_wheel.urdf.xacro

ARG file=$ck_src_dir/robotnik_sensors/urdf/hokuyo_ust20lx.urdf.xacro
RUN sed -i 's#<!--arg name="namespace" value="$(arg namespace)"/-->##' ${file}

ARG file=$ck_src_dir/rb1_base_common/rb1_base_description/urdf/wheels/rubber_wheel.gazebo.xacro
RUN sed 's/<!--/\x0<!--/g;s/-->/-->\x0/g' $file | grep -zv '^<!--' | tr -d '\0' >${file}.new && mv ${file}.new $file

ARG file=$ck_src_dir/rb1_base_common/rb1_base_description/urdf/wheels/omni_wheel.urdf.xacro
RUN sed 's/<!--/\x0<!--/g;s/-->/-->\x0/g' $file | grep -zv '^<!--' | tr -d '\0' >${file}.new && mv ${file}.new $file

# CMD /opt/ros/noetic/lib/xacro/xacro /home/ros/catkin_ws/src/rb1_base_common/rb1_base_description/robots/rb1_base.urdf.xacro prefix:=robot_ ros_planar_move_plugin:=true