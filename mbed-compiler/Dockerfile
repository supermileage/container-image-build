FROM ubuntu:20.04

# Install necessary packages.
RUN apt-get update && apt-get -y install software-properties-common
RUN add-apt-repository ppa:deadsnakes/ppa
RUN add-apt-repository ppa:ubuntu-toolchain-r/test
RUN apt-get update && apt-get -y install gawk wget diffstat \
    build-essential chrpath socat libsdl1.2-dev python3.7 tar locales cpio git libncurses5-dev \
    pkg-config curl sudo libncursesw5-dev vim mercurial\
	scons bzr lib32z1 cowsay python3-pip python3.7-dev figlet pv

# use python3 for repo
RUN ln -s /usr/bin/python3.7 /usr/bin/python

# install Mbed CLI
RUN python -m pip install mbed-cli

# install Mbed-os requirements
RUN mkdir -p /temp
WORKDIR /temp
RUN wget https://raw.githubusercontent.com/ARMmbed/mbed-os/master/requirements.txt
RUN python -m pip install -r requirements.txt

# install gcc-arm-none-eabi 6.3.1 (mbed-os requires a version between 6.0.0 and 7.0.0)
RUN wget https://developer.arm.com/-/media/Files/downloads/gnu-rm/6-2017q2/gcc-arm-none-eabi-6-2017-q2-update-linux.tar.bz2
RUN tar xvf gcc-arm-none-eabi-6-2017-q2-update-linux.tar.bz2
RUN rm gcc-arm-none-eabi-6-2017-q2-update-linux.tar.bz2
RUN mv gcc-arm-none-eabi-6-2017-q2-update /opt/gcc-arm-none-eabi-6-2017-q2-update
ENV PATH="/opt/gcc-arm-none-eabi-6-2017-q2-update/bin:$PATH"

RUN rm -rf *

# enable the COWSAY <-- extremely important. Nothing will work if this is not set.
ENV PATH="$PATH:/usr/games"

# Set default shell to BASH because BASH is superior shell.
RUN rm /bin/sh && ln -s bash /bin/sh

# Set locale <-- this is probably not required
RUN locale-gen en_US.UTF-8 && update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LC_ALL en_US.UTF-8

ENV MBEDDIR=/code
WORKDIR ${MBEDDIR}
RUN git clone https://github.com/supermileage/MbedFirmware
ENV MBEDDIR=${MBEDDIR}/MbedFirmware
WORKDIR ${MBEDDIR}
RUN mbed deploy
RUN python -m pip install -r mbed-os/requirements.txt

# precompile mbed-os so the user can compile less.
RUN mbed compile --source ./mbed-os; exit 0;

# misc utils
COPY create_user /usr/local/bin
RUN chmod a+x /usr/local/bin/create_user

# Default UID and GID values - set in docker run using '-e' flag (i.e. docker run -e UID=$(id -u) mbed-compiler:latest)
ENV UID=1001
ENV GID=1001

ENV USERNAME=mbed
ENV GROUP=mbed
ENV COMPILENAME=nucelo
ENV SRCS="mbed-os:Examples"
ENV OUTPUT=/output
ENV COW=none
ENV FIG=FALSE

# build command
CMD \
    # New user creation and permission changes
    # create_user; \
    # Run rest of commands as new user
    # runuser ${USERNAME} -p -c " \
    SRCS=$(echo $SRCS | tr ':' '\n'); \
    echo "Sources: $SRCS"; \
    for src in $SRCS; do \
        echo "Adding $src to mbed compile sources"; \
        SRCSTRING="$SRCSTRING --source $src"; \
    done; \
    echo "SRCSTRING: $SRCSTRING"; \
    echo "compile $SRCSTRING -N $COMPILENAME"; \
    mbed compile $SRCSTRING -N $COMPILENAME | \
    if [[ "$FIG" == "TRUE" ]]; then \
        figlet -f small; \
    else \
        cat; \
    fi && \
    mv ./BUILD/NUCLEO_L432KC/GCC_ARM/${COMPILENAME}.bin /output && \
    echo output && \
    ls /output && \
    if [[ "$COW" == "none" ]]; then \
        echo "SUCCESS"; \
    else \
        figlet SUCESS | cowsay -n -f ${COW}; \
    fi;
    #    "