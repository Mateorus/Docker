FROM		centos

ARG			RUBY_VERSION=2.6.5

SHELL		["/bin/bash", "-c"]

# UPDATE AND INSTALL DEPENDENCIES
RUN			yum update && \
			yum install -y --enablerepo=PowerTools \
			gcc-c++ patch readline-devel zlib-devel git \
			libyaml-devel libffi-devel openssl-devel make \
			bzip2 autoconf automake libtool bison sqlite-devel which

WORKDIR		/tmp

# INSTALL RUBY
RUN			curl -sSL https://rvm.io/mpapis.asc | gpg2 --import - && \
			curl -sSL https://rvm.io/pkuczynski.asc | gpg2 --import - && \
			curl -L get.rvm.io | bash -s stable && \
			usermod -aG rvm root && \
			source /etc/profile.d/rvm.sh && \
			rvm reload && \
			rvm requirements run && \
			rvm install ${RUBY_VERSION}

# INSTALL BUNDLER
RUN			gem install bundler -v 1.17.3

ENTRYPOINT	bash
