FROM tensorflow/tensorflow:latest-gpu-py3
ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ENV PATH /opt/conda/bin:$PATH
LABEL maintainer="Scott Penberthy <aai@google.com>"

# Pick up some Dask dependencies
RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys E1DD270288B4E6030699E45FA1715D88E1DF1F24
RUN su -c "echo 'deb http://ppa.launchpad.net/git-core/ppa/ubuntu trusty main' > /etc/apt/sources.list.d/git.list"
RUN apt-get update \
  && apt-get install -yq --no-install-recommends libfuse-dev nano fuse vim git graphviz \
             man tree build-essential \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

RUN curl -O https://bootstrap.pypa.io/get-pip.py && \
    python get-pip.py && \
        rm get-pip.py

# download conda
RUN curl -O https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh && \
    mv Miniconda3-latest-Linux-x86_64.sh ~/miniconda.sh && \
    bash ~/miniconda.sh -b -p /opt/conda && \
    rm ~/miniconda.sh && \
    /opt/conda/bin/conda clean -tipsy && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc && \
    . /opt/conda/etc/profile.d/conda.sh

# geologist packages, redacted
#RUN conda install \
#    cartopy \
#    esmpy \
#    netcdf4>1.4 \
#    nomkl \
#    rasterio \
#    s3fs \
#    gsw 

RUN conda install --yes \
    -c conda-forge \
    -c defaults \
    -c pyviz/label/dev \
    -c bokeh/channel/dev \
    -c intake \
#    bokeh=0.12.16 \
    bokeh \
    bqplot \
    cython \
    cytoolz \
    dask\
#    dask=0.18.2 \
    dask-ml \
    datashader \
#    distributed=1.22.1 \
    distributed \
    fastparquet \
    gcsfs \
#    gcsfs=0.1.2 \
    ipywidgets \
    ipyleaflet \
    jupyter \
    jupyterlab \
#    jupyterlab=0.34.2 \
    jupyterlab_launcher \
#    jupyterlab_launcher=0.13.1 \
    jupyter_client \
    holoviews \
    libsodium \
    lz4 \
    matplotlib \
    msgpack-python \
    nb_conda_kernels \
    numba \
    numcodecs \
#    numpy=1.15.1 \
#    pandas=0.23.2 \
    python-blosc \
    pyzmq \
    scipy \
    scikit-image \
    scikit-learn \
    toolz \
#    toolz=0.9.0 \
    tornado \
#    tornado=5.0.2 \
    xarray \
#    xarray=0.10.8 \
    zarr \
#    zarr=2.2.0 \
    zict \
    intake-xarray \
    graphviz \
    python-graphviz \
    && conda clean -tipsy

RUN pip install --upgrade pip

# PIP geologist packages, redacted
#RUN pip install xesmf \
#                git+https://github.com/xgcm/xgcm \
#                git+https://github.com/Unidata/cftime.git 

RUN pip install fusepy \
                click \
                jedi \
                kubernetes==6.0.0 \
                dask-kubernetes==0.4.0 \
                nbserverproxy==0.8.1 \
                --upgrade --no-cache-dir \
                --upgrade-strategy only-if-needed

# jupyter extensions require nodejs
RUN curl -sL https://deb.nodesource.com/setup_9.x | bash -
RUN apt-get purge nodejs npm && \
    apt-get install -y nodejs
    
#RUN jupyter labextension install @jupyter-widgets/jupyterlab-manager@0.37.4 \
#                                 @jupyterlab/hub-extension@0.11.0 \
#                                 @pyviz/jupyterlab_pyviz@0.6.1 \
#                                 jupyter-leaflet \
#                                 dask-labextension@0.1.2
RUN jupyter labextension install @jupyter-widgets/jupyterlab-manager \
                                 @jupyterlab/hub-extension \
                                 @pyviz/jupyterlab_pyviz \
                                 jupyter-leaflet \
                                 dask-labextension 

RUN jupyter serverextension enable --py nbserverproxy jupyterlab --sys-prefix

##
#### TODO move this to prepare.sh, pulling sample notebooks from Github (get it running first)
##
COPY prepare.sh /usr/bin/prepare.sh
RUN chmod +x /usr/bin/prepare.sh
COPY dask-config.yaml /etc/skel/
COPY worker-template.yaml /etc/skel/

# tell dask where we've mapped their various web services and placed config files.
# see dpod-ingress.yaml where we map /info, /scheduler, /worker, and /tb
# using an nginx ingress controller, which rewrites these paths to /
# and then maps traffic to a port on our pod.
ENV DASK_CONFIG=/etc/skel/dask-config.yaml
ENV DASK_KUBERNETES__WORKER_TEMPLATE_PATH=/etc/skel/worker-template.yaml
ENV DASK_DASHBOARD_URL=/info/
ENV DASK_GIT_URL=https://github.com/mrocklin/pydata-nyc-2018-tutorial.git

# set up our dask demo
RUN mkdir /gcs 
RUN mkdir /opt/app

# download tools to launch RESTful models
# from our friends at seldon.io
RUN curl -L https://github.com/openshift/source-to-image/releases/download/v1.1.13/source-to-image-v1.1.13-b54d75d3-linux-amd64.tar.gz -o s2i.gz && \
    tar xfvz s2i.gz && \
    mv s2i /usr/local/bin/ && \
    rm s2i.gz
RUN pip install seldon-core

##
####
##

# TensorBoard
EXPOSE 6006

# IPython
EXPOSE 8888

# /status, /tasks, /workers
EXPOSE 8787

# /debug of scheduler
EXPOSE 8788

# /debug of worker
EXPOSE 8789

WORKDIR "/notebooks"
RUN echo \
"#!/bin/bash \n\
 \n\
# Startup our jupyterlab inside the virtual environment \n\
# we created earlier. \n\
. /opt/conda/etc/profile.d/conda.sh \n\
coda activate base \n\
\n\
\n\
# Download the latest files \n\
cd /notebooks \n\
rmdir dask &> /dev/null # deletes directory if empty, in favour of fresh clone \n\
if [ ! -d "dask" ]; then \n\
  echo "Cloning '$DASK_GIT_URL' into dask" \n\
  git clone $DASK_GIT_URL dask \n\
fi \n\
cd dask \n\
echo "Updating notebooks" \n\
git remote set-url origin $DASK_GIT_URL \n\
git fetch origin \n\
git reset --hard origin/master \n\
git merge --strategy-option=theirs origin/master \n\
cd .. \n\
echo "Launching jupyterlab" \n\
jupyter lab \"\$@\"\n" >> /jupyterlab.sh
RUN chmod +x /jupyterlab.sh
CMD ["/jupyterlab.sh", "--allow-root"]

#ENTRYPOINT ["tini", "--", "/usr/bin/prepare.sh"]
#CMD ["start.sh jupyter lab"]
