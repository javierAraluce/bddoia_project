ARG CUDA="9.0"
ARG CUDNN="7"

FROM nvidia/cuda:${CUDA}-cudnn${CUDNN}-devel-ubuntu16.04

RUN echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections

# install basics
RUN apt-get update -y \
 && apt-get install -y apt-utils git curl wget ca-certificates bzip2 cmake tree htop bmon iotop g++  \
 && apt-get install -y libglib2.0-0 libsm6 libxext6 libxrender-dev 

# Install Miniconda
# RUN curl -so /miniconda.sh https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh \
#  && chmod +x /miniconda.sh \
#  && /miniconda.sh -b -p /miniconda \
#  && rm /miniconda.sh

# Install Miniconda
RUN wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh \
 && chmod +x /Miniconda3-latest-Linux-x86_64.sh \
 && bash Miniconda3-latest-Linux-x86_64.sh -b -p /miniconda \
 && rm /Miniconda3-latest-Linux-x86_64.sh

ENV PATH=/miniconda/bin:$PATH

# Create a Python 3.6 environment
RUN /miniconda/bin/conda install -y conda-build \
 && /miniconda/bin/conda create -y --name py36 python=3.6.7 \
 && /miniconda/bin/conda clean -ya

ENV CONDA_DEFAULT_ENV=py36
ENV CONDA_PREFIX=/miniconda/envs/$CONDA_DEFAULT_ENV
ENV PATH=$CONDA_PREFIX/bin:$PATH
ENV CONDA_AUTO_UPDATE_CONDA=false

RUN conda install -y ipython
RUN pip install requests ninja yacs cython matplotlib opencv-python tqdm packaging scikit-learn

# Install PyTorch 1.0 Nightly
ARG CUDA
RUN conda install pytorch==1.0 torchvision cudatoolkit=${CUDA} -c pytorch \
# RUN conda install -c pytorch pytorch-nightly torchvision cudatoolkit=9.0 \
 && conda clean -ya

# Install TorchVision master
# RUN git clone https://github.com/pytorch/vision.git \
#  && cd vision \
#  && python setup.py install
# RUN pip install torchvision==0.2.1

# install pycocotools
RUN git clone https://github.com/cocodataset/cocoapi.git \
 && cd cocoapi/PythonAPI \
 && python setup.py build_ext install


# install apex
ENV CUDA_HOME="/usr/local/cuda"
RUN git clone https://github.com/NVIDIA/apex.git \
 && cd apex \
 && python setup.py install
#  && pip install -v --disable-pip-version-check --no-cache-dir --global-option="--cpp_ext" --global-option="--cuda_ext" ./

# install PyTorch Detection
ARG FORCE_CUDA="1"
ENV FORCE_CUDA=${FORCE_CUDA}
RUN git clone https://github.com/facebookresearch/maskrcnn-benchmark.git \
 && cd maskrcnn-benchmark \
 && python setup.py build develop

WORKDIR /maskrcnn-benchmark
