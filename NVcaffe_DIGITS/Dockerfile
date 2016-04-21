FROM joe/cuda-x2go-opencv:stable

MAINTAINER ADI TSAI <tsaitungtso@gmail.com>

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
RUN git clone --branch caffe-0.13 https://github.com/NVIDIA/caffe.git
WORKDIR /home/gesture/caffe


# Install Caffe python requirement
USER root
RUN cat python/requirements.txt | xargs -n1 sudo pip install

# Build Caffe
USER gesture
RUN mkdir build
WORKDIR /home/gesture/caffe/build
RUN cmake ..
RUN make --jobs=4

# Download source of DIGITS
USER gesture
WORKDIR /home/gesture
RUN git clone https://github.com/NVIDIA/DIGITS.git digits
WORKDIR /home/gesture/digits
USER root

RUN sudo apt-get update

RUN sudo apt-get install -y python-pil 
RUN sudo apt-get install -y python-numpy 
RUN sudo apt-get install -y python-scipy 
RUN sudo apt-get install -y python-protobuf 
RUN sudo apt-get install -y python-gevent 
RUN sudo apt-get install -y python-flask 
RUN sudo apt-get install -y python-flaskext.wtf 
RUN sudo apt-get install -y gunicorn python-h5py

RUN sudo pip install -r requirements.txt

# ReSet libcuda.so
RUN rm -rf /usr/lib/x86_64-linux-gnu/libcuda.so
RUN ln -s /usr/local/nvidia/lib64/libcuda.so.1 /usr/lib/x86_64-linux-gnu/libcuda.so

# Remove no-use data
RUN sudo rm -rf /var/lib/apt/lists/*

EXPOSE 22

# Start sshd
CMD /usr/sbin/sshd && /bin/bash

