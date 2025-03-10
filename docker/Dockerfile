# Use the official Ubuntu 22.04 as the base image
FROM ubuntu:22.04

# Set environment variables to avoid interactive prompts during package installation
ENV DEBIAN_FRONTEND=noninteractive

# Update and install necessary packages
RUN apt-get update && \
    apt-get install -y wget vim git curl&& \
    apt-get clean

# Download and install Miniconda
RUN wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O /tmp/miniconda.sh && \
    bash /tmp/miniconda.sh -b -p /opt/conda && \
    rm /tmp/miniconda.sh

# Put conda in PATH
ENV PATH=/opt/conda/bin:$PATH

# Example of installing a specific Python version using conda (optional)
# RUN conda install -y python=3.9
RUN eval "$(conda shell.bash hook)" && \
    conda create -n openvla-oft python=3.10 -y && \
    conda activate openvla-oft
# Ensure that the base environment is activated when the container starts
RUN echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate openvla-oft" >> ~/.bashrc

# Install PyTorch
# Use a command specific to your machine: https://pytorch.org/get-started/locally/
RUN mkdir /workspace
ADD ./requirements.txt /workspace/requirements.txt

RUN eval "$(conda shell.bash hook)" && \
    conda activate openvla-oft && \
    pip install torch torchvision torchaudio

# Clone openvla-oft repo and pip install to download dependencies
#RUN cd /workspace && git clone https://github.com/moojink/openvla-oft.git && \
#   cd openvla-oft && \
#   pip install -e .
RUN cd /workspace && \
    eval "$(conda shell.bash hook)" && \
    conda activate openvla-oft && \
    pip install -r requirements.txt

# Install Flash Attention 2 for training (https://github.com/Dao-AILab/flash-attention)
#   =>> If you run into difficulty, try `pip cache remove flash_attn` first
RUN eval "$(conda shell.bash hook)" && \
    conda activate openvla-oft && \    
    pip install packaging ninja && \
    ninja --version; echo $?  && \
    pip install "flash-attn==2.5.5" --no-build-isolation

WORKDIR /workspace  /openvla-opt  
# Set the default command to bash
CMD ["/bin/bash"]