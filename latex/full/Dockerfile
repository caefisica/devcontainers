# syntax=docker/dockerfile:1.5
FROM --platform=${TARGETARCH} docker.io/library/buildpack-deps:kinetic-scm as chktex-builder
ARG CHKTEX_VERSION=1.7.6
ARG CHKTEX_MIRROR="http://download.savannah.gnu.org/releases/chktex"

WORKDIR /tmp/chktex-builder

RUN apt update -y && \
    apt install -y --no-install-recommends g++ make && \
    curl -fL -o- ${CHKTEX_MIRROR}/chktex-${CHKTEX_VERSION}.tar.gz | tar xz --strip-components 1 && \
    ./configure && \
    make

FROM --platform=${TARGETPLATFORM} docker.io/library/buildpack-deps:kinetic-scm

LABEL org.opencontainers.image.authors="David Duran <contacto@altmails.com>" \
    org.opencontainers.image.url="https://github.com/caefisica/LaTeX/" \
    org.opencontainers.image.documentation="https://github.com/caefisica/LaTeX/blob/master/README.md" \
    org.opencontainers.image.source="https://github.com/caefisica/LaTeX/tree/master/.devcontainer"

ARG DOCFILES=0
ARG SRCFILES=0
ARG TEXLIVE_VERSION=2023
ARG TEXLIVE_MIRROR="https://mirror.ctan.org/systems/texlive/tlnet"
ARG DEBIAN_FRONTEND="noninteractive"

ENV LANG="es_ES.UTF-8" \
    LANGUAGE="es_ES.UTF-8" \
    TERM="xterm" \
    TEXDIR="/usr/local/texlive" \
    TEXUSERDIR="/texlive-user" \
    PATH="${TEXDIR}/bin/aarch64-linux:${TEXDIR}/bin/x86_64-linux:${PATH}"

WORKDIR /tmp/texlive

# Installing base packages, locale and TeXLive
RUN apt update -y && \
    apt install -y --no-install-recommends fontconfig vim neovim python3-pygments ttf-mscorefonts-installer locales texlive-lang-spanish ghostscript && \
    locale-gen ${LANG} && \
    update-locale LANG=${LANG} && \
    echo "instopt_letter 1" > /tmp/texlive/profile.txt && \
    echo "instopt_adjustpath 0" >> /tmp/texlive/profile.txt && \
    echo "tlpdbopt_autobackup 0" >> /tmp/texlive/profile.txt && \
    echo "tlpdbopt_desktop_integration 0" >> /tmp/texlive/profile.txt && \
    echo "tlpdbopt_file_assocs 0" >> /tmp/texlive/profile.txt && \
    echo "tlpdbopt_install_docfiles ${DOCFILES}" >> /tmp/texlive/profile.txt && \
    echo "tlpdbopt_install_srcfiles ${SRCFILES}" >> /tmp/texlive/profile.txt && \
    wget -qO- "${TEXLIVE_MIRROR}/install-tl-unx.tar.gz" | tar xz --strip-components=1 && \
    TEXLIVE_INSTALL_NO_CONTEXT_CACHE=1 TEXLIVE_INSTALL_NO_WELCOME=1 ./install-tl -profile /tmp/texlive/profile.txt -no-interaction -texdir ${TEXDIR} -texuserdir ${TEXUSERDIR} && \
    apt clean && \
    apt autoclean && \
    apt autoremove -y && \
    rm -rf /var/lib/apt/lists/* /var/lib/{apt,dpkg,cache,log}/ /tmp/texlive ${TEXDIR}/*.log

# Install `latexindent` dependencies
RUN apt update -y && \
    apt install -y --no-install-recommends cpanminus make gcc libc6-dev && \
    cpanm -n -q Log::Log4perl XString Log::Dispatch::File YAML::Tiny File::HomeDir Unicode::GCString && \
    apt remove -y cpanminus make gcc libc6-dev && \
    apt clean && \
    apt autoclean && \
    apt autoremove -y && \
    rm -rf /var/lib/{apt,dpkg,cache,log}/

# Update the TexLive package manager and minimal packages
RUN tlmgr update --self --verbose
RUN tlmgr install latexmk latexindent --verbose
RUN tlmgr update --all --verbose
RUN texhash

COPY --from=chktex-builder /tmp/chktex-builder/chktex /usr/local/bin/chktex

# Comprobaciones finales
RUN tlmgr version && \
    latexmk -version && \
    texhash --version && \
    chktex --version

WORKDIR /workspace