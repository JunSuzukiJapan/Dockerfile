FROM amazonlinux
MAINTAINER Jun Suzuki(jun.suzuki.japan@gmail.com)

ENV LC_ALL en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US.UTF-8

RUN yum groupinstall -y "Development tools" && \
    yum install -y zlib-devel bzip2-devel openssl-devel ncurses-devel \
    sqlite-devel readline-devel tk-devel ruby epel-release golang

RUN cd /tmp; curl -O https://www.python.org/ftp/python/2.7.13/Python-2.7.13.tgz && \
    curl -O https://www.python.org/ftp/python/3.6.0/Python-3.6.0.tgz && \
    tar zxf Python-2.7.13.tgz && \
    tar zxf Python-3.6.0.tgz

RUN cd /tmp/Python-2.7.13; ./configure --prefix=/opt/local && \
    make && make altinstall

RUN cd /tmp/Python-3.6.0; ./configure --prefix=/opt/local && \
    make && make altinstall && \
    curl -kL https://bootstrap.pypa.io/get-pip.py | /opt/local/bin/python2.7 && \
    curl -kL https://bootstrap.pypa.io/get-pip.py | /opt/local/bin/python3.6 && \
    /opt/local/bin/pip2.7 install virtualenv && \
    /opt/local/bin/pip3.6 install virtualenv && \
    ln -s /opt/local/bin/python3.6 /usr/local/bin/python3 && \
    ln -s /opt/local/bin/python2.7 /usr/local/bin/python2 && \
    ln -s /opt/local/bin/python /usr/local/bin/python && \
    ln -sf /opt/local/bin/pip /usr/local/bin/pip

RUN pip install --upgrade pip && \
    pip install pandas matplotlib numpy \
                seaborn scipy scikit-learn scikit-image \
                sympy cython patsy \
                statsmodels dill bokeh h5py \
                jupyter jupyter_nbextensions_configurator

RUN cd /tmp; git clone https://github.com/ipython-contrib/IPython-notebook-extensions && \
    cd /tmp/IPython-notebook-extensions && \
    python2 setup.py install

RUN rm -rf /tmp/*

RUN mkdir -p /notebooks

VOLUME /notebooks
WORKDIR /notebooks

EXPOSE 8888

CMD ["jupyter", "notebook"]