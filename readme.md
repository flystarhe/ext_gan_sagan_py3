# Low dose CT
基于条件生成对抗网络的清晰度感知低剂量CT去噪,推荐阅读原项目[xinario/SAGAN](https://github.com/xinario/SAGAN).我仅仅修改了少量代码使支持Python3.

## Getting Started
Install torch7:
```bash
$ git clone https://github.com/torch/distro.git ~/torch --recursive
$ cd ~/torch; bash install-deps;
$ ./install.sh
$ source ~/.bashrc
```

test torch:
```bash
$ th
th> torch.Tensor{1,2,3} + 2
th> os.exit()
```

Install torch packages nngraph and hdf5:
```bash
luarocks install nngraph

sudo apt-get install libhdf5-serial-dev hdf5-tools
git clone https://github.com/deepmind/torch-hdf5
cd torch-hdf5
luarocks make hdf5-0-0.rockspec LIBHDF5_LIBDIR="/usr/lib/x86_64-linux-gnu/"
```

set `HDF5_INCLUDE_PATH`:
```
#vim ~/torch/install/share/lua/5.1/hdf5/config.lua
hdf5._config = {
    HDF5_INCLUDE_PATH = "/usr/lib/x86_64-linux-gnu/hdf5/serial/include",
    HDF5_LIBRARIES = "/home/hejian/anaconda3/lib/libhdf5.so;/usr/lib/x86_64-linux-gnu/librt.so;/usr/lib/x86_64-linux-gnu/libpthread.so;/usr/lib/x86_64-linux-gnu/libz.so;/usr/lib/x86_64-linux-gnu/libdl.so;/usr/lib/x86_64-linux-gnu/libm.so"
}
```

or set value `/usr/include/hdf5/serial`.

Clone this repo into `{slyx-sr}/modules/gan_sagan`.

Download the pretrained denoising model from [here](https://1drv.ms/u/s!Aj4IQl4ug0_9gj4TTqVW1JhhHG5f), and put it into the `checkpoints/SAGAN` folder.

Prepare your test set with the provided python script:
```bash
#make a directory to store your raw dicoms, `/data2/tmps/tmps/dicoms`
#then put all your low dose CT images of dicom format into this folder and run
python pre_process.py -s 1 -i /data2/tmps/tmps/dicoms -o /data2/tmps/tmps/experiment/test
#all your test images would now be saved as uint16 png format inside folder `/data2/tmps/tmps/experiment`.
#Arguement `-s 1` is to ensure the output images are stored in sequence.
#note: in order to use the python script, make sure you have the follwing packages installed
#opencv, pydicom, numpy, h5py
```

Test the model:
```bash
DATA_ROOT=/data2/tmps/tmps/experiment name=SAGAN which_direction=AtoB phase=test th test.lua
#the results are saved in `results/SAGAN/latest_net_G_test/result.h5`
```

Display the result with a specific window:
```bash
python post_process.py -w 'lung'  #lung, abdomen, bone, none
```

Now you can view the result by open the html `results/SAGAN/latest_net_G_test/index.html`.

## paper
- Sharpness-aware Low dose CT denoising using conditional generative adversarial network, [web](https://arxiv.org/abs/1708.06453v2)
- A Simple Low-dose X-ray CT Simulation from High-dose Scan, [web](https://www.ncbi.nlm.nih.gov/pubmed/26543245)

## dataset

### Blur detection dataset
[web](http://www.cse.cuhk.edu.hk/~leojia/projects/dblurdetect/dataset.html)