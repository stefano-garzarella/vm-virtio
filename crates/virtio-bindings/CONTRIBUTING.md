# Contributing to virtio-bindings

## Dependencies

### Bindgen
The bindings are currently generated using
[bindgen](https://crates.io/crates/bindgen) version 0.49.0:
```bash
cargo install bindgen --vers 0.49.0
```

### Linux Kernel
Generating bindings depends on the Linux kernel, so you need to have the
repository on your machine:

```bash
git clone https://github.com/torvalds/linux.git
```

## Example for updating to a new kernel version

For this example we assume that you have both linux and virtio-bindings
repositories in your root.

```bash
# linux is the repository that you cloned at the previous step.
cd linux
# Step 1: Checkout the version you want to generate the bindings for.
git checkout v5.0

# Step 2: Generate the bindings from the kernel headers. We need to
# generate a file for each one of the virtio headers we're interested on.
# For the moment, we're generating "virtio_blk", "virtio_net" and
# "virtio_ring". Feel free to add additional header files if you need them
# for your project.
make headers_install INSTALL_HDR_PATH=v5_0_headers
cd v5_0_headers
for i in virtio_blk virtio_net virtio_ring ; do \
    bindgen include/linux/$i.h -o $i.rs \
    --with-derive-default \
    --with-derive-partialeq \
    -- -Iinclude
done
cd ~

# Step 6: Copy the generated files to the new version module.
cp linux/v5_0_headers/*.rs vm-virtio/crates/virtio-bindings/src
```