# Build it

```
# Build the image
cd <repo root>/builder

# build the docker image
docker build -t khenidak/ebpf-builder-base:v01 -f Dockerfile .
```

# Use it

```
#example
cd <repo root>/builder
docker run -it -v $(pwd)/examples_ebpfs:/src/ -v $(pwd)/output:/tmp/output khenidak/ebpf-builder-base:v01 make build SRC_DIR=/src/helloworld/ OUTPUT_DIR=/tmp/output

# a couple of examples are provided for builds
# for your code 

docker run -it -v <path-to-my-code>:/src/ -v <output-path-on-host>/output:/tmp/output khenidak/ebpf-builder-base:v01 make build SRC_DIR=/src/ OUTPUT_DIR=/tmp/output
```

> current image builds for 4.16 kernel version.

# Other things
All documentation is part of ebpf Makefile. View it:
```
docker run -it -v $(pwd)/examples_ebpfs:/src/ -v $(pwd)/output:/tmp/output khenidak/ebpf-builder-base:v01 make help
```

Things you can do:
1. Build a single or selection files `SRC_FILES=/path/to/file1 /path/to/file2`
2. Build an entire dir `SRC_DIR=/path/to/my/code/dir`
3. Replace default clang flags, add additional clang flags, include directories, add clang flags
4. Run it as a dry run using make


