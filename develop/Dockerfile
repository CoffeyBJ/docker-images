############################################################
# Dockerfile to build OhMyZsh container images
# Based on Latest CentOS
############################################################

# Set the base image to CentOS
FROM centos:6.6

# File Author / Maintainer
MAINTAINER Ben Withem

#RUN rpm -ivh http://mirrors.opencas.cn/epel/6/i386/epel-release-6-8.noarch.rpm
RUN rpm -ivh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm

ENV HOME /root
RUN yum -y update
RUN yum install -y zsh git tmux openssh-server vim wget tar gcc ncurses-devel gcc-c++ make automake python-devel
RUN ssh-keygen -N '' -t rsa -q -f /etc/ssh/ssh_host_rsa_key
RUN ssh-keygen -N '' -t dsa -q -f /etc/ssh/ssh_host_dsa_key
RUN echo "PermitRootLogin yes" >> /etc/ssh/sshd_config
RUN echo hello|passwd --stdin root
RUN sed -i -E "s/UsePAM yes/#UsePAM yes/" /etc/ssh/sshd_config
RUN sed -i -E "s/#UsePAM no/UsePAM no/" /etc/ssh/sshd_config
CMD ["/usr/sbin/sshd","-D"]


################## BEGIN INSTALLATION ######################
RUN git clone git://github.com/bwithem/oh-my-zsh.git ~/.oh-my-zsh \
    && cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc \
    && chsh -s /bin/zsh
RUN sed -i -E "s/^plugins=\((.*)\)$/plugins=(\1 tmux themes tmuxinator)/" ~/.zshrc

RUN yum install -y ruby ruby-devel rubygems rpm-build
RUN gem sources --remove https://rubygems.org/
RUN gem sources -a http://ruby.taobao.org/
RUN gem update --system
RUN gem install tmuxinator
WORKDIR /root

########## python ##############
#RUN wget http://www.python.org/ftp/python/2.7.2/Python-2.7.2.tar.bz2
#RUN tar jxvf Python-2.7.2.tar.bz2
#RUN cd Python-2.7.2 
#RUN ./configure --prefix=/usr/local/Python2.7 --enable-shared
#RUN make && make install
#RUN echo "/usr/local/Python2.7/lib" > /etc/ld.so.conf.d/python.conf
#RUN mv /usr/bin/python /usr/bin/python.old
#RUN ln -s /usr/local/Python2.7/bin/python /usr/bin/python
#RUN sed -i -E "s/usr\/bin\/python/\/usr\/bin\/python2.6/" /usr/bin/yum

################ golang ##################
#RUN git clone https://github.com/jiatower/docker-images.git
COPY go1.4.1.linux-amd64.tar.gz ./
RUN tar -xzvf go1.4.1.linux-amd64.tar.gz
RUN cd go/src;./all.bash
RUN mv go /usr/local
RUN mkdir -p go/{src,bin,pkg,conf}
COPY gocode golint gotags errcheck godef goimports gorename oracle go/bin/

####### vim #############
RUN wget ftp://ftp.vim.org/pub/vim/unix/vim-7.4.tar.bz2
RUN tar -xjvf vim-7.4.tar.bz2
RUN cd vim74;./configure --with-features=huge  --enable-multibyte --enable-xim --with-x --enable-pythoninterp=yes --enable-cscope --enable-fontset --with-python-config-dir=/usr/lib64/python2.6/config
RUN cd vim74;make && make install && rm /usr/bin/vim
RUN git clone https://github.com/gmarik/vundle.git ~/.vim/bundle/vundle
RUN git clone https://github.com/gmarik/Vundle.vim.git ~/.vim/bundle/Vundle.vim
RUN rm /bin/vi;ln -s /usr/local/bin/vim /bin/vi
RUN git clone https://github.com/jiatower/config-files.git
RUN ln -s config-files/.vimrc .
RUN ln -s config-files/.tmuxinator .
RUN ln -s config-files/.tmux.conf .
RUN ln -s config-files/.gitconfig .
RUN rm .zshrc;ln -s config-files/.zshrc .

############ cmake ###########
RUN wget http://www.cmake.org/files/v3.2/cmake-3.2.2.tar.gz
RUN tar -xvzf cmake-3.2.2.tar.gz
RUN cd cmake-3.2.2;./bootstrap && gmake && gmake install

########## YouCompleteMe ################
RUN git clone "https://github.com/Valloric/YouCompleteMe.git" .vim/bundle/YouCompleteMe
RUN cd .vim/bundle/YouCompleteMe;git submodule update --init --recursive
RUN export GOROOT=/usr/local/go;export PATH=$PATH:/usr/local/go/bin;export;cd .vim/bundle/YouCompleteMe;./install.sh --gocode-completer
