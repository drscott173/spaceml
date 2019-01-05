# SpaceML

We are building a platform for 
[citizen science](https://daily.zooniverse.org/2014/09/16/citizen-science-in-dictionary/) in space called "SpaceML."

[![NASA FDL and Google](https://img.youtube.com/vi/Tu7Ja8eUqgU/0.jpg)](https://www.youtube.com/watch?v=Tu7Ja8eUqgU)

SpaceML originated with James Parr of NASA's Frontier Development Lab,
after we worked together in the summer of 2018 (described in the video
above). We tested the use of machine learning and cloud
to find exoplanets, predict solar flares, and model atmospheres
that could be produced by extraterrestial life.   

Recently NASA has been [asking for proposals](https://open.nasa.gov/open-gov/)
to host a petabyte of space-related data, 
enabling citizens across the world
to conduct their own experiments.  The ESA has joined as well, now looking
for methods to use space data to improve life on earth.  For example, we could
use satellite data from the upper atmosphere to detect shifts in earth's
electromagnetic signature, which could be a sign of pending earthquakes
as magma shifts beneath the surface.

Our initial version of SpaceML is a Jupyter Lab environment
powered by a GPU-accelerated Kubernetes cluster.  Data are hosted
and shared on the public cloud (GCS and GBQ). 
Data are manipulated, explored and visualized 
within a Python environment of notebooks, terminal shells, and other Jupyter Lab
plugins.  We're inspired by [Pangeo](https://github.com/pangeo-data), 
a similar effort in earth science.  [Kubeflow](https://www.kubeflow.org/) 
is a generic model for machine learning on kubernetes; 
we borrow heavily from their designs, but believe every user needs
their _own_ cluster.  :-)

Our stack is entirely open source.
[Dask](https://github.com/dask/distributed)
expands in-memory models popularized
by numpy and pandas to handle large data sets that exceed limits of a single
machine (e.g. arrays with 100M rows).  Predictive models are built and trained using
tensorflow, scikit-learn, and pytorch.  Hyperparamter optimization and distributed
training are accelerated by Dask. 
Models are deployed into RESTful endpoints with 
[seldon](https://github.com/SeldonIO/seldon-core) for integration
with other systems.

We hope you find this stack useful in your own AI endeavors.

_@scottpenberthy, January 2019_


