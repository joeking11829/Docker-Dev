FROM joe/cuda-x2go-opencv:stable

MAINTAINER JOE YOUNG <joeking11829@gmail.com>

# Install OpenCV dependency lib
RUN sudo apt-get update
RUN sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libhdf5-serial-dev protobuf-compiler
RUN sudo apt-get install -y --no-install-recommends libboost-all-dev
RUN sudo apt-get install -y libatlas-base-dev
RUN sudo apt-get install -y libgflags-dev libgoogle-glog-dev liblmdb-dev

# Install Python Module
RUN sudo pip install scipy

#Set libcuda.so
RUN rm -rf /usr/lib/x86_64-linux-gnu/libcuda.so
RUN ln -s /usr/local/cuda/lib64/stubs/libcuda.so /usr/lib/x86_64-linux-gnu/libcuda.so

# Change User
USER gesture
WORKDIR /home/gesture

# Download Caffe repository
RUN git clone https://github.com/BVLC/caffe.git
WORKDIR /home/gesture/caffe

#Install Caffe python requirement
USER root
RUN sudo pip install -r python/requirements.txt

#Set Caffe build config
USER gesture
RUN cp Makefile.config.example Makefile.config
RUN sed -i "s/# USE_CUDNN := 1/USE_CUDNN := 1/g" Makefile.config
RUN sed -i "s/# OPENCV_VERSION := 3/OPENCV_VERSION := 3/g" Makefile.config
RUN sed -i "s/# WITH_PYTHON_LAYER := 1/WITH_PYTHON_LAYER := 1/g" Makefile.config

#Build Caffe
RUN make all -j8
#RUN make test -j8
#RUN make runtest -j8

#Build Caffe python interface
RUN make pycaffe -j8

# ReSet libcuda.so
USER root
RUN rm -rf /usr/lib/x86_64-linux-gnu/libcuda.so
RUN ln -s /usr/local/nvidia/lib64/libcuda.so.1 /usr/lib/x86_64-linux-gnu/libcuda.so

# Remove no-use data
RUN sudo rm -rf /var/lib/apt/lists/*

EXPOSE 22

# Start sshd
CMD /usr/sbin/sshd && /bin/bash
