Bootstrap: docker
From: centos:centos7.6.1810
IncludeCmd: yes

# IMPORTANT: When you are going to work inside the container remember to source these 2 file in order to set the proper module environment with spack and Lmod
# source /opt/spack/share/spack/setup-env.sh && source /usr/share/lmod/8.2.7/init/sh

%post

yum -y install epel-release
yum -y groupinstall "Development tools"
yum install -y c-ares c-ares-devel Lmod
yum install -y  bzip2  gzip tar  zip unzip xz curl wget vim patch make cmake file git which perl-Data-Dumper perl-Thread-Queue boost-devel
yum install -y numactl-libs gtk2 atk cairo tcsh lsof ethtool tk pciutils libnl3 libmnl libudev-devel

# INSTALL MELLANOX

cd /opt
wget https://b2drop.eudat.eu/s/xM8dSwEXDXj64Xn/download -O MLNX_OFED_LINUX-4.7-3.2.9.1-rhel7.6alternate-ppc64le.tar.gz
tar xzvf MLNX_OFED_LINUX-4.7-3.2.9.1-rhel7.6alternate-ppc64le.tar.gz
cd MLNX_OFED_LINUX-4.7-3.2.9.1-rhel7.6alternate-ppc64le
./mlnxofedinstall --force --without-32bit --without-fw-update --user-space-only
cd /opt 
rm -rf MLNX_OFED_LINUX-4.7-3.2.9.1-rhel7.6alternate-ppc64le.tar.gz MLNX_OFED_LINUX-4.7-3.2.9.1-rhel7.6alternate-ppc64le


# INSTALL CUDA DRIVERS

cd /opt
wget https://b2drop.eudat.eu/s/Xd9YmJd9ZQzKwM2/download -O cuda_driver_m100.tar.gz
tar xvf cuda_driver_m100.tar.gz
yum -c /opt/nvidia-driver-local-repo-440.64.00/etc/yum.repos.d/nvidia-driver-local-440.64.00.repo install -y cuda-drivers
rm -f cuda_driver_m100.tar.gz


#yum install -y openssl libibverbs-devel rdma-core-devel openssl-devel binutils dapl dapl-utils ibacm infiniband-diags libibverbs libibverbs-utils
#yum install -y libmlx4 librdmacm librdmacm-utils mstflint opensm-libs perftest qperf rdma libjpeg-turbo-devel libpng-devel openssh-clients 
#yum install -y openssh-server subversion libffi libffi-devel scl-utils 
#yum install -y libpsm2 libpsm2-devel pmix pmix-devel

rm -rf /var/cache/yum 
yum clean all

# INSTALL SPACK

cd /opt 
git clone https://github.com/spack/spack.git
cd spack
git checkout tags/v0.16.0

# SPACK ENVIRONMENT

export SPACK_ROOT=/opt/spack
export PATH=${SPACK_ROOT}/bin:$PATH
export MODULEPATH=/opt/spack/share/spack/modules/linux-centos7-power8le:/opt/spack/share/spack/modules/linux-centos7-power9le

spack --version

# INSTALL GNU 8.4.0 WITH SPACK 

cat > /opt/spack/etc/spack/defaults/linux/compilers.yaml <<EOF
compilers:
- compiler:
    spec: gcc@4.8.5
    paths:
      cc: /bin/gcc
      cxx: /bin/g++
      f77: /bin/gfortran
      fc: /bin/gfortran
    flags: {}
    operating_system: centos7
    target: ppc64le
    modules: []
    environment: {}
    extra_rpaths: []
EOF

#- compiler:
#    spec: gcc@8.4.0
#    paths:
#      cc: /opt/spack/opt/spack/linux-centos7-power8le/gcc-4.8.5/gcc-8.4.0-4k4ayh3zzn6jiakojj2vkqtlq6j3y7rn/bin/gcc
#      cxx: /opt/spack/opt/spack/linux-centos7-power8le/gcc-4.8.5/gcc-8.4.0-4k4ayh3zzn6jiakojj2vkqtlq6j3y7rn/bin/g++
#      f77: /opt/spack/opt/spack/linux-centos7-power8le/gcc-4.8.5/gcc-8.4.0-4k4ayh3zzn6jiakojj2vkqtlq6j3y7rn/bin/gfortran
#      fc: /opt/spack/opt/spack/linux-centos7-power8le/gcc-4.8.5/gcc-8.4.0-4k4ayh3zzn6jiakojj2vkqtlq6j3y7rn/bin/gfortran
#    flags: {}
#    operating_system: centos7
#    target: ppc64le
#    modules: []
#    environment: {}
#    extra_rpaths: []
#EOF

#spack install gcc@8.4.0 %gcc@4.8.5


# LINK EXTERNAL SOFTWARE WITH SPACK hcoll and mxm

cat >> /opt/spack/etc/spack/defaults/packages.yaml <<EOF
  hcoll:
    externals:
    - prefix: /opt/mellanox/hcoll
      spec: hcoll@m100
  mxm:
    externals:
    - prefix: /opt/mellanox/mxm
      spec: mxm@m100
EOF


# INSTALL ESSENTIAL SOFTWARE FOR DEVELOPMENT CUDA-LIBRARY + OPENMPI

#spack install openmpi@4.0.3  %gcc@8.4.0 +cuda +pmi +legacylaunchers schedulers=slurm fabrics=ucx,mxm,hcoll ^slurm@19-05-6-1 ^ucx@1.7.0 +cuda cuda_arch=70 ^cuda@10.2.89

#spack gc -y

#spack install py-horovod@0.19.2%gcc@7.3.1 frameworks=pytorch,tensorflow \
#  ^py-tensorflow@2.0.0%gcc@7.3.1 +cuda +mpi +numa cuda_arch=70 \
#  ^py-torch@1.5.0%gcc@7.3.1 cuda_arch=70 ~mkldnn +caffe2 \
#  ^bazel@0.26.1%gcc@7.3.1 \
#  ^nccl@2.7.8-1%gcc@7.3.1+cuda cuda_arch=70 \
#  ^openmpi@4.0.3 %gcc@7.3.1 +cuda +pmi +legacylaunchers schedulers=slurm +singularity fabrics=ucx,psm2,verbs \
#  ^slurm@19-05-6-1%gcc@7.3.1 \
#  ^cuda@10.1.243%gcc@7.3.1 \
#  ^cudnn@7.6.4.38-10.1-linux-x64%gcc@7.3.1 \
#  ^ucx@1.8.1%gcc@7.3.1+cuda cuda_arch=70


%environment

export SPACK_ROOT=/opt/spack
export PATH=/usr/share/lmod/8.2.7/libexec/lmod:${SPACK_ROOT}/bin:$PATH
export LMOD_CMD=/usr/share/lmod/8.2.7/libexec/lmod
export MODULEPATH=/opt/spack/share/spack/modules/linux-centos7-power8le:/opt/spack/share/spack/modules/linux-centos7-power9le


%startscript

source /opt/spack/share/spack/setup-env.sh 
source /usr/share/lmod/8.2.7/init/sh

