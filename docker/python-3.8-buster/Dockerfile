#
# File: https://github.com/machine-learning-helpers/docker-python-light/tree/master/docker/python-3.8-buster/Dockerfile
# On Docker Hub: https://hub.docker.com/repository/docker/artificialintelligence/python-light/general
# Usual Docker tag: py38-buster (artificialintelligence/python-light:py38-buster)
#

# See also:
# * Python 3.8 Debian Buster:
#   + Image on Docker Hub/Cloud: https://hub.docker.com/_/python?tab=tags&page=1&name=3.8-buster
#   + Dockerfile: https://github.com/docker-library/python/blob/master/3.8/buster/Dockerfile
# * The best Docker base image for your Python application, Apr. 2020:
#   https://pythonspeed.com/articles/base-image-python-docker-images/
# * General purpose Debian 10 (Buster) C++/Python development image:
#   + Docker Hub: https://cloud.docker.com/u/cpppythondevelopment/repository/docker/cpppythondevelopment/base
#   + Dockerfile: https://github.com/cpp-projects-showcase/docker-images/tree/master/debian10
#

FROM python:3.8-buster

LABEL maintainer "Denis Arnaud <denis.arnaud_fedora@m4x.org>"

# Tell Docker about the server port
EXPOSE 5000

# Environment
ENV container docker
ENV HOME /root
ENV LANGUAGE en_US:en
ENV LANG en_US.UTF-8
ENV LANG4GEN en_US.utf8
ENV LC_ALL $LANG
ENV SOCI_VER 4.0.1-alpha2

# Update the system
#RUN echo "Europe/Paris" > /etc/timezone
RUN echo 'APT::Get::Assume-Yes "true";' > /etc/apt/apt.conf.d/90-yes
RUN apt-get -qq update
RUN DEBIAN_FRONTEND=noninteractive apt-get install --no-install-recommends \
	apt-utils apt-transport-https \
	ca-certificates locales locales-all tzdata sudo \
	zip unzip gzip bzip2 xz-utils tar p7zip-full \
	curl wget netcat net-tools aptitude

# Generate the locales
RUN locale-gen $LANG && update-locale LANG=$LANG LANGUAGE=$LANGUAGE LC_ALL=$LC_ALL

# Configure the time-zone
RUN dpkg-reconfigure -f noninteractive tzdata

# Basic, C++ and Python packages
RUN apt-get -y install less htop screen \
	git keychain gawk \
	bash-completion vim-nox emacs-nox apt-utils keyutils ftp \
	zlib1g-dev libbz2-dev \
	lsb-release libgmp-dev \
	gcc g++ cppcheck clang cmake manpages patch pkg-config \
	m4 autoconf automake libtool libltdl-dev build-essential \
	flex bison \
	libboost-all-dev libxapian-dev \
	libreadline-dev libncurses5-dev \
	libzmq5-dev libczmq-dev libssl-dev libffi-dev \
	swig graphviz libopenblas-dev
RUN apt-get -y install libmpich-dev libopenmpi-dev \
	sqlite3 libsqlite3-dev \
	mariadb-client libmariadbclient-dev \
	libpqxx-dev \
	libicu-dev libprotobuf-dev protobuf-compiler \
	python libpython-dev python3 libpython3-dev \
	python3-django libapache2-mod-wsgi-py3 \
	jq libgeos++-dev \
	doxygen ghostscript texlive-latex-recommended \
	r-base r-base-dev \
	rake

# Cleaning
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# SOCI
RUN mkdir -p /opt/soci
ADD resources/soci-debian-cmake.patch /opt/soci/soci-debian-cmake.patch
RUN wget https://github.com/SOCI/soci/archive/$SOCI_VER.tar.gz \
	  -O /opt/soci/soci-$SOCI_VER.tar.gz && cd /opt/soci && \
	tar zxf soci-$SOCI_VER.tar.gz && rm -f soci-$SOCI_VER.tar.gz && \
	cd soci-$SOCI_VER && patch -p1 < ../soci-debian-cmake.patch && \
	mkdir -p build && cd build && \
	cmake -DCMAKE_INSTALL_PREFIX=/usr -DCMAKE_BUILD_TYPE=Release \
	  -DSOCI_CXX11=ON -DSOCI_TESTS=OFF .. && \
	make install

# Set up the packaging environment for the `build` user
ADD resources/bashrc $HOME/.bashrc
ADD resources/gitconfig $HOME/.gitconfig
ADD resources/vimrc $HOME/.vimrc
RUN chmod 640 $HOME/.bashrc $HOME/.gitconfig $HOME/.vimrc

# Install a few Python modules for data science
WORKDIR $HOME
RUN python3 -mpip install -U pip
RUN python3 -mpip install -U psutil dill joblib
RUN python3 -mpip install -U setuptools wheel build scikit-build
RUN python3 -mpip install -U pytest tox twine
RUN python3 -mpip install -U pyjq
RUN python3 -mpip install -U numpy pandas pandas-datareader
RUN python3 -mpip install -U scikit-learn
RUN python3 -mpip install -U matplotlib seaborn
RUN python3 -mpip install -U dash flask flask_restful connexion requests
RUN python3 -mpip install -U jupyterlab
RUN python3 -mpip install -U opentraveldata OpenTrepWrapper Neobase
RUN python3 -mpip install -U elasticsearch kafka-python
RUN python3 -mpip install -U psycopg2

#
CMD ["/bin/bash"]


