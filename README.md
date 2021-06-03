# oneAPI on CUDA setup script

This script allows you to build and setup the DPC++ compiler, oneMKL and oneDNN with Nvidia GPUs support. 

### Use
1. If needed, set `$DPCPP_HOME` and `$CUDA_ROOT` in the script.
2. Run the script with ```./build.sh```.

Everything will be installed to `$DPCPP_HOME/deploy`. 

To build with testing support and run the tests for DPC++, oneMKL, oneTBB, oneDNN and Lapack, run: ```DPCPP_TESTS=ON ./build.sh```

### Environment variables
Once everything was built, add the first four exports of the script in your environment or add them in your shell's config file. 


### Using the CUDA Backend
1. Use the following selector:
```C++
class CUDADeviceSelector : public sycl::device_selector {
public:
    int operator()(const sycl::device &device) const override {
        return device.get_platform().get_backend() == sycl::backend::cuda ? 1 : -1;
    }
};
```
2. Build with `-fsycl -fsycl-targets=nvptx64-nvidia-cuda-sycldevice`
3. For oneMKL, link with: `-lonemkl_blas_cublas -lonemkl`


### Intel's openCL
You can also install Intel's openCL driver so you can target your CPU with `-fsycl-targets=spir64_x86_64-unknown-unknown-sycldevice`. 


### Dependencies
* Even though the latest version of CUDA seems to work with DPC++, we need to use CUDA 10.2 as oneMKL uses cuBLAS 10.2. On RHEL: `cuda-10-2 libcublas-devel-10-2`. For the setup see: https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#pre-installation-actions. 
* `libatomic`
* `intel-oneapi-runtime-opencl` to be able to target Intel's CPUs with openCL. For the setup see: https://software.intel.com/content/www/us/en/develop/documentation/installation-guide-for-intel-oneapi-toolkits-linux/top/installation/install-using-package-managers.html
* `ninja-build cmake`


### Caveats
* oneMKL does not support testing when building for both cuBLAS and cuRAND
* for oneMKL with cuda you should use the namespace `oneapi::mkl::blas::column_major::` as cuBLAS is column_major. 

### Repositories used
- OpenCL Headers+Loaders: https://github.com/KhronosGroup/OpenCL-Headers.git, https://github.com/KhronosGroup/OpenCL-ICD-Loader.git
- DPC++ Compiler: https://github.com/intel/llvm.git
- NETLIB Lapack: https://github.com/Reference-LAPACK/lapack.git
- oneTBB: https://github.com/oneapi-src/oneTBB.git
- oneMKL: https://github.com/oneapi-src/oneMKL.git
- oneDNN: https://github.com/oneapi-src/oneDNN.git