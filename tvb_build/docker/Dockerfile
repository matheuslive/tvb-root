FROM continuumio/miniconda3

ARG TVB_USER=tvb_user
ARG USER_HOME=/home/$TVB_USER
ARG TVB_STORAGE=$USER_HOME/TVB_STORAGE

RUN useradd -ms /bin/bash $TVB_USER
WORKDIR $USER_HOME

RUN apt-get -y update && apt-get -y install build-essential gcc
RUN apt-get -y install texlive-base texlive-formats-extra
RUN apt-get -y install postgresql octave

#RUN conda update -n base -c defaults conda
RUN conda create -y --name tvb-docs python=2.7 nomkl scipy numpy networkx scikit-learn cython h5py pip==9.0.1 numexpr psutil
RUN conda install -y --name tvb-docs pytest
RUN conda install -y --name tvb-docs pytables numba scikit-image==0.14.2 simplejson cherrypy
RUN conda install -y --name tvb-docs -c conda-forge subprocess32
#RUN /opt/conda/envs/tvb-docs/bin/pip install --upgrade pip
RUN /opt/conda/envs/tvb-docs/bin/pip install matplotlib==2.1.0 sphinx==1.2.3 docutils==0.12
RUN /opt/conda/envs/tvb-docs/bin/pip install formencode cfflib genshi nibabel sqlalchemy==1.1.14 sqlalchemy-migrate==0.11.0 allensdk BeautifulSoup4
RUN /opt/conda/envs/tvb-docs/bin/pip install tvb-gdist mod-pywebsocket apscheduler lxml minixsv tzlocal

RUN conda create -y --name tvb-run python=2.7 nomkl scipy numpy networkx scikit-learn cython h5py pip==9.0.1 numexpr psutil
RUN conda install -y --name tvb-run pytest pytest-cov ipython psycopg2
RUN conda install -y --name tvb-run pytables numba scikit-image==0.14.2 simplejson cherrypy docutils
RUN conda install -y --name tvb-run -c conda-forge subprocess32
#RUN /opt/conda/envs/tvb-run/bin/pip install --upgrade pip
RUN /opt/conda/envs/tvb-run/bin/pip install matplotlib==2.1.0
RUN /opt/conda/envs/tvb-run/bin/pip install formencode cfflib genshi nibabel sqlalchemy==1.1.14 sqlalchemy-migrate==0.11.0 allensdk BeautifulSoup4
RUN /opt/conda/envs/tvb-run/bin/pip install tvb-gdist

# make sure a copy of tvb-root exists inside, for users without Github clone
USER $TVB_USER
RUN git clone --depth 1 --branch 1.5.10 https://github.com/the-virtual-brain/tvb-root.git

USER root
ARG LAST_SHA=LATEST
RUN cd tvb-root; \
    git pull; \
    cd tvb_build; \
    /bin/bash -c "source activate tvb-run && /bin/bash install_full_tvb.sh"

COPY --chown=$TVB_USER:$TVB_USER .tvb.configuration $USER_HOME/.tvb.configuration
#COPY --chown=$TVB_USER:$TVB_USER step1/_help $USER_HOME/tvb-root/framework_tvb/tvb/interfaces/web/static/help
RUN chown $TVB_USER:$TVB_USER $USER_HOME

USER $TVB_USER

RUN mkdir $USER_HOME/.tvb-temp; mkdir $USER_HOME/.tvb-temp/logs; mkdir -m777 $TVB_STORAGE $NUMBA_CACHE $KEYCLOAK_CONFIG

ENV TVB_USER_HOME $USER_HOME
ENV NUMBA_CACHE_DIR $NUMBA_CACHE

WORKDIR $USER_HOME/tvb-root

CMD ["bash","-c","source activate tvb-run && cd tvb_bin && python -m tvb.interfaces.web.run WEB_PROFILE"]