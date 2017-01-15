FROM izumin5210/ielixir-notebook

# Install Rust
RUN cd /tmp; curl -o rustup.sh https://sh.rustup.rs; sh rustup.sh -y && true
ENV PATH $PATH:/root/.cargo/bin

RUN cd /tmp; git clone https://github.com/m0riiiii/jupyter-rust-kernel.git
RUN cd /tmp/jupyter-rust-kernel; jupyter-kernelspec install rust_spec; python setup.py install

# Install jupyter nbextensions
RUN pip install --upgrade pip && \
    pip install numpy matplotlib python-highcharts pandas-highcharts \
                seaborn scipy scikit-learn scikit-image \
                sympy cython patsy statsmodels dill bokeh h5py pandas \
                jupyter_nbextensions_configurator

RUN cd /tmp; git clone https://github.com/ipython-contrib/IPython-notebook-extensions && \
    cd /tmp/IPython-notebook-extensions && \
    python setup.py install

RUN rm -rf /tmp/*

VOLUME /notebooks
WORKDIR /notebooks

EXPOSE 8888

ENTRYPOINT ["tini", "--"]
CMD ["jupyter", "notebook"]