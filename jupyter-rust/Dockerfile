FROM izumin5210/ielixir-notebook

RUN cd /tmp; curl -o rustup.sh https://sh.rustup.rs; sh rustup.sh -y && true
ENV PATH $PATH:/root/.cargo/bin

RUN cd /root; git clone https://github.com/m0riiiii/jupyter-rust-kernel.git
RUN cd /root/jupyter-rust-kernel; jupyter-kernelspec install rust_spec; python setup.py install

VOLUME /notebooks
WORKDIR /notebooks

EXPOSE 8888

ENTRYPOINT ["tini", "--"]
CMD ["jupyter", "notebook"]