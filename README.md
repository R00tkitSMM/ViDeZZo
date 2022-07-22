# ViDeZZo: Dependency-aware Virtual Device Fuzzing Framework

ViDeZZo is a virtual device fuzzing framework considering both intra- and
inter-message dependencies to balance fuzzing scalability and efficiency.

Currently, ViDeZZo supports libFuzzer in combination with ASAN and UBSAN.

Currently, ViDeZZo supports QEMU (C) and VirtualBox (C++) covering Audio,
Storage, Network, USB, and Graphics virtual devices, and covering i386, ARM, and
AArch64 builds.

More technical details go in to our paper. The usage of ViDeZZo is as follows.


## Docker container

We recommend running ViDeZZo in a docker container. We also tested ViDeZZo on a
native Ubuntu 20.04 host.

```
sudo docker build -t videzzo:latest .
sudo docker run --rm -it -v $PWD:/root/videzzo videzzo:latest /bin/bash
```

## Fuzzing process

In practice, we fuzz QEMU and VirtualBox virtual devices as follows.

1. Maintain ViDeZZo (ViDeZZo's Maintainer)

>Fix any bugs in ViDeZZo, adjust fuzzing policies, tune performance, and new
features to ViDeZZo, e.g., add new virtual device target.

+ Add a new virtual device target: follow `predefined_configs` in both
[videzzo_qemu.c](./videzzo_qemu/videzzo_qemu.c) and
[VBoxViDeZZo.cpp](./videzzo_vbox/VBoxViDeZZo.cpp); follow this
[manual](./docs/IntraMessageDependenciesManuals.md) and update
[videzzo_types_gen_vmm.py](./videzzo_types_gen_vmm.py).

2. Deploy ViDeZZo Locally (Automation Script)

>Build both QEMU and VirtualBox targets, and fuzz all virtual devices on a
machine. Usually, we enable ASAN and UBSAN. Considering the number of resources,
virtual devices, and hours we have, we deploy the fuzzing campaign
automatically.

+ Build: `cd videzzo`, and then `make qemu` or `make vbox`.

+ Test: To test a virtual device, go to `videzzo_qemu/out-san` or
`videzzo_vbox/out-san`, and then run binary there. Note that, to test VBox
virtual device, start with `sudo`.

3. Triage bugs (Security Analyst)

>Evaluate security impacts of crashes, fix bugs and verify, submit patches and
discuss in communities. Apply for CVE and advertise if it is necessary.

+ Collect historical seeds: run the crashed fuzz target with `CORPUS`.
+ Delta-debug and gen a PoC: run `02-dd.sh -t ABS_PATH_TO_BINARY -c
ABS_PATH_TO_CRASHING_SEED -s ABS_PATH_TO_CORPUS`.

## Source code coverage profiling

With source code coverage profiling, we know what we can or cannot improve. To
enable the profiling, `make qemu-cov` or `make vbox-cov`, and then run binary in
`videzzo_qemu/out-cov` or `videzzo_vbox/out-cov`. Next, pick up any uncover code
and update the ViDeZZo to support it.

Part of code is not covered by ViDeZZo due to the following reasons. Currently,
we do not have a plan to support them.

+ VM Snapshot
+ Device plug in/out

## Contribution

If any questions and ideas, please do not hesitate to raise an issse or a pull
request.
