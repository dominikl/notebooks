FROM rocker/tidyverse:3.4.2

RUN apt-get update && \
    apt-get -y install python3-pip && \
    apt-get -y install openjdk-8-jdk curl libcurl4-openssl-dev libssl-dev libssh2-1-dev libjpeg-dev && \
    pip3 install --no-cache-dir notebook==5.2 && \
    apt-get purge && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

ENV PATH=$PATH:/usr/lib/jvm/java-8-openjdk-amd64/bin
ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$JAVA_HOME/jre/lib/amd64:$JAVA_HOME/jre/lib/amd64/server

RUN wget https://repo.continuum.io/archive/Anaconda3-5.0.1-Linux-x86_64.sh
RUN bash Anaconda3-5.0.1-Linux-x86_64.sh -b &&\
    rm Anaconda3-5.0.1-Linux-x86_64.sh
ENV PATH /root/anaconda3/bin:$PATH

RUN conda install -c r r-rjava=0.9_8 

RUN R CMD javareconf

ENV NB_USER rstudio
ENV NB_UID 1000
ENV HOME /home/rstudio
WORKDIR ${HOME}

USER ${NB_USER}

# Set up R Kernel for Jupyter
RUN R --quiet -e "install.packages(c('repr', 'IRdisplay', 'evaluate', 'crayon', 'pbdZMQ', 'devtools', 'uuid', 'digest', 'jpeg', 'rJava'))"
RUN R --quiet -e "devtools::install_github('IRkernel/IRkernel')"
RUN R --quiet -e "IRkernel::installspec()"

RUN curl -J -O https://lindnerdominik.2y.net/owncloud/index.php/s/adnVfdKycy3BcOE/download \
 && R -e "install.packages('romero.gateway_0.3.0.tar.gz', repos = NULL, type='source')"

# Make sure the contents of our repo are in ${HOME}
COPY . ${HOME}
USER root
RUN chown -R ${NB_UID}:${NB_UID} ${HOME}
USER ${NB_USER}

# Run install.r if it exists
RUN if [ -f install.r ]; then R --quiet -f install.r; fi

