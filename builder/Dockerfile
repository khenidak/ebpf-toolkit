FROM fedora:26

# Install basic build tools
RUN dnf install -y \
	llvm \
	clang \
	kernel-devel \
	make \
	binutils

RUN mkdir /build-tools/
COPY ./Makefile build-tools/
WORKDIR /build-tools/
