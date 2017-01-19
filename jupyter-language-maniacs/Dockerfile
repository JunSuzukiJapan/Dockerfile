FROM izumin5210/ielixir-notebook

#
# Install Python libraries
#
RUN pip install --upgrade pip && pip install numpy matplotlib python-highcharts pandas-highcharts \
                seaborn scipy scikit-learn scikit-image \
                sympy cython patsy statsmodels dill bokeh h5py pandas \
                jupyter_nbextensions_configurator entrypoints

RUN cd /tmp; git clone https://github.com/ipython-contrib/IPython-notebook-extensions && \
    cd /tmp/IPython-notebook-extensions && \
    python3 setup.py install && rm -rf /tmp/*

#
# Install tools & libraries
#

# install gcc6.3
RUN apt-get update && apt-get install bison -y && \
    cd /tmp; curl -O http://ftp.tsukuba.wide.ad.jp/software/gcc/releases/gcc-6.3.0/gcc-6.3.0.tar.gz && \
    cd /tmp; tar zxf gcc-6.3.0.tar.gz -C . && \
    cd /tmp/gcc-6.3.0; ./contrib/download_prerequisites && \
    ./configure --enable-languages=c,c++ --prefix=/usr/local --disable-bootstrap --disable-multilib && \
    make && make install && \
    rm -rf /tmp/*

ENV LD_LIBRARY_PATH /usr/local/lib64:$LD_LIBRARY_PATH

# install libsodium
RUN cd /tmp; curl -O https://download.libsodium.org/libsodium/releases/libsodium-1.0.10.tar.gz && \
    tar xvf libsodium-1.0.10.tar.gz -C . && \
    cd libsodium-1.0.10; ./configure && \
    make && make check && make install && ldconfig && \
    rm -rf /tmp/*

# install 0MQ & czmq
RUN apt-get install libzmq3-dev libtool autoconf -y && \
    cd /tmp && git clone --depth 1 git://github.com/zeromq/libzmq.git && \
    cd libzmq && ./autogen.sh && ./configure && make && \
    cd /tmp/libzmq && make install && ldconfig && \
    cd /tmp; git clone git://github.com/zeromq/czmq.git && \
    cd czmq; ./autogen.sh && ./configure && make check && \
    make install && ldconfig && \
    rm -rf /tmp/*

#
# Install Rust
#
RUN cd /tmp; curl -o rustup.sh https://sh.rustup.rs; sh rustup.sh -y && true
ENV PATH $PATH:/root/.cargo/bin

RUN cd /tmp; git clone https://github.com/m0riiiii/jupyter-rust-kernel.git && \
    cd /tmp/jupyter-rust-kernel; jupyter-kernelspec install rust_spec; python setup.py install && \
    rm -rf /tmp/*

#
# Install Ruby 2.4.0
#
RUN apt-get install -y libreadline-dev && \
    cd /opt; git clone git://github.com/sstephenson/rbenv.git && \
    mkdir /opt/rbenv/plugins && \
    cd /opt/rbenv/plugins && \
    git clone git://github.com/sstephenson/ruby-build.git

ENV RBENV_ROOT "/opt/rbenv"
ENV PATH "/opt/rbenv/shims:${PATH}:$HOME/.rbenv/bin:${RBENV_ROOT}/bin:${PATH}:/opt/local/bin"
ENV RBENV_SHELL bash

RUN cd /opt/rbenv/plugins && \
    rbenv install 2.4.0 && \
    rbenv global 2.4.0 && \
    gem install rbczmq && \
    gem install cztop && \
    gem install iruby && \
    echo 'eval "$(rbenv init -)"' >> /etc/profile.d/rbenv.sh && \
    hash -r 2>/dev/null || true 

SHELL ["/bin/bash", "-c"]

RUN source '/opt/rbenv/libexec/../completions/rbenv.bash'

# install Ruby kernelspec
RUN cd /tmp; mkdir -p ruby; echo "\
define(['base/js/namespace'], function(IPython) { \
    "use strict"; \
    var onload = function() { \
	IPython.CodeCell.options_default['cm_config']['indentUnit'] = 2; \
	var cells = IPython.notebook.get_cells(); \
        for (var i in cells){ \
            var c = cells[i]; \
            if (c.cell_type === 'code') \
                c.code_mirror.setOption('indentUnit', 2); \
        } \
    } \
    return {onload:onload}; \
});" >> ruby/kernel.js && \
    echo "{\"argv\":[\"/opt/rbenv/versions/2.4.0/bin/iruby\",\"kernel\",\"{connection_file}\"],\"display_name\":\"Ruby 2.4.0\",\"language\":\"ruby\"}" >> ruby/kernel.json && \
    cp /opt/rbenv/versions/2.4.0/lib/ruby/gems/2.4.0/gems/iruby-0.2.9/lib/iruby/assets/logo-32x32.png ruby && \
    cp /opt/rbenv/versions/2.4.0/lib/ruby/gems/2.4.0/gems/iruby-0.2.9/lib/iruby/assets/logo-64x64.png ruby && \
    jupyter kernelspec install ruby


# Install Haskell
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 575159689BEFB442 && \
    echo 'deb http://download.fpcomplete.com/ubuntu trusty main'| tee /etc/apt/sources.list.d/fpco.list && \
    apt-get update && sudo apt-get install stack libncurses5-dev -y && \
    mkdir -p ~/.stack/global && \
    echo -e "flags: {}\nresolver: lts-4.0\npackages: []\nextra-deps: []" > ~/.stack/global/stack.yaml && \
    stack setup && \
    apt-get install python-virtualenv python-dev ncurses-base -y && \
    virtualenv venv-ihaskell && source venv-ihaskell/bin/activate  && \
    stack build ihaskell && stack exec ihaskell -- install

ENV PATH $PATH:~/.stack/programs/x86_64-linux/ghc-7.10.3/bin

#
# Install C++ (cling)
#

# install cmake
RUN cd /tmp; wget https://cmake.org/files/v3.7/cmake-3.7.2.tar.gz && \
    tar xvf cmake-3.7.2.tar.gz && \
    cd cmake-3.7.2; ./configure && make && make install

# install cling
RUN cd /tmp; wget https://raw.githubusercontent.com/karies/cling-all-in-one/master/clone.sh && \
    bash clone.sh && mv /tmp/inst /usr/local/cling

ENV PATH $PATH:/usr/local/cling/bin

# install cling kernel
RUN cd /tmp; git clone https://github.com/minrk/clingkernel && \
    cd clingkernel; python setup.py install && \
    python -m pip install tornado && \
    jupyter kernelspec install --user cling && \
    rm -rf /tmp/*

#
# Install Julia
#
RUN apt-get install gfortran -y && ldconfig && \
    ln -s /usr/lib/x86_64-linux-gnu/libgfortran.so.3 /usr/lib/libgfortran.so && \
    cd /tmp; git clone git://github.com/JuliaLang/julia.git && \
    cd julia; make && make install &&  \
    mv usr /usr/local/julia && \
    libtool --finish /usr/local/julia/lib && \
    rm -rf /tmp/*

ENV PATH $PATH:/usr/local/julia/bin

#RUN julia -e 'Pkg.add("IJulia")'

#
# Install Go
#
RUN mkdir -p /tmp/go1.4 && \
    cd /tmp/go1.4; wget https://storage.googleapis.com/golang/go1.4.2.linux-amd64.tar.gz && \
    tar zxvf go1.4.2.linux-amd64.tar.gz -C .

ENV GOROOT_BOOTSTRAP "/tmp/go1.4/go"

RUN cd /tmp; git clone https://go.googlesource.com/go && \
    cd /tmp/go/src; ./make.bash && \
    mv /tmp/go ~/ && rm -rf /tmp/*

RUN mkdir -p ~/go/gopath
ENV GOPATH "$HOME/go/gopath"
ENV PATH "$PATH:$HOME/go/bin"
ENV GOROOT "$HOME/go"

# install igo
#RUN go get github.com/zeromq/goczmq && \
#    go get github.com/takluyver/igo


# Install R
RUN apt-get install r-base r-base-dev -y


# Remove temporary files
#RUN rm -rf /tmp/*

VOLUME /notebooks
WORKDIR /notebooks

EXPOSE 8888

ENTRYPOINT ["tini", "--"]
CMD ["jupyter", "notebook"]