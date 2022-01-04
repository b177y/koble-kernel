KERNEL_VERSION="5.14.9"
KERNEL_URL="https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.14.9.tar.xz"
KERNEL_ARCHIVE=f"linux-{KERNEL_VERSION}.tar.xz"
KERNEL_SRC=f"download_kernel/linux-{KERNEL_VERSION}"
MAKE_JOBS="$(nproc)"

http_archive(
    name="download_kernel",
    urls=[KERNEL_URL],
    strip_prefix="download_kernel",
)

genrule(
    name="patch_kernel",
    srcs=["//:download_kernel", "patches"],
    cmd=[
        f"QUILT_PATCHES=patches quilt push -a",
    ]
)

genrule(
    name="kernel",
    srcs=["//:download_kernel", "uml-kernel-config"],
    deps=["//:patch_kernel"],
    outs=["linux"],
    binary=True,
    cmd=[
        f"cp uml-kernel-config {KERNEL_SRC}/.config",
        f"make -C {KERNEL_SRC} oldconfig ARCH=um",
        f"make -C {KERNEL_SRC} -j {MAKE_JOBS} linux modules ARCH=um",
        f"cp {KERNEL_SRC}/linux linux"
    ]
)

tarball(
    name="archive",
    srcs=["//:kernel"],
    out=f"netkit-kernel-{KERNEL_VERSION}.tar.gz",
    gzip=True,
)
