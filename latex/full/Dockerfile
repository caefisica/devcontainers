# syntax=docker/dockerfile:1.5

FROM --platform=${TARGETARCH} docker.io/library/buildpack-deps:kinetic-scm as chktex-builder
ARG VARIANT="kinetic"
ARG TARGETPLATFORM
ARG TARGETOS
ARG TARGETARCH
ARG CHKTEX_VERSION=1.7.6
ARG CHKTEX_MIRROR="http://download.savannah.gnu.org/releases/chktex"

WORKDIR /tmp/chktex-builder
ENV DEBIANT_FRONTEND noninteractive

RUN apt update -y && \
    apt install -y --no-install-recommends g++ make

RUN curl -fL -o- ${CHKTEX_MIRROR}/chktex-${CHKTEX_VERSION}.tar.gz | tar xz --strip-components 1

RUN ./configure && \
    make

FROM --platform=${TARGETPLATFORM} docker.io/library/buildpack-deps:kinetic-scm
ARG VARIANT="kinetic"
ARG TARGETPLATFORM
ARG TARGETOS
ARG TARGETARCH

LABEL org.opencontainers.image.authors="David Duran <contacto@altmails.com>" \
    org.opencontainers.image.url="https://github.com/caefisica/LaTeX/" \
    org.opencontainers.image.documentation="https://github.com/caefisica/LaTeX/blob/master/README.md" \
    org.opencontainers.image.source="https://github.com/caefisica/LaTeX/tree/master/.devcontainer"

ARG SCHEME="scheme-full"
ARG DOCFILES=0
ARG SRCFILES=0
ARG TEXLIVE_VERSION=2023
ARG TEXLIVE_MIRROR="https://mirror.ctan.org/systems/texlive/tlnet"
ARG DEBIAN_FRONTEND="noninteractive"

ENV LANG="es_ES.UTF-8"
ENV LANGUAGE="es_ES:es"
ENV TERM="xterm"
ENV TEXDIR="/usr/local/texlive"
ENV TEXUSERDIR="/texlive-user"
ENV PATH ${TEXDIR}/bin/aarch64-linux:${TEXDIR}/bin/x86_64-linux:${PATH}

RUN apt update -y && \
    apt install -y --no-install-recommends fontconfig vim neovim python3-pygments ttf-mscorefonts-installer ghostscript locales texlive-lang-spanish && \
    locale-gen ${LANG} && \
    update-locale LANG=${LANG} && \
    apt clean autoclean && \
    apt autoremove -y && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /tmp/texlive

COPY <<EOF /tmp/texlive/profile.txt
selected_scheme ${SCHEME}
instopt_letter 1
instopt_adjustpath 0
tlpdbopt_autobackup 0
tlpdbopt_desktop_integration 0
tlpdbopt_file_assocs 0
tlpdbopt_install_docfiles ${DOCFILES}
tlpdbopt_install_srcfiles ${SRCFILES}
EOF

RUN wget -qO- "${TEXLIVE_MIRROR}/install-tl-unx.tar.gz" | tar xz --strip-components=1 && \
    export TEXLIVE_INSTALL_NO_CONTEXT_CACHE=1 && \
    export TEXLIVE_INSTALL_NO_WELCOME=1 && \
    ./install-tl -profile /tmp/texlive/profile.txt -no-interaction -texdir ${TEXDIR} -texuserdir ${TEXUSERDIR} && \
    rm -rf /tmp/texlive ${TEXDIR}/*.log

# Install `latexindent` dependencies
RUN apt update -y && \
    apt install -y --no-install-recommends cpanminus make gcc libc6-dev && \
    cpanm -n -q Log::Log4perl && \
    cpanm -n -q XString && \
    cpanm -n -q Log::Dispatch::File && \
    cpanm -n -q YAML::Tiny && \
    cpanm -n -q File::HomeDir && \
    cpanm -n -q Unicode::GCString && \
    apt remove -y cpanminus make gcc libc6-dev && \
    apt clean autoclean && \
    apt autoremove -y && \
    rm -rf /var/lib/{apt,dpkg,cache,log}/

RUN tlmgr update --self --all && \
    tlmgr install latexmk latexindent babel-spanish && \
    tlmgr update --all && \
    texhash

COPY --from=chktex-builder /tmp/chktex-builder/chktex /usr/local/bin/chktex

RUN tlmgr version && \
    latexmk -version && \
    texhash --version && \
    chktex --version

WORKDIR /workspace