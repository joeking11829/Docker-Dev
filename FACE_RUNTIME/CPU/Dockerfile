FROM ubuntu:14.04

MAINTAINER JOE YOUNG <joeking11829@gmail.com>

# Create USER
RUN useradd -d /home/facerecognition -m -s /bin/bash facerecognition
RUN adduser facerecognition sudo
RUN usermod -a -G video facerecognition

#USER facerecognition
WORKDIR /home/facerecognition

# Install X2GO
RUN sudo apt-get update && sudo apt-get install -y software-properties-common python-software-properties openssh-server
RUN sudo add-apt-repository ppa:x2go/stable -y && sudo apt-get update -y
RUN sudo apt-get install -y x2goserver x2goserver-xsession

# Install MATE Desktop
RUN sudo apt-add-repository ppa:ubuntu-mate-dev/ppa -y && sudo apt-add-repository ppa:ubuntu-mate-dev/trusty-mate -y
RUN sudo apt-get update && sudo apt-get upgrade -y
RUN sudo apt-get install -y mate-desktop-environment-core && sudo apt-get install -y x2gomatebindings
RUN sudo apt-get install -y wget vim htop git curl unzip

#C++ dependency lib
RUN sudo apt-get install liblapack-dev

# Install OpenCV dependency lib
RUN sudo apt-get install -y python-dev python-pip python-numpy build-essential cmake pkg-config libatlas-base-dev gfortran \
                            libjasper-dev libgtk2.0-dev libavcodec-dev libavformat-dev \
                            libswscale-dev libjpeg-dev libpng-dev libtiff-dev libv4l-dev v4l-utils

# Install Python Module
RUN sudo pip install numpy matplotlib

#Install caffe dependency lib
RUN sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libhdf5-serial-dev protobuf-compiler
RUN sudo apt-get install -y --no-install-recommends libboost-all-dev
RUN sudo apt-get install -y libatlas-base-dev
RUN sudo apt-get install -y libgflags-dev libgoogle-glog-dev liblmdb-dev
RUN sudo apt-get install -y libopenblas-base libopenblas-dev

# Install Python Module
RUN sudo pip install scipy scikit-learn

# Change User
USER facerecognition
WORKDIR /home/facerecognition

# Install OpenCV
# Download PrimeSense Binary
RUN mkdir xtion_pro
WORKDIR /home/facerecognition/xtion_pro
RUN wget https://github.com/joeking11829/CameraPSB/blob/master/Bin/OpenNI-Bin-Dev-Linux-x64-v1.5.2.23.tar.bz2?raw=true -O OpenNI-Bin-Dev-Linux-x64-v1.5.2.23.tar.bz2
RUN wget https://github.com/joeking11829/CameraPSB/blob/master/Bin/Sensor-Bin-Linux-x64-v5.1.0.41.tar.bz2?raw=true -O Sensor-Bin-Linux-x64-v5.1.0.41.tar.bz2
RUN tar jxvf OpenNI-Bin-Dev-Linux-x64-v1.5.2.23.tar.bz2
RUN tar jxvf Sensor-Bin-Linux-x64-v5.1.0.41.tar.bz2

# Install PrimeSense Lib
USER root
RUN cd OpenNI-Bin-Dev-Linux-x64-v1.5.2.23 && sudo ./install.sh
RUN cd Sensor-Bin-Linux-x64-v5.1.0.41 && sudo ./install.sh
USER facerecognition
WORKDIR /home/facerecognition

# Download OpenCV
RUN wget https://github.com/Itseez/opencv/archive/3.0.0.zip -O opencv-3.0.0.zip && \
    unzip -q opencv-3.0.0.zip

# Build OpenCV
RUN mkdir opencv-3.0.0/release
WORKDIR /home/facerecognition/opencv-3.0.0/release
RUN cmake -D CMAKE_BUILD_TYPE=RELEASE \
          -D CMAKE_INSTALL_PREFIX=/usr/local \
          -D WITH_V4L=ON \
          -D WITH_IPP=OFF \
          -D WITH_OPENNI=ON \
          ..

RUN make -j"$(nproc)"

# Install OpenCV Lib
USER root
RUN sudo make install
RUN sudo ldconfig
RUN echo "usr/local/liib" >> /etc/ld.so.conf.d/opencv.conf && sudo ldconfig -v
RUN export LD_LIBRARY_PATH=/usr/local/lib/:$LD_LIBRARY_PATH && sudo ldconfig
RUN export LD_LIBRARY_PATH=/home/facerecognition/opencv-3.0.0/release/:$LD_LIBRARY_PATH && sudo ldconfig
RUN echo "export PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig" >> /etc/bash.bashrc
# Install OpenCV-Python2.7 cv2.so
RUN sudo cp lib/cv2.so /usr/lib/python2.7/dist-packages/.

# Change USER
USER facerecognition
WORKDIR /home/facerecognition

# Download Caffe repository
RUN git clone https://github.com/BVLC/caffe.git
WORKDIR /home/facerecognition/caffe

#Install Caffe python requirement
USER root
#RUN sudo pip install cython
#RUN sudo pip install -r python/requirements.txt
RUN for req in $(cat python/requirements.txt) pydot; do pip install $req; done

#Set Caffe build config
USER facerecognition
RUN cp Makefile.config.example Makefile.config
RUN sed -i "s/# CPU_ONLY := 1/CPU_ONLY := 1/g" Makefile.config
RUN sed -i "s/BLAS := atlas/BLAS := open/g" Makefile.config
RUN sed -i "s/# OPENCV_VERSION := 3/OPENCV_VERSION := 3/g" Makefile.config
RUN sed -i "s/# WITH_PYTHON_LAYER := 1/WITH_PYTHON_LAYER := 1/g" Makefile.config

#Build Caffe
RUN make all -j"$(nproc)"
#RUN make test -j"$(nproc)"
#RUN make runtest -j"$(nproc)"

#Build Caffe python interface
RUN make pycaffe -j"$(nproc)"

# Change WORKDIR
WORKDIR /home/facerecognition

# Download Dlib
RUN wget http://dlib.net/files/dlib-19.0.tar.bz2 -O dlib-19.0.tar.bz2 && \
    tar jxvf dlib-19.0.tar.bz2

#Change USER to root
USER root

# Remove no-use data
RUN sudo rm -rf /var/lib/apt/lists/*

# Set User Passwordf
RUN echo "facerecognition:foxconn" | chpasswd

# Set SSH Config
RUN mkdir -p /var/run/sshd

EXPOSE 22

# Start sshd
CMD /usr/sbin/sshd && /bin/bash
