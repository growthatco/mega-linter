# MEGA-LINTER FLAVOR [dotnet]: Optimized for C, C++, C# or VB based projects
###########################################
###########################################
## Dockerfile to run MegaLinter ##
###########################################
###########################################

# @not-generated

#############################################################################################
## @generated by .automation/build.py using descriptor files, please do not update manually ##
#############################################################################################
#FROM__START
FROM mvdan/shfmt:latest-alpine as shfmt
FROM hadolint/hadolint:v2.7.0-alpine as hadolint
FROM yoheimuta/protolint:latest as protolint
#FROM__END

##################
# Get base image #
##################
FROM python:3.9.7-alpine3.13

#############################################################################################
## @generated by .automation/build.py using descriptor files, please do not update manually ##
#############################################################################################
#ARG__START
ARG PWSH_VERSION='latest'
ARG PWSH_DIRECTORY='/opt/microsoft/powershell'
ARG ARM_TTK_NAME='master.zip'
ARG ARM_TTK_URI='https://github.com/Azure/arm-ttk/archive/master.zip'
ARG ARM_TTK_DIRECTORY='/opt/microsoft'
ARG PSSA_VERSION='latest'
#ARG__END

####################
# Run APK installs #
####################

# APK Packages used by mega-linter core architecture
RUN apk add --update --no-cache \
    bash \
    ca-certificates \
    coreutils \
    curl \
    file \
    gcc \
    git git-lfs\
    go \
    gnupg \
    icu-libs \
    krb5-libs \
    libcurl libintl libssl1.1 libstdc++ \
    libffi-dev \
    linux-headers \
    make \
    musl-dev \
    openjdk8 \
    openssh \
    npm \
    nodejs \
    py3-setuptools \
    readline-dev \
    ruby-bundler \
    ruby-dev \
    yarn

#############################################################################################
## @generated by .automation/build.py using descriptor files, please do not update manually ##
#############################################################################################
#APK__START
RUN apk add --update --no-cache \
                lttng-ust-dev \
                zlib \
                zlib-dev \
                ansible \
                ansible-lint \
                libc-dev \
                libxml2-dev \
                libxml2-utils \
                libgcc
#APK__END

# Ignore npm package issues
RUN yarn config set ignore-engines true
# PATH for golang
ENV GOROOT=/usr/lib/go
ENV GOPATH=/go
# hadolint ignore=DL3044
ENV PATH="$PATH":"$GOROOT"/bin:"$GOPATH"/bin
RUN mkdir -p ${GOPATH}/src ${GOPATH}/bin

#############################################################################################
## @generated by .automation/build.py using descriptor files, please do not update manually ##
#############################################################################################
#PIP__START
RUN pip3 install --no-cache-dir --upgrade pip && pip3 install --no-cache-dir --upgrade \
          'cpplint' \
          'snakemake' \
          'sqlfluff' \
          'yamllint'
#PIP__END

############################
# Install NPM dependencies #
#############################################################################################
## @generated by .automation/build.py using descriptor files, please do not update manually ##
#############################################################################################

# Downgrade npm because from npm@v7, npm install crashes when called from root directory within Dockerfile
RUN npm install npm@latest-6 -g
# Disable package-lock.json to avoid sudden crash. Try to remove later if possible
RUN echo 'package-lock=false' >> .npmrc 
ENV NODE_OPTIONS="--max-old-space-size=8192"
#NPM__START
RUN npm install --no-cache --ignore-scripts \
                jscpd@3.3.26 \
                secretlint@4.1.0 \
                @secretlint/secretlint-rule-preset-recommend@4.1.0 \
                stylelint \
                stylelint-config-standard \
                stylelint-config-sass-guidelines \
                stylelint-scss \
                dockerfilelint \
                editorconfig-checker \
                graphql-schema-linter \
                npm-groovy-lint \
                htmlhint \
                jsonlint \
                eslint \
                eslint-plugin-jsonc \
                v8r@0.6.1 \
                prettier \
                markdownlint-cli \
                remark-cli \
                remark-preset-lint-recommended \
                markdown-link-check \
                markdown-table-formatter \
                @stoplight/spectral@5.6.0 \
                cspell \
                sql-lint \
                tekton-lint
#NPM__END

# Add node packages to path #
ENV PATH="/node_modules/.bin:${PATH}"

##############################
# Installs ruby dependencies #
#############################################################################################
## @generated by .automation/build.py using descriptor files, please do not update manually ##
#############################################################################################

#GEM__START
RUN echo 'gem: --no-document' >> ~/.gemrc && \
    gem install \
          scss_lint
#GEM__END

#############################################################################################
## @generated by .automation/build.py using descriptor files, please do not update manually ##
#############################################################################################
#OTHER__START
# CSHARP installation
RUN wget --tries=5 -q -O dotnet-install.sh https://dot.net/v1/dotnet-install.sh \
    && chmod +x dotnet-install.sh \
    && ./dotnet-install.sh --install-dir /usr/share/dotnet -channel 5.0 -version latest

ENV PATH="${PATH}:/root/.dotnet/tools:/usr/share/dotnet"

# GIT installation
RUN git config --global core.autocrlf true

# POWERSHELL installation
RUN mkdir -p ${PWSH_DIRECTORY} \
    && curl --retry 5 --retry-delay 5 -s https://api.github.com/repos/powershell/powershell/releases/${PWSH_VERSION} \
        | grep browser_download_url \
        | grep linux-alpine-x64 \
        | cut -d '"' -f 4 \
        | xargs -n 1 wget -O - \
        | tar -xzC ${PWSH_DIRECTORY} \
    && ln -sf ${PWSH_DIRECTORY}/pwsh /usr/bin/pwsh


# actionlint installation
ENV GO111MODULE=on
RUN go get github.com/rhysd/actionlint/cmd/actionlint

# arm-ttk installation
ENV ARM_TTK_PSD1="${ARM_TTK_DIRECTORY}/arm-ttk-master/arm-ttk/arm-ttk.psd1"
RUN curl --retry 5 --retry-delay 5 -sLO "${ARM_TTK_URI}" \
    && unzip "${ARM_TTK_NAME}" -d "${ARM_TTK_DIRECTORY}" \
    && rm "${ARM_TTK_NAME}" \
    && ln -sTf "${ARM_TTK_PSD1}" /usr/bin/arm-ttk \
    && chmod a+x /usr/bin/arm-ttk


# bash-exec installation
RUN printf '#!/bin/bash \n\nif [[ -x "$1" ]]; then exit 0; else echo "Error: File:[$1] is not executable"; exit 1; fi' > /usr/bin/bash-exec \
    && chmod +x /usr/bin/bash-exec


# shellcheck installation
RUN wget -qO- "https://github.com/koalaman/shellcheck/releases/download/stable/shellcheck-stable.linux.x86_64.tar.xz" | tar -xJv \
    && cp "shellcheck-stable/shellcheck" /usr/bin/ \
    && shellcheck --version


# shfmt installation
COPY --from=shfmt /bin/shfmt /usr/bin/

# dotnet-format installation
RUN /usr/share/dotnet/dotnet tool install -g dotnet-format

# hadolint installation
COPY --from=hadolint /bin/hadolint /usr/bin/hadolint

# dotenv-linter installation
RUN wget -q -O - https://raw.githubusercontent.com/dotenv-linter/dotenv-linter/master/install.sh | sh -s

# ktlint installation
RUN curl --retry 5 --retry-delay 5 -sSLO https://github.com/pinterest/ktlint/releases/latest/download/ktlint && \
    chmod a+x ktlint && \
    mv "ktlint" /usr/bin/


# kubeval installation
RUN wget -q https://github.com/instrumenta/kubeval/releases/latest/download/kubeval-linux-amd64.tar.gz \
    && tar xf kubeval-linux-amd64.tar.gz \
    && cp kubeval /usr/local/bin


# powershell installation
RUN pwsh -c 'Install-Module -Name PSScriptAnalyzer -RequiredVersion ${PSSA_VERSION} -Scope AllUsers -Force'

# protolint installation
COPY --from=protolint /usr/local/bin/protolint /usr/bin/

# misspell installation
RUN curl -L -o ./install-misspell.sh https://git.io/misspell \
    && sh ./install-misspell.sh


# tsqllint installation
RUN dotnet tool install --global TSQLLint

#OTHER__END

######################
# Set the entrypoint #
######################
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x entrypoint.sh
ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]

################################
# Installs python dependencies #
################################
COPY megalinter /megalinter
RUN python /megalinter/setup.py install \
    && python /megalinter/setup.py clean --all \
    && rm -rf /var/cache/apk/*

#######################################
# Copy scripts and rules to container #
#######################################
COPY megalinter/descriptors /megalinter-descriptors
COPY TEMPLATES /action/lib/.automation

###########################
# Get the build arguments #
###########################
ARG BUILD_DATE
ARG BUILD_REVISION
ARG BUILD_VERSION

#################################################
# Set ENV values used for debugging the version #
#################################################
ENV BUILD_DATE=$BUILD_DATE \
    BUILD_REVISION=$BUILD_REVISION \
    BUILD_VERSION=$BUILD_VERSION

#FLAVOR__START
ENV MEGALINTER_FLAVOR=dotnet
#FLAVOR__END

#########################################
# Label the instance and set maintainer #
#########################################
LABEL com.github.actions.name="MegaLinter" \
      com.github.actions.description="The ultimate linters aggregator to make sure your projects are clean" \
      com.github.actions.icon="code" \
      com.github.actions.color="red" \
      maintainer="Nicolas Vuillamy <nicolas.vuillamy@gmail.com>" \
      org.opencontainers.image.created=$BUILD_DATE \
      org.opencontainers.image.revision=$BUILD_REVISION \
      org.opencontainers.image.version=$BUILD_VERSION \
      org.opencontainers.image.authors="Nicolas Vuillamy <nicolas.vuillamy@gmail.com>" \
      org.opencontainers.image.url="https://megalinter.github.io" \
      org.opencontainers.image.source="https://github.com/megalinter/megalinter" \
      org.opencontainers.image.documentation="https://megalinter.github.io" \
      org.opencontainers.image.vendor="Nicolas Vuillamy" \
      org.opencontainers.image.description="Lint your code base with GitHub Actions"
