# base stage
FROM ubuntu:20.04 as base

# {pre-base}

## install dependencies
RUN DEBIAN_FRONTEND=noninteractive apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -qy git libgomp1 qt5-default libqt5charts5 libqt5network5 python3-venv ssh x11-apps && \
    DEBIAN_FRONTEND=noninteractive apt-get clean && \
    find /usr/share/doc \( -type f -o -type l \) \! -iname copyright -delete && \
    find /usr/share/doc -type d -empty -delete && \
    mkdir /usr/share/doc/hash && \
    for i in $(find /usr/share/doc -type f); do \
        hash=$(sha1sum -b $i | cut -d\  -f1) && \
        cp $i /usr/share/doc/hash/$hash && \
        rm $i && \
        ln -s /usr/share/doc/hash/$hash $i; \
    done
# Note: in the above we compress /usr/share/doc while keeping legally required copyrights

# {post-base}

# build stage
FROM base as build

# {pre-build}

## install additional build dependencies
RUN apt-get update && \
    apt-get install -qy build-essential cmake libboost-all-dev libqt5charts5-dev ssh x11-apps
## download repositories
RUN git clone https://github.com/Starlink/sofa.git --depth=1 --branch=vendor /opt/IAU_SOFA && rm -rf /opt/IAU_SOFA/.git
RUN git clone https://github.com/dnwrnr/sgp4.git /opt/sgp4 && cd /opt/sgp4 && git checkout f5cb54b38 && rm -rf .git
RUN git clone https://github.com/TUW-VieVS/VieSchedpp.git --depth=1 /opt/VieSchedpp && rm -rf /opt/VieSchedpp/.git
RUN git clone https://github.com/TUW-VieVS/VieSchedppGUI.git --depth=1 /opt/VieSchedppGUI && rm -rf /opt/VieSchedppGUI/.git
RUN git clone --recurse-submodules https://github.com/TUW-VieVS/VieSchedpp_AUTO.git /opt/VieSchedpp_AUTO && rm -rf /opt/VieSchedpp_AUTO/.git

## install IAU_SOFA
RUN mkdir /opt/IAU_SOFA/Release && \
    cd /opt/IAU_SOFA/src && \
    make -j "$(nproc)" && \
    mv libsofa_c.a ../Release/ && \
    make clean

## install SGP4
RUN mkdir /opt/sgp4/Release && \
    cd /opt/sgp4/Release && \
    cmake -j "$(nproc)" -DCMAKE_BUILD_TYPE=Release .. && \
    make -j "$(nproc)"

## install VieSched++
RUN mkdir /opt/VieSchedpp/Release && \
    cd /opt/VieSchedpp/Release && \
    cmake -j "$(nproc)" -DCMAKE_BUILD_TYPE=Release .. && \
    make -j "$(nproc)"

## install VieSched++ GUI
RUN cd /opt/VieSchedppGUI && \
    qmake VieSchedppGUI.pro && \
    make -j "$(nproc)" && \
    make clean

## install VieSched++ AUTO
RUN cd /opt/VieSchedpp_AUTO && \
    python3 -m venv venv && \
    . venv/bin/activate && \
    pip install -q wheel && \
    pip install -q -r requirements.txt && \
    printf '#! /bin/bash\n\n. /opt/VieSchedpp/VieSchedpp_AUTO/venv/bin/activate\npython3 /opt/VieSchedpp/VieSchedpp_AUTO/VieSchedpp_AUTO.py "$@"\n' > /opt/VieSchedpp_AUTO/VieSchedpp_AUTO && \
    chmod 755 /opt/VieSchedpp_AUTO/VieSchedpp_AUTO

# {post-build}

# install stage

## install runtime dependencies
FROM base

## bring in /opt
COPY --from=build /opt /opt

# {pre-install}

## enable SSH directly to root@container without password and with X11 (Future versions might support using oper:vlbi (801:630) instead)
## then link VieSched++ around
COPY sshd_config /etc/ssh/sshd_config
COPY bashrc /etc/profile.d/zzz_vieschedpp.sh
ENV LIBGL_ALWAYS_INDIRECT=1 XDG_RUNTIME_DIR=/tmp/runtime-root
RUN mkdir /var/run/sshd && touch /root/.Xauthority && chmod 600 /root/.Xauthority && \
    echo 'root:$6$mBadEA6b9fKFCFjt$xCyMoyFUZTq/2kK7.8BMXTwwJ.fW65UyESsX5DugLYYfpF8qi34g3ix8SvbvmWwxUtHKwaOFLD6hJALLWND3u/' | chpasswd -e && \
    ln -s /opt/VieSchedpp/Release/VieSchedpp /root/VieSchedpp && \
    ln -s /opt/VieSchedppGUI/VieSchedppGUI /root/VieSchedppGUI && \
    ln -s /opt/VieSchedpp_AUTO/VieSchedpp_AUTO /root/VieSchedpp_AUTO && \
    ln -s /opt/VieSchedpp/Release/VieSchedpp /usr/local/bin/VieSchedpp && \
    ln -s /opt/VieSchedpp/Release/VieSchedpp /usr/local/bin/VieSched++ && \
    ln -s /opt/VieSchedpp/Release/VieSchedpp /usr/local/bin/VieSched && \
    ln -s /opt/VieSchedpp/Release/VieSchedpp /usr/local/bin/viesched++ && \
    ln -s /opt/VieSchedpp/Release/VieSchedpp /usr/local/bin/vieschedpp && \
    ln -s /opt/VieSchedpp/Release/VieSchedpp /usr/local/bin/viesched && \
    ln -s /opt/VieSchedppGUI/VieSchedppGUI /usr/local/bin/VieSchedppGUI && \
    ln -s /opt/VieSchedppGUI/VieSchedppGUI /usr/local/bin/VieSched++GUI && \
    ln -s /opt/VieSchedppGUI/VieSchedppGUI /usr/local/bin/VieSched-GUI && \
    ln -s /opt/VieSchedppGUI/VieSchedppGUI /usr/local/bin/VieSchedGUI && \
    ln -s /opt/VieSchedppGUI/VieSchedppGUI /usr/local/bin/vieschedppgui && \
    ln -s /opt/VieSchedppGUI/VieSchedppGUI /usr/local/bin/viesched++gui && \
    ln -s /opt/VieSchedppGUI/VieSchedppGUI /usr/local/bin/viesched-gui && \
    ln -s /opt/VieSchedppGUI/VieSchedppGUI /usr/local/bin/vieschedgui && \
    ln -s /opt/VieSchedpp_AUTO/VieSchedpp_AUTO /usr/local/bin/VieSchedpp_AUTO && \
    ln -s /opt/VieSchedpp_AUTO/VieSchedpp_AUTO /usr/local/bin/VieSchedppAUTO && \
    ln -s /opt/VieSchedpp_AUTO/VieSchedpp_AUTO /usr/local/bin/VieSched++AUTO && \
    ln -s /opt/VieSchedpp_AUTO/VieSchedpp_AUTO /usr/local/bin/VieSched-AUTO && \
    ln -s /opt/VieSchedpp_AUTO/VieSchedpp_AUTO /usr/local/bin/VieSchedAUTO && \
    ln -s /opt/VieSchedpp_AUTO/VieSchedpp_AUTO /usr/local/bin/vieschedpp_auto && \
    ln -s /opt/VieSchedpp_AUTO/VieSchedpp_AUTO /usr/local/bin/vieschedppauto && \
    ln -s /opt/VieSchedpp_AUTO/VieSchedpp_AUTO /usr/local/bin/viesched++auto && \
    ln -s /opt/VieSchedpp_AUTO/VieSchedpp_AUTO /usr/local/bin/viesched-auto && \
    ln -s /opt/VieSchedpp_AUTO/VieSchedpp_AUTO /usr/local/bin/vieschedauto

## open bash by default
CMD ["bash", "-l"]

# {post-install}
