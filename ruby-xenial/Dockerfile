# ntodd/ruby-xenial:2.4.1

FROM ubuntu:xenial
LABEL maintainer="Nate Todd <nate@pixelauthorityllc.com>"

ENV RUBY_MAJOR=2.4 \
		RUBY_VERSION=2.4.1 \
		RUBY_DOWNLOAD_SHA256=a330e10d5cb5e53b3a0078326c5731888bb55e32c4abfeb27d9e7f8e5d000250 \
		RUBYGEMS_VERSION=2.6.12 \
		BUNDLER_VERSION=1.15.4

RUN set -ex \
 	&& apt-get update \
	&& apt-get install -y --no-install-recommends \
		bzip2 \
		ca-certificates \
		libffi-dev \
		libgdbm3 \
		libssl-dev \
		libyaml-dev \
		procps \
		zlib1g-dev \
	&& rm -rf /var/lib/apt/lists/* \
	\
	# skip installing gem documentation
	&& mkdir -p /usr/local/etc \
	&& { \
		echo 'install: --no-document'; \
		echo 'update: --no-document'; \
	} >> /usr/local/etc/gemrc \
	\
	# some of ruby's build scripts are written in ruby
	# we purge system ruby later to make sure our final image uses what we just built
	&& buildDeps=' \
		autoconf \
		bison \
		gcc \
		libbz2-dev \
		libgdbm-dev \
		libglib2.0-dev \
		libncurses-dev \
		libreadline-dev \
		libxml2-dev \
		libxslt-dev \
		make \
		ruby \
		wget \
		xz-utils \
	' \
	&& apt-get update \
	&& apt-get install -y --no-install-recommends $buildDeps \
	&& rm -rf /var/lib/apt/lists/* \
	\
	&& wget -O ruby.tar.gz "https://cache.ruby-lang.org/pub/ruby/${RUBY_MAJOR%-rc}/ruby-$RUBY_VERSION.tar.gz" \
	&& echo "$RUBY_DOWNLOAD_SHA256 *ruby.tar.gz" | sha256sum -c - \
	\
	&& mkdir -p /usr/src/ruby \
	&& tar -xzf ruby.tar.gz -C /usr/src/ruby --strip-components=1 \
	&& rm ruby.tar.gz \
	\
	&& cd /usr/src/ruby \
	\
	# hack in "ENABLE_PATH_CHECK" disabling to suppress:
	# warning: Insecure world writable dir
	&& { \
		echo '#define ENABLE_PATH_CHECK 0'; \
		echo; \
		cat file.c; \
	} > file.c.new \
	&& mv file.c.new file.c \
	\
	&& autoconf \
	&& ./configure --disable-install-doc --enable-shared \
	&& make -j"$(nproc)" \
	&& make install \
	\
	&& apt-get purge -y --auto-remove $buildDeps \
	&& cd / \
	&& rm -r /usr/src/ruby \
	\
	&& gem update --system "$RUBYGEMS_VERSION" \
	&& gem install bundler --version "$BUNDLER_VERSION"

# install things globally, for great justice
# and don't create ".bundle" in all our apps
ENV GEM_HOME=/usr/local/bundle \
		BUNDLE_PATH="$GEM_HOME" \
	  BUNDLE_BIN="$GEM_HOME/bin" \
	  BUNDLE_SILENCE_ROOT_WARNING=1 \
		BUNDLE_APP_CONFIG="$GEM_HOME" \
		PATH=$BUNDLE_BIN:$PATH

RUN mkdir -p "$GEM_HOME" "$BUNDLE_BIN" \
	&& chmod 777 "$GEM_HOME" "$BUNDLE_BIN"

CMD [ "irb" ]
