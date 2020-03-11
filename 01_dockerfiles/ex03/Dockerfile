FROM		centos:8

ARG			RUBY_VERSION=2.6.5
ARG			GIT_VERSION=2.23.0
ARG			NODEJS_VERSION=13.0.0

EXPOSE		3000

SHELL		["/bin/bash", "-c"]

# UPDATE AND INSTALL DEPENDENCIES
RUN			dnf update && \
			curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.rpm.sh | bash && \
			dnf install -y epel-release && \
			dnf module -y install go-toolset && \
			dnf install -y --enablerepo=PowerTools \
			gcc-c++ patch readline-devel zlib-devel expat-devel perl-Digest-SHA sudo \
			libyaml-devel libffi-devel openssl-devel curl-devel make openssh-server \
			bzip2 autoconf automake libtool bison sqlite-devel which gettext-devel python2 \
			postgresql-server postgresql-devel libicu-devel wget git-lfs cmake python3 \
			redis ed fontconfig freetype libfreetype.so.6 libfontconfig.so.1 rpmdevtools \
			libstdc++.so.6 npm re2 re2-devel GraphicsMagick perl-Image-ExifTool glibc-static \
			postgresql-contrib

# INSTALL GIT
RUN			wget https://github.com/git/git/archive/v${GIT_VERSION}.tar.gz -O git.tar.gz && \
			tar -xf git.tar.gz && \
			cd git-* && \
			make prefix=/usr/local all install

# ADD NEW USER
RUN			useradd -rm -d /home/ruler -s /bin/bash -g root -G wheel ruler && \
			echo "ruler ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

USER		ruler

# INSTALL NODEJS
RUN			curl https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.2/install.sh | bash && \
			source ~/.bash_profile && \
			nvm install ${NODEJS_VERSION}

# INSTALL MINIO
RUN			sudo curl https://dl.min.io/server/minio/release/linux-amd64/minio --output /usr/local/bin/minio && \
			sudo chmod +x /usr/local/bin/minio

# INSTALL RUBY
RUN			curl -sSL https://rvm.io/mpapis.asc | gpg2 --import - && \
			curl -sSL https://rvm.io/pkuczynski.asc | gpg2 --import - && \
			curl -L get.rvm.io | bash -s stable && \
			source /home/ruler/.rvm/scripts/rvm && \
			rvm reload && \
			rvm requirements run && \
			rvm install ${RUBY_VERSION}

# INSTALL YARN
RUN			curl --silent --location https://dl.yarnpkg.com/rpm/yarn.repo | sudo tee /etc/yum.repos.d/yarn.repo && \
			sudo rpm --import https://dl.yarnpkg.com/rpm/pubkey.gpg && \
			sudo dnf install -y yarn

USER		root

# INSTALL RUNIT
RUN			git clone https://github.com/imeyer/runit-rpm runit-rpm && \
			cd ./runit-rpm && \
			./build.sh && \
			rpm -i ~/rpmbuild/RPMS/*/*.rpm

USER		ruler

WORKDIR		/home/ruler

# INSTALL GDK
RUN			source /home/ruler/.rvm/scripts/rvm && \
			source ~/.bash_profile && \
			rvm use ruby-2.6.5 && \
			gem install gitlab-development-kit && \
			gem install bundler -v 1.17.3 && \
			nvm use 13.0.0 && \
			gdk init && \
			cd gitlab-development-kit && \
			(gdk install gitlab_repo=https://gitlab.com/gitlab-org/gitlab-foss.git || \
			gdk install gitlab_repo=https://gitlab.com/gitlab-org/gitlab-foss.git)

WORKDIR		/home/ruler/gitlab-development-kit

# CONFIG GDK
RUN			source /home/ruler/.rvm/scripts/rvm && \
			source ~/.bash_profile && \
			gdk update && \
			echo "hostname: 0.0.0.0" > gdk.yml && \
			gdk reconfigure && \
			rm openssh/sshd_config && make openssh/sshd_config && \
			rm nginx/conf/nginx.conf && make nginx/conf/nginx.conf && \
			cd gitlab && bundle install && bundle exec rails db:migrate
			# ./support/enable-postgres-extensions
			
ENTRYPOINT	source /home/ruler/.rvm/scripts/rvm && \
			source ~/.bash_profile && \
			gdk start
