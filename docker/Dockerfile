#################### Build Arguments
ARG UBUNTU_VERSION=22.04
ARG CUDA_VERSION=12.4.1
ARG PULL_IMAGE=nvidia/cuda:${CUDA_VERSION}-devel-ubuntu${UBUNTU_VERSION}

#################### Base Image
FROM ${PULL_IMAGE}

ARG UBUNTU_VERSION
ARG CUDA_VERSION

USER root

#################### Environment Variables
ENV TZ=Asia/Seoul \
    DEBIAN_FRONTEND=noninteractive

#################### Print Build Information
RUN echo "======================================="
RUN echo "🚀 Building Docker Image"
RUN echo "Ubuntu:    ${UBUNTU_VERSION}"
RUN echo "CUDA:      ${CUDA_VERSION}"
RUN echo "======================================="

#################### Set Timezone & Install Utilities
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
RUN apt-get update -y
RUN apt-get install -y --no-install-recommends python3-pip git net-tools 
RUN apt-get install -y --no-install-recommends iputils-ping nano ffmpeg libsm6 libxext6 
RUN apt-get install -y --no-install-recommends openssh-server 
RUN apt-get install -y --no-install-recommends curl gnupg2 lsb-release 

RUN rm -rf /var/lib/apt/lists/*

#################### Install Python Packages
RUN pip install --upgrade pip
# RUN pip install opencv-python==4.5.5.64  
# #RUN pip install numpy==1.26.4
# RUN pip install pyrealsense2 
# RUN pip install gdown 
# RUN pip install imutils 
# RUN pip install scikit-learn 
# RUN pip install scikit-image 
# RUN pip install Cython==0.29.33 
# RUN pip install Pillow==9.5.0 

RUN /bin/bash -c ' \
    echo "Using Ubuntu ${UBUNTU_VERSION} with CUDA ${CUDA_VERSION}"; \
    if [ "${CUDA_VERSION}" = "11.1.1" ]; then \
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
    fi'

# 필요 패키지 설치
RUN apt-get update && apt-get install -y --no-install-recommends \
    git cmake build-essential \
    libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev libglib2.0-dev libudev-dev \
    udev usbutils v4l-utils \
    gstreamer1.0-tools gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad \
    python3 python3-pip python3-venv curl ca-certificates lsb-release \
 && rm -rf /var/lib/apt/lists/*

# ------------------------------------------------------------
# 품질/편의 설정
# ------------------------------------------------------------
ENV TZ=${TZ}
# tiscamera 환경 자동 로드 (non-login 쉘에서도 필요하면 수동 source)
SHELL ["/bin/bash", "-lc"]

# 기본 워킹 디렉터리
WORKDIR /root

#################### Expose SSH Port & Start SSH on Container Startup
EXPOSE 22
ENTRYPOINT service ssh start && bash
CMD ["/usr/sbin/sshd", "-D"]