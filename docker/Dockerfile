#################### Build Arguments
ARG UBUNTU_VERSION=22.04
ARG CUDA_VERSION=12.1.0
ARG PULL_IMAGE=nvidia/cuda:${CUDA_VERSION}-devel-ubuntu${UBUNTU_VERSION}

#################### Base Image
FROM ${PULL_IMAGE}

ARG UBUNTU_VERSION
ARG CUDA_VERSION

USER root

# 비대화형 + 타임존
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Seoul

# System setup in ONE layer (noninteractive)
RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y --no-install-recommends \
        tzdata sudo passwd bash-completion \
        python3 python3-pip python3-venv python3-dev \
        git curl wget nano ffmpeg net-tools iputils-ping \
        openssh-server ca-certificates lsb-release \
        build-essential cmake \
        libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev \
        libglib2.0-dev libudev-dev udev usbutils v4l-utils \
        gstreamer1.0-tools gstreamer1.0-plugins-base \
        gstreamer1.0-plugins-good gstreamer1.0-plugins-bad \
    && ln -fs /usr/share/zoneinfo/$TZ /etc/localtime \
    && echo $TZ > /etc/timezone \
    && dpkg-reconfigure -f noninteractive tzdata \
    && rm -rf /var/lib/apt/lists/*

# Bash completion for all shells
RUN echo ". /usr/share/bash-completion/bash_completion" >> /etc/bash.bashrc

# Create user AFTER packages are installed
RUN useradd -m -s /bin/bash keti && \
    usermod -aG sudo keti && \
    printf "keti ALL=(ALL) NOPASSWD: ALL\n" > /etc/sudoers.d/keti && \
    chmod 0440 /etc/sudoers.d/keti

USER keti
WORKDIR /home/keti

#################### CUDA & PyTorch env
ENV NVIDIA_VISIBLE_DEVICES=all
ENV NVIDIA_DRIVER_CAPABILITIES=compute,utility
ENV CUDA_HOME=/usr/local/cuda
ENV TZ=${TZ}
SHELL ["/bin/bash", "-lc"]

#################### Print Build Information
RUN echo "=======================================" && \
    echo "� Building Docker Image" && \
    echo "Ubuntu:    ${UBUNTU_VERSION}" && \
    echo "CUDA:      ${CUDA_VERSION}" && \
    echo "======================================="

#################### Python / PyTorch
RUN python3 -m pip install --upgrade pip
RUN /bin/bash -lc '\
  echo "Using Ubuntu ${UBUNTU_VERSION} with CUDA ${CUDA_VERSION}"; \
  if   [ "${CUDA_VERSION}" = "11.1.1" ]; then \
    pip install torch==1.10.1+cu111 torchvision==0.11.2+cu111 torchaudio==0.10.1 -f https://download.pytorch.org/whl/cu111/torch_stable.html; \
  elif [ "${CUDA_VERSION}" = "11.7.1" ]; then \
    pip install torch==1.13.1+cu117 torchvision==0.14.1+cu117 torchaudio==0.13.1 --extra-index-url https://download.pytorch.org/whl/cu117; \
  elif [ "${CUDA_VERSION}" = "12.1.0" ]; then \
    pip install torch==2.5.1 torchvision==0.20.1 torchaudio==2.5.1 --index-url https://download.pytorch.org/whl/cu121; \
  elif [ "${CUDA_VERSION}" = "12.4.1" ]; then \
    pip install torch==2.5.1 torchvision==0.20.1 torchaudio==2.5.1 --index-url https://download.pytorch.org/whl/cu124; \
  elif [ "${CUDA_VERSION}" = "12.6.3" ]; then \
    pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu126; \
  else \
    echo "❌ Unsupported"; \
  fi \
'

RUN git clone https://github.com/IDEA-Research/GroundingDINO.git
WORKDIR /home/keti/GroundingDINO
RUN pip install -e .

RUN mkdir weights
WORKDIR /home/keti/GroundingDINO/weights
RUN wget -q https://github.com/IDEA-Research/GroundingDINO/releases/download/v0.1.0-alpha/groundingdino_swint_ogc.pth

RUN echo 'export CUDA_HOME=/usr/local/cuda' >> ~/.bashrc
RUN echo 'export PATH=${CUDA_HOME}/bin:${PATH}' >> ~/.bashrc
RUN echo 'export LD_LIBRARY_PATH=${CUDA_HOME}/lib64:${LD_LIBRARY_PATH}' >> ~/.bashrc

WORKDIR /home/keti

RUN touch ~/.hushlogin # root message disable

RUN sudo apt-get update && sudo apt-get install -y --no-install-recommends \
    libgl1 libglib2.0-0 \
    libx11-6 libxext6 libxrender1 libsm6 \
    libxcb1 libxkbcommon-x11-0 libx11-xcb1 \
 && sudo rm -rf /var/lib/apt/lists/*

RUN git clone https://github.com/abewley/sort.git
RUN pip install filterpy scikit-image lap
RUN sudo apt-get update
RUN sudo apt-get install python3-tk -y
RUN pip install deep-sort-realtime

RUN mkdir sample
RUN mkdir output
RUN mkdir code

CMD ["/bin/bash"]