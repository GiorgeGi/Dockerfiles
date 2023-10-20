# Build the dockerfile with 'docker image build -t <name> .'


# From which existing image we want to start from
FROM osrf/ros:noetic-desktop-full

# Package installation
RUN apt-get update \
	&& apt-get install -y \
	nano \
	git \
	curl \
	ros-noetic-rosserial \
	ros-noetic-rosserial-arduino \
	ros-noetic-teleop-twist-keyboard \
	&& rm -rf /var/lib/apt/lists/*

# Copy custom files from config folder inside the image
# config folder contents are .yaml files
COPY config/ /site-config/

# Add a regular, non root user
ARG USERNAME=ros
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN groupadd --gid $USER_GID $USERNAME \
	&& useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
	&& mkdir /home/$USERNAME/.config && chown $USER_UID:$USER_GID /home/$USERNAME/.config

# Set up sudo
RUN apt-get update \
	&& apt-get install -y sudo \
	&& echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME\
	&& chmod 0440 /etc/sudoers.d/$USERNAME \
	&& rm -rf /var/lib/apt/lists*

# Install ros complient language
RUN apt-get update && apt-get install -y \
	locales \
	&& locale-gen en_US.UTF-8 \
	&& rm -rf /var/lib/apt/lists/*
ENV LANG en_us.UTF-8

# Install timezone
RUN ln -f /usr/share/zoneinfo/UTC /etc/localtime \
	&& export DEBIAN_FRONTEND=noninteractive \
	&& apt-get update \
	&& apt-get install -y tzdata \
	&& dpkg-reconfigure --frontend noninteractive tzdata \
	&& rm -rf /var/lib/apt/lists/*

# Copy entrypoint and bashrc scripts for proper container environment set up
# entrypoint allows suppling external arguments when run command is initiated
# bashrc automatically sources ros version
COPY entrypoint.sh /entrypoint.sh
COPY bashrc /home/${USERNAME}/.bashrc

# Set up entrypoint and default command
ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
CMD ["bash"]
