FROM continuumio/miniconda3

ARG TVB_USER=tvb_user
ARG USER_HOME=/home/$TVB_USER
ARG TVB_STORAGE=$USER_HOME/TVB_STORAGE
ARG NUMBA_CACHE=$USER_HOME/numba_cache

RUN useradd -ms /bin/bash $TVB_USER
WORKDIR $USER_HOME

RUN apt-get -y update && apt-get -y install build-essential gcc
RUN apt-get -y update && apt-get -y install texlive-base texlive-formats-extra
RUN mkdir -p /usr/share/man/man1 && apt-get -y update && apt-get -y install octave

RUN conda create -y --name tvb-run python=2 llvmlite
RUN /opt/conda/envs/tvb-run/bin/pip install tvb-library==1.5.8 tvb-framework==1.5.8

COPY --chown=$TVB_USER:$TVB_USER .tvb.configuration $USER_HOME/.tvb.configuration
#COPY --chown=$TVB_USER:$TVB_USER step1/_help $USER_HOME/tvb-root/framework_tvb/tvb/interfaces/web/static/help
RUN chown $TVB_USER:$TVB_USER $USER_HOME

USER $TVB_USER

RUN mkdir $USER_HOME/.tvb-temp; mkdir $USER_HOME/.tvb-temp/logs; mkdir -m777 $TVB_STORAGE $NUMBA_CACHE $KEYCLOAK_CONFIG

ENV TVB_USER_HOME $USER_HOME
ENV NUMBA_CACHE_DIR $NUMBA_CACHE

WORKDIR $USER_HOME

CMD ["bash","-c","source activate tvb-run && python -m tvb.interfaces.web.run WEB_PROFILE tvb.config"]
