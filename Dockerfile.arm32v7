FROM fkrull/qemu-user-static AS qemu-provider

FROM arm32v7/debian:stable-slim AS stage1
COPY --from=qemu-provider /usr/bin/qemu-arm-static /usr/bin/qemu-arm-static

RUN apt-get update --yes
RUN apt-get install curl tar --yes

RUN curl -fsSL http://os.archlinuxarm.org/os/ArchLinuxARM-armv7-latest.tar.gz > /tmp/ArchLinuxARM-armv7-latest.tar.gz
RUN mkdir -p /tmp/root && cd /tmp/root && tar xpf /tmp/ArchLinuxARM-armv7-latest.tar.gz

FROM scratch AS stage2
COPY --from=stage1 /tmp/root .
COPY --from=qemu-provider /usr/bin/qemu-arm-static /usr/bin/qemu-arm-static

USER root

RUN pacman -Syy --noconfirm

RUN pacman-key --init
RUN pacman-key --populate archlinuxarm
RUN pacman -S archlinuxarm-keyring --noconfirm
RUN pacman-key --populate archlinuxarm

RUN pacman -Suu --noconfirm

RUN rm -rf /var/cache/pacman/pkg/*

# squash layers to optimize image size
FROM scratch
COPY --from=stage2 / .

CMD /bin/bash
