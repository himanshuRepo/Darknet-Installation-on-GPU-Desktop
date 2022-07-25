# Steps to install Darknet Framework on Ubuntu 20.04 Desktop with GPU 

## Step 1: Install following dependencies through terminal:
```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install build-essential cmake unzip pkg-config
sudo apt-get install gcc-6 g++-6
sudo apt-get install libxmu-dev libxi-dev libglu1-mesa libglu1-mesa-dev
sudo apt-get install libjpeg-dev libpng-dev libtiff-dev
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
sudo apt-get install libxvidcore-dev libx264-dev
sudo apt-get install libopenblas-dev libatlas-base-dev liblapack-dev gfortran
sudo apt-get install libhdf5-serial-dev
sudo apt-get install python3-dev python3-tk python-imaging-tk
sudo apt-get install libgtk-3-dev
```
> Adding ppa:graphics-drivers/ppa repository into your system:
```
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update
```
## Step 2: Install the NVidia Driver compatible with CUDA
> (Referred: https://linuxize.com/post/how-to-nvidia-drivers-on-ubuntu-20-04/)
- Run the following command to get information about your graphic card and available drivers:
```
ubuntu-drivers devices
```
- Install the recommended driver (Eg., nvidia-driver-440):
```
sudo apt install nvidia-driver-440
```
- Once the installation is completed, reboot your system:
```
sudo reboot
```
- On boot up, view the driver setting by launch the nvidia-settings utility:
```
sudo nvidia-settings
```
- Also, run the following command to verify the installation:
```
nvidia-smi
```
## Step 3: Install the cuda and cudnn
> (Referred: https://techzizou.com/install-cuda-and-cudnn-on-windows-and-linux/#linux)
### Download and install CUDA Toolkit
- Open the terminal and following commands
```
cd ~
mkdir installers
cd installers/
```
- Go to https://developer.nvidia.com/cuda-downloads and Select the appropriate options for Target Platform and run following commands (Eg, Suppose you are downloading cuda_11.7.0_515.43.04_linux.run):
```
wget https://developer.download.nvidia.com/compute/cuda/11.7.0/local_installers/cuda_11.7.0_515.43.04_linux.run
sudo sh cuda_11.7.0_515.43.04_linux.run
```
- CUDA installer window appears, de-select the driver (as it is already installed) and press enter to perform installation.
- Run following commands on terminal to change .bashrc:
```
gedit  ~/.bashrc
```
- Add following lines in .bashrc:
```
export PATH=/usr/local/cuda-11.7/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-11.7/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```
- Close the .bashrc and refresh the shell environment by running following command: 
```
source ~/.bashrc
```
- Verify installation of cuda by running following command:
```
nvcc –V
```
> **_Note:_** Incase above step shows cuda version other than the installed one, then
> - (i) Run following command to change the simulink of the cuda with the installed cuda version:
> ```
> sudo ln -sfT /usr/local/cuda/cuda-11.7/ /usr/local/cuda
> ```
> - (ii) Verify the change in simulink by running following link (The arrow in the result should point to installed version):
> ```
> ls -l /usr/local | grep cuda
> ```
> - (iii) Again verify installation of cuda by running following command:
> ```
> nvcc –V
> ```
### Download and setup CUDNN
- Go to https://developer.nvidia.com/cudnn  to download the latest version of CUDNN for the latest CUDA toolkit version (Note: You have to be signed in using your Nvidia account to download CUDNN).
- Download only the zip file of the cudnn library.
- Copy the downloaded cuDNN zip file to the installers folder.
- Unzip it using following command (Eg, cudnn-11.0-linux-x64-v8.0.5.39.tgz):
```
tar -zxf cudnn-11.0-linux-x64-v8.0.5.39.tgz
```
- Next, we need the contents of the include & lib64 folders from cuDNN to be inside the include and lib64 folders of CUDA directory. So, run following commands on terminal inside the installers folder:
```
cd cuda
sudo cp -P lib64/* /usr/local/cuda/lib64/
sudo cp -P include/* /usr/local/cuda/include/
```
## Step 4: Build OpenCV from source with CUDA support
> **_Note:_**
> 1. Assuming python3 is already installed in the system,
> 2. Installing opencv without creating the virtual environment, 
> 3. Referred link: https://cv-tricks.com/installation/opencv-4-1-ubuntu18-04/
- Verify cuda installation by running following commands:
```
nvidia-smi
nvcc –V
```
- Installing dependencies of OpenCV:
> Note: Run each command individual so that you can see whether every command has executed successfully or not.
```
sudo apt-get update -y
sudo apt-get remove -y x264 libx264-dev # Remove the older version of libx264-dev and x264
sudo apt-get install -y build-essential checkinstall cmake pkg-config yasm
sudo apt-get install -y git gfortran
sudo add-apt-repository -y "deb http://security.ubuntu.com/ubuntu xenial-security main"
sudo apt-get install -y libjpeg8-dev libjasper-dev libpng12-dev
sudo apt-get install -y libtiff5-dev
sudo apt-get install -y libavcodec-dev libavformat-dev libswscale-dev libdc1394-22-dev
sudo apt-get install -y libxine2-dev libv4l-dev
sudo apt-get install -y libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev
sudo apt-get install -y qt5-default libgtk2.0-dev libtbb-dev
sudo apt-get install -y libatlas-base-dev
sudo apt-get install -y libfaac-dev libmp3lame-dev libtheora-dev
sudo apt-get install -y libvorbis-dev libxvidcore-dev
sudo apt-get install -y libopencore-amrnb-dev libopencore-amrwb-dev
sudo apt-get install -y x264 v4l-utils
```
> Some Optional Dependencies
```
sudo apt-get install -y libprotobuf-dev protobuf-compiler
sudo apt-get install -y libgoogle-glog-dev libgflags-dev
sudo apt-get install -y libgphoto2-dev libeigen3-dev libhdf5-dev doxygen
```
- Downloading OpenCV 4.5.4 and OpenCV Contrib from Github
```
git clone https://github.com/opencv/opencv.git
cd opencv
git checkout 4.5.4
cd ..
```
```
git clone https://github.com/opencv/opencv_contrib.git
cd opencv_contrib
git checkout 4.5.4
cd ..
```
- Using CMake to build the library
```
cd ~/opencv
mkdir build
cd build
```
- Pass your own flags to build the library:
```
cmake -D CMAKE_BUILD_TYPE=RELEASE \
-D CMAKE_INSTALL_PREFIX=/usr/local \
-D INSTALL_C_EXAMPLES=ON \
-D INSTALL_PYTHON_EXAMPLES=ON \
-D WITH_TBB=ON \
-D WITH_V4L=ON \
-D WITH_QT=ON \
-D WITH_OPENGL=ON \
-D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules \
-D BUILD_EXAMPLES=ON \
-D OPENCV_GENERATE_PKGCONFIG=YES ..
```
- Use make utility for building the library:
> To find the number of threads compatible in your machine run the following command.
```
nproc
```
> The following command specifies the number of jobs with make utility
```
make -j8
```
- Install the library by running the following commands:
```
sudo make install
sudo sh -c 'echo "/usr/local/lib" >> /etc/ld.so.conf.d/opencv.conf'
sudo ldconfig
```
- Verify the contents of opencv4.pc file by running following commands:
```
cd ~/Desktop/opencv/build/unix-install/
gedit opencv4.pc
```
> The contents of opencv4.pc file should be following:
```
prefix=/usr/local
exec_prefix=${prefix}
libdir=${exec_prefix}/lib
includedir=${prefix}/include/opencv4
```
- Relocate the opencv4.pc file
> Create a new directory ‘pkgconfig’ in the location /usr/local/lib/
```
cd /usr/local/lib/
mkdir pkgconfig
```
> Move or Copy the opencv4.pc file to the newly created directory
```
sudo cp ~/Desktop/opencv/build/unix-install/opencv4.pc /usr/local/lib/pkgconfig/
```
- Adding file location to PKG_CONFIG_PATH Variable and modifying .bashrc file:
> Open .bashrc file
```
sudo gedit ~/.bashrc
```
> Add the following 2 lines at the end of the file i.e. copy the following lines at the end of .bashrc file.
```
PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig
export PKG_CONFIG_PATH
```
> Save and close the .bashrc file and run the following command.
```
source ~/.bashrc
```
> Verifying whether the path is added or not by executing the following command on terminal:
```
echo $PKG_CONFIG_PATH
```
- Verifying the Installation
```
pkg-config --modversion opencv4
python3 -c "import cv2; print(cv2.__version__)"
```
> **_Note:_** Incase the last step results different versions, then:
> 
> (i) Verify the simulink of cv2.so by running the following command (refer: https://linuxhint.com/remove-symbolic-link-linux):
> ```
> ls –l
> ```
>  If it is not pointing to correct cv2.so file, follow the steps of symbolic link in the following link and correct it: https://techzizou.com/setup-opencv-dnn-cuda-module-for-linux/
> 
> (ii) If you want python to choose specific version of opencv or specific cv2.so of opencv, then follow the link: https://stackoverflow.com/questions/44647906/how-to-make-python-to-choose-a-specific-version-of-opencv
## Step 5: Installing the Darknet Framework:
> **_Note:_** For successful installation of Darknet, follow the installation sequence strictly i.e., install cuda and cudnn before installing opencv (Step 1, Step 2, Step 3, and Step 4.)
- Open a terminal and clone the darknet git repository using the following command:
> Visit official AlexeyAB’s GitHub to learn more about darknet.
```
git clone https://github.com/AlexeyAB/darknet.git
```
- Go inside the darknet folder and open the makefile to do following changes: 
> For reference, you can do changes based on following link: https://github.com/AlexeyAB/darknet/#how-to-compile-on-linux-using-make
```
GPU=1
CUDNN=1
CUDNN_HALF=1
OPENCV=1
LIBSO=1
```
- In makefile, set the ARCH according to the compute capability as per your GPU Architecture.
> To know compute capability of GPU Architecture refer following link: https://developer.nvidia.com/cuda-gpus (Eg., Your compute capability is 8.6)
```
ARCH= -gencode arch=compute_86,code=[sm_86,compute_86]
```
- Run make command:
```
cd darknet
make
```
> **_Note:_** Incase above step gives lcuda error, do following changes in the makefile and rerun above command.
> 
> (Referred: https://stackoverflow.com/questions/62999715/when-i-make-darknet-with-cuda-1-usr-bin-ld-cannot-find-lcudaoccured-how)
> ```
> LDFLAGS+= -L/usr/local/cuda/lib64 -lcudart -lcublas -lcurand -L/usr/local/cuda/lib64/stubs -lcuda
> ```
- Verify the installation by typing the following:
```
./darknet
```
> The o/p should be:
> ``` 
> usage: ./darknet <function>
> ```

