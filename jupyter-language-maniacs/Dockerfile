FROM izumin5210/ielixir-notebook

# Install Python libraries
RUN pip install --upgrade pip && pip install numpy matplotlib python-highcharts pandas-highcharts \
                seaborn scipy scikit-learn scikit-image \
                sympy cython patsy statsmodels dill bokeh h5py pandas \
                jupyter_nbextensions_configurator entrypoints

RUN cd /tmp; git clone https://github.com/ipython-contrib/IPython-notebook-extensions && \
    cd /tmp/IPython-notebook-extensions && \
    python3 setup.py install

# Install Rust
RUN cd /tmp; curl -o rustup.sh https://sh.rustup.rs; sh rustup.sh -y && true
ENV PATH $PATH:/root/.cargo/bin

RUN cd /tmp; git clone https://github.com/m0riiiii/jupyter-rust-kernel.git && \
    cd /tmp/jupyter-rust-kernel; jupyter-kernelspec install rust_spec; python setup.py install

# Install Ruby 2.4.0
RUN apt-get update && apt-get install -y libreadline-dev && \
    cd /opt; git clone git://github.com/sstephenson/rbenv.git && \
    mkdir /opt/rbenv/plugins && \
    cd /opt/rbenv/plugins && \
    git clone git://github.com/sstephenson/ruby-build.git

ENV RBENV_ROOT "/opt/rbenv"
ENV PATH "/opt/rbenv/shims:${PATH}:$HOME/.rbenv/bin:${RBENV_ROOT}/bin:${PATH}:/opt/local/bin"
ENV RBENV_SHELL bash

RUN apt-get install libzmq3-dev libtool libtool autoconf -y && \
    cd /opt/rbenv/plugins && \
    rbenv install 2.4.0 && \
    rbenv global 2.4.0 && \
    gem install rbczmq && \
    gem install cztop && \
    gem install iruby && \
    echo 'eval "$(rbenv init -)"' >> /etc/profile.d/rbenv.sh && \
    hash -r 2>/dev/null || true

SHELL ["/bin/bash", "-c"]

ENV PATH "/opt/rbenv/shims:${PATH}"
ENV RBENV_SHELL bash
RUN source '/opt/rbenv/libexec/../completions/rbenv.bash'

# Remove temporary files
RUN rm -rf /tmp/*

VOLUME /notebooks
WORKDIR /notebooks

EXPOSE 8888

ENTRYPOINT ["tini", "--"]
CMD ["iruby", "notebook"]