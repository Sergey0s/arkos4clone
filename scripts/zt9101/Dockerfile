# Rebuild zt9101_ztopmac_usb.ko for arkos4clone 4.4.189 aarch64.
# From the repository root:
#   docker build -f scripts/zt9101/Dockerfile -t zt9101-build .
#   docker run --rm -v "$PWD/out:/out" zt9101-build
FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
      build-essential bc bison flex libssl-dev libncurses5-dev \
      gcc-aarch64-linux-gnu g++-aarch64-linux-gnu \
      ca-certificates curl git python3 python-is-python3 \
      rsync kmod cpio patch \
    && rm -rf /var/lib/apt/lists/*

ENV ARCH=arm64
ENV CROSS_COMPILE=aarch64-linux-gnu-

WORKDIR /build

RUN git clone --depth=1 --recurse-submodules -b rg351 \
      https://github.com/lcdyk0517/arkos.bsp.4.4.git kernel \
 && cd kernel \
 && make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- clone_defconfig \
 && make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- modules_prepare -j$(nproc)

RUN git clone --depth=1 https://github.com/yjun123/ZT9101_vendor_linux_driver.git zt9101

COPY scripts/zt9101/patches/0001-r36s-platform.patch /tmp/0001-r36s-platform.patch
RUN cd zt9101 && patch -p1 < /tmp/0001-r36s-platform.patch \
 && cd ZT9101_android_V1.2.1800.20241115 \
 && make HOST_PLAT=r36s \
      ARCH=arm64 \
      CROSS_COMPILE=aarch64-linux-gnu- \
      KVER=4.4.189 \
      KSRC=/build/kernel \
      -j$(nproc) \
 && aarch64-linux-gnu-strip --strip-debug zt9101_ztopmac_usb.ko || true

CMD ["bash", "-c", "cp -f /build/zt9101/ZT9101_android_V1.2.1800.20241115/zt9101_ztopmac_usb.ko /out/ && ls -la /out/zt9101_ztopmac_usb.ko"]
