# SpaceML

We are building a plaform for citizen science in space called "SpaceML."

SpaceML originated with James Parr of NASA's Frontier Development Lab,
after we worked together in the summer of 2018, testing the use of AI
in finding exoplanets, predicting solar flares, and modeling atmospheres
that could be produced by extraterrestial life.   NASA has been asking
for proposals to host a petabyte of data, enabling citizens across the world
to conduct their own experiments.  The ESA has joined as well, now looking
for methods to use space data to improve life on earth.  For example, we could
use satellite data from the upper atmosphere to detect shifts in earth's
electromagnetic signature, which could be a sign of pending earthquakes
as magma shifts beneath the surface.

Our initial version of SpaceML is a Jupyter Lab environment in the cloud, 
powered by a GPU-accelerated Kubernetes cluster.  Data are hosted
and shared on GCS and BigQuery.  Data are manipulated, explored and visualized 
within a Python environment of notebooks, terminal shells, and other Jupyter Lab
plugins.

The stack is entirely open source. Dask expands the in-memory models popularized
by numpy and pandas to handle large data sets that exceed limits of a single
machine (e.g. arrays with 100M rows).  Predictive models are built and trained using
tensorflow, scikit-learn, and pytorch.  Hypterparamter optimization and distributed
training are accelerated by Dask. 
Models are deployed into RESTful endpoints with seldon for integration
with other systems.

We plan to make this platform free to use, much
like Google Colaboratory.  Deeper explorations of data can start 
individual projects by replicating the SpaceML
stack on private clusters.  

We hope you find this stack useful in your own AI endeavors.

_@scottpenberthy, January 2019_


