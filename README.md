# container_m100_devel

Basic container to deploy software for Marconi 100. Software installed inside:

- Mellanox drivers MLNX_OFED_LINUX-4.7-3.2.9.1-rhel7.6alternate-ppc64le
- Nvidia drivers nvidia-driver-440.64.00-ppc64le
- GNU compiler 4.8.5
- Spack 16.0
- Lmod 8.2.7, module environment

IMPORTANT: When you are going to work inside the container remember to source these 2 file in order to set the proper module environment with spack and Lmod

- source /opt/spack/share/spack/setup-env.sh
- source /usr/share/lmod/8.2.7/init/sh
