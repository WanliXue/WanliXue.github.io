---
layout: post
title: "korean server note"
date: 2017-04-05
---


ssh wanli@210.107.198.223

pw: xuewanli123


#check video card situation
nvidia-smi

/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=train_val.prototxt --weights=bvlc_alexnet.caffemodel --iterations 100

/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=train_val.prototxt --weights=bvlc_alexnet.caffemodel --iterations 100


—————
/home/jungmo/apps/caffe/build/tools/caffe.bin train -solver solver.prototxt —-gpu 0,1

/home/jungmo/apps/caffe/build/tools/caffe.bin train -solver /home/wanli/alexnet_otherdatast/solver_alexnet_cifar.prototxt -snapshot /home/wanli/alexnet_otherdatast/alex_cifar_iter_150000.solverstate
—————

add
import sys
sys.path.append("/home/jungmo/apps/caffe/python") # caffe path
before 'import caffe'.

import sys
sys.path.append("/home/jungmo/apps/caffe/python")
import caffe

matlab
h5disp('cifar10_full_iter_60000.caffemodel.h5')



net = caffe.Net('/home/wanli/caffe_local/deploy.prototxt',
'/home/wanli/caffe_local/bvlc_alexnet.caffemodel',
caffe.TEST)

net = caffe.Net('/home/wanli/alexnet_otherdatast/deploy_cifar.prototxt',
'/home/wanli/alexnet_otherdatast/Alexnet_cifar.caffemodel',
caffe.TEST)


#if using deploy. need weight, if using train_val, assuming require data file

python extract.py --model=deploy.prototxt --weights=bvlc_alexnet.caffemodel --output=extract

python extract.py --model=cifar10_full_train_test.prototxt --weights=cifarfull10_iter_60000.caffemodel --output=cifar10_extract

import numpy as np
np.load("7x7_s2_0.npy”)

this cannot show the all and not easy to process.

we find a code turn npy to matlab code:
https://github.com/kwikteam/npy-matlab

then process with matlab, sucessfully read the weight fc8_0.pny 1000x4096
b=readNPY('fc8_0.npy'); //matlab code



——————————modifying the parameter in weights————————————
1st import paramater

import numpy as np
import sys
sys.path.append("/home/jungmo/apps/caffe/python")
import caffe


#if using deploy. need weight, if using train_val, assuming require data file
net = caffe.Net('/home/wanli/caffe_local/deploy.prototxt',
'/home/wanli/caffe_local/bvlc_alexnet.caffemodel',
caffe.TEST)

net = caffe.Net('/home/wanli/caffe_local/train_val.prototxt',
'/home/wanli/caffe_local/bvlc_alexnet.caffemodel',
caffe.TEST)

——


net = caffe.Net('/home/wanli/alexnet_otherdatast/cifar10_full_train_test.prototxt',
'/home/wanli/alexnet_otherdatast/cifarfull10_iter_60000.caffemodel',
caffe.TEST)

———
net = caffe.Net('/home/wanli/alexnet_otherdatast/train_val_alex_cifar.prototxt',
'/home/wanli/alexnet_otherdatast/Alexnet_cifar.caffemodel',
caffe.TEST)
fc6 weights are (1024, 288) dimensional and biases are (1024,) dimensional
fc7 weights are (1024, 1024) dimensional and biases are (1024,) dimensional
fc8 weights are (27, 1024) dimensional and biases are (27,) dimensional

print("blobs {}\nparams {}".format(net.blobs.keys(), net.params.keys()))



net = caffe.Net('/home/wanli/caffe_local/modified_alexnet_train.prototxt', '/home/wanli/caffe_local/bvlc_alexnet.caffemodel', caffe.TEST)



2.
W = net.params['conv1'][0].data[...]  #print the weight of layer conv1

fc7=net.params['fc7'][0].data
———————————————————————

net = caffe.Net('/home/wanli/caffe_local/deploy.prototxt', '/home/wanli/caffe_local/bvlc_alexnet.caffemodel', caffe.TEST)

###net = caffe.Net('/home/wanli/caffe_local/alex_deploy_modified.prototxt',
'/home/wanli/caffe_local/wanli_alexnet.caffemodel',
caffe.TEST)

params = ['fc6', 'fc7', 'fc8']
params = ['fc6', 'fc6.5', 'fc7.1', 'fc8']

# fc_params = {name: (weights, biases)}
fc_params = {pr: (net.params[pr][0].data, net.params[pr][1].data) for pr in params}

for fc in params:
print '{} weights are {} dimensional and biases are {} dimensional'.format(fc, fc_params[fc][0].shape, fc_params[fc][1].shape)

fc6 weights are (4096, 9216) dimensional and biases are (4096,) dimensional
fc6.5 weights are (2000, 4096) dimensional and biases are (2000,) dimensional
fc7.1 weights are (4096, 2000) dimensional and biases are (4096,) dimensional
fc8 weights are (1000, 4096) dimensional and biases are (1000,) dimensional

用proto生成带有6.5层的caffemodel，然后在更改model的参数

根据net——surgery 可以直接用不同的.caffemodel 放在deploy.prototxt 里，有的layer会自动对应，没有的参数就为0，比如fc6.5和fc7.1，但是weight矩阵的纬度（方向）很奇怪。猜测是转换的。
并不是转换，而是weight在前面乘，所以先乘v再乘U. W = U*V

fc6_5=np.load('fc6_5.npy')
fc7_1=np.load('fc7_1.npy')

new model parameter is:
wanli_alexnet_update.caffemodel

so read the net become:
net = caffe.Net('/home/wanli/caffe_local/alex_deploy_modified.prototxt',
'/home/wanli/caffe_local/wanli_alexnet_update.caffemodel',
caffe.TEST)





————————————
when testing, require the mean profile. I modified the create_mean.sh from CIFAR-10 example, to generate the mean. But came across error when running.
solving try http://stackoverflow.com/questions/18172405/getting-error-usr-bin-env-sh-no-such-file-or-directory-when-running-command-p
dos2unix create_alexnet_mean.sh
(not working, directly modified cifar_10 create file is easier)


/home/jungmo/apps/caffe/build/tools/compute_image_mean -backend=lmdb \
~/ilsvrc12_val_lmdb ~/alex_test_mean.binaryproto

(I failed because to make the mean file require the training dataset instead of validating dataset, so jungmo did it for me)


#validation command
/home/jungmo/apps/caffe/build/tools/caffe test -model /home/wanli/caffe_local/modified_alexnet_train.prototxt -weights /home/wanli/caffe_local/wanli_alexnet_update.caffemodel -gpu all -iterations 100


top-5 accuracy
/home/jungmo/apps/caffe/build/tools/caffe test -model /home/wanli/caffe_local/modified_alexnet_train_top5.prototxt -weights /home/wanli/caffe_local/wanli_alexnet_update.caffemodel -gpu all -iterations 100


/home/jungmo/apps/caffe/build/tools/caffe test -model /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar10_full_train_test.prototxt -weights /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifarfull10_iter_60000.caffemodel -gpu all -iterations 100




raw#
/home/jungmo/apps/caffe/build/tools/caffe test -model /home/wanli/caffe_local/alex_train_val.prototxt -weights /home/wanli/caffe_local/bvlc_alexnet.caffemodel -gpu all -iterations 100




———————————
data structure:

HDF5 cifar10_full_iter_60000.caffemodel.h5
Group '/'
Group '/data'
Group '/data/cifar'
Group '/data/conv1'
Dataset '0'
Size:  5x5x3x32
MaxSize:  5x5x3x32
Datatype:   H5T_IEEE_F32LE (single)
ChunkSize:  []
Filters:  none
FillValue:  0.000000
Dataset '1'
Size:  32
MaxSize:  32
Datatype:   H5T_IEEE_F32LE (single)
ChunkSize:  []
Filters:  none
FillValue:  0.000000
Group '/data/conv2'
Dataset '0'
Size:  5x5x32x32
MaxSize:  5x5x32x32
Datatype:   H5T_IEEE_F32LE (single)
ChunkSize:  []
Filters:  none
FillValue:  0.000000
Dataset '1'
Size:  32
MaxSize:  32
Datatype:   H5T_IEEE_F32LE (single)
ChunkSize:  []
Filters:  none
FillValue:  0.000000
Group '/data/conv3'
Dataset '0'
Size:  5x5x32x64
MaxSize:  5x5x32x64
Datatype:   H5T_IEEE_F32LE (single)
ChunkSize:  []
Filters:  none
FillValue:  0.000000
Dataset '1'
Size:  64
MaxSize:  64
Datatype:   H5T_IEEE_F32LE (single)
ChunkSize:  []
Filters:  none
FillValue:  0.000000
Group '/data/ip1'
Dataset '0'
Size:  1024x10
MaxSize:  1024x10
Datatype:   H5T_IEEE_F32LE (single)
ChunkSize:  []
Filters:  none
FillValue:  0.000000
Dataset '1'
Size:  10
MaxSize:  10
Datatype:   H5T_IEEE_F32LE (single)
ChunkSize:  []
Filters:  none
FillValue:  0.000000
Group '/data/loss'
Group '/data/norm1'
Group '/data/norm2'
Group '/data/pool1'
Group '/data/pool2'
Group '/data/pool3'
Group '/data/relu1'
Group '/data/relu2'
Group '/data/relu3'


———————————
using normal solver
alex_cifar_32_train_val.prototxt (raw alexnet model only change crop size to 32 and output class 10)

not work, error

—————
using normal solver
alex_para_modi_cifar.prototxt (raw alexnet model changed some layer dimension according to download file)
crop size 31, each conv and fc layer got updated, output 10)

failed, after 150K iteration, accuracy only 0.1
——————

/home/wanli/alexnet_otherdatast/cifar10_full_caffe/caffe_cifar10_suggery

separate caffe_cifar10_example  ip1 to ip0.5 and ip1.1

10x1000 9x1000   10x9

net = caffe.Net('/home/wanli/alexnet_otherdatast/cifar10_full_caffe/caffe_cifar10_suggery/cifar10_full_train_test.prototxt’, '/home/wanli/alexnet_otherdatast/cifar10_full_caffe/caffe_cifar10_suggery/cifarfull10_iter_60000.caffemodel’, caffe.TEST)


—modifed  separate to 2 layer without noise —10x1000 9x1000   10x9—
/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=wanli_cifar10_full_train_test.prototxt --weights=wanli_caffe_cifar_update.caffemodel --iterations 100 -gpu all

result:
I0605 14:02:42.237463  7380 caffe.cpp:318] Loss: 0.741915
I0605 14:02:42.237478  7380 caffe.cpp:330] accuracy = 0.7546
I0605 14:02:42.237488  7380 caffe.cpp:330] loss = 0.741915 (* 1 = 0.741915 loss)


—— start to add dp noises to 2 layers—— (add to dictionary 10x9)———

————eps :1
/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=wanli_cifar10_full_train_test.prototxt --weights=wanli_caffe_cifar_eps1.caffemodel --iterations 100 -gpu all

I0612 16:04:29.683989 15777 caffe.cpp:318] Loss: 38.7366
I0612 16:04:29.684002 15777 caffe.cpp:330] accuracy = 0.0908
I0612 16:04:29.684012 15777 caffe.cpp:330] loss = 38.7366 (* 1 = 38.7366 loss)
——————eps:5

/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=wanli_cifar10_full_train_test.prototxt --weights=wanli_caffe_cifar_eps5.caffemodel --iterations 100 -gpu all

I0612 15:53:39.294044 15470 caffe.cpp:318] Loss: 8.64625
I0612 15:53:39.294057 15470 caffe.cpp:330] accuracy = 0.1876
I0612 15:53:39.294085 15470 caffe.cpp:330] loss = 8.64625 (* 1 = 8.64625 loss)

——————eps:7
/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=wanli_cifar10_full_train_test.prototxt --weights=wanli_caffe_cifar_eps7.caffemodel --iterations 100 -gpu all

I0612 16:06:37.046643 15848 caffe.cpp:318] Loss: 2.59091
I0612 16:06:37.046672 15848 caffe.cpp:330] accuracy = 0.4061
I0612 16:06:37.046700 15848 caffe.cpp:330] loss = 2.59091 (* 1 = 2.59091 loss)

——————eps:8.5
/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=wanli_cifar10_full_train_test.prototxt --weights=wanli_caffe_cifar_eps8_5.caffemodel --iterations 100 -gpu all

I0612 16:09:09.899412 15954 caffe.cpp:318] Loss: 2.64174
I0612 16:09:09.899425 15954 caffe.cpp:330] accuracy = 0.48
I0612 16:09:09.899452 15954 caffe.cpp:330] loss = 2.64174 (* 1 = 2.64174 loss)


——eps :9

/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=wanli_cifar10_full_train_test.prototxt --weights=wanli_caffe_cifar_eps9.caffemodel --iterations 100 -gpu all

I0612 16:11:07.719035 16027 caffe.cpp:318] Loss: 1.88365
I0612 16:11:07.719050 16027 caffe.cpp:330] accuracy = 0.4778
I0612 16:11:07.719079 16027 caffe.cpp:330] loss = 1.88365 (* 1 = 1.88365 loss)

————eps :9.5

/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=wanli_cifar10_full_train_test.prototxt --weights=wanli_caffe_cifar_eps9_5.caffemodel --iterations 100 -gpu all

I0612 16:13:01.286469 16097 caffe.cpp:318] Loss: 1.21606
I0612 16:13:01.286507 16097 caffe.cpp:330] accuracy = 0.6697
I0612 16:13:01.286536 16097 caffe.cpp:330] loss = 1.21606 (* 1 = 1.21606 loss)

————eps: 10
/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=wanli_cifar10_full_train_test.prototxt --weights=wanli_caffe_cifar_eps10.caffemodel --iterations 100 -gpu all

I0612 15:58:40.667547 15548 caffe.cpp:318] Loss: 1.1029
I0612 15:58:40.667562 15548 caffe.cpp:330] accuracy = 0.6676
I0612 15:58:40.667587 15548 caffe.cpp:330] loss = 1.1029 (* 1 = 1.1029 loss)

——eps : 15
/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=wanli_cifar10_full_train_test.prototxt --weights=wanli_caffe_cifar_eps15.caffemodel --iterations 100 -gpu all

I0612 16:00:49.944980 15620 caffe.cpp:318] Loss: 0.897609
I0612 16:00:49.945008 15620 caffe.cpp:330] accuracy = 0.6981
I0612 16:00:49.945035 15620 caffe.cpp:330] loss = 0.897609 (* 1 = 0.897609 loss)

———raw————————————
/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=cifar10_full_train_test.prototxt --weights=cifarfull10_iter_60000.caffemodel --iterations 100 -gpu all

I0605 14:48:49.377022  7858 caffe.cpp:318] Loss: 0.648308
I0605 14:48:49.377035  7858 caffe.cpp:330] accuracy = 0.7841
I0605 14:48:49.377063  7858 caffe.cpp:330] loss = 0.648308 (* 1 = 0.648308 loss)

————add laplacian noise very big—————
/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=wanli_cifar10_full_train_test.prototxt --weights=wanli_caffe_cifar_ip1_2.caffemodel --iterations 100 -gpu all

I0605 16:46:06.148085  8118 caffe.cpp:318] Loss: 21.0641
I0605 16:46:06.148099  8118 caffe.cpp:330] accuracy = 0.1119
I0605 16:46:06.148126  8118 caffe.cpp:330] loss = 21.0641 (* 1 = 21.0641 loss)

————add small laplacian noise — eps 0.01——————
I0606 14:22:26.946291  9390 caffe.cpp:318] Loss: 0.756951
I0606 14:22:26.946305  9390 caffe.cpp:330] accuracy = 0.7498
I0606 14:22:26.946332  9390 caffe.cpp:330] loss = 0.756951 (* 1 = 0.756951 loss)


—————————add dp noise to training data——————————————
#########################

normalise data to [-1, +1] add noise, then normal back to train

/home/jungmo/apps/caffe/build/tools/compute_image_mean -backend=lmdb \
/home/wanli/alexnet_otherdatast/cifar10_s121e0_5_data/cifar10_train_lmdb /home/wanli/alexnet_otherdatast/cifar10_s121e0_5_data/mean.binaryproto

————— add eps 20 sa = 1 normalised to check ———

/home/jungmo/apps/caffe/build/examples/cifar10/convert_cifar_data.bin /home/wanli/cifar10_newdata /home/wanli/alexnet_otherdatast/cifar10_s1e20_data lmdb

nohup /home/jungmo/apps/caffe/build/tools/caffe.bin train --solver /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar10_full_solver.prototxt --gpu all >/home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar_s1e20.log &

I0618 19:27:48.423763 23658 data_layer.cpp:73] Restarting data prefetching from start.
I0618 19:27:48.478471 23649 solver.cpp:398]     Test net output #0: accuracy = 0.5
I0618 19:27:48.478495 23649 solver.cpp:398]     Test net output #1: loss = 2.30259 (* 1 = 2.30259 loss)

/home/jungmo/apps/caffe/build/tools/caffe test -model /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar10_full_train_test.prototxt -weights /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar_noisetrain_iter_60000.caffemodel -gpu all -iterations 100

I0618 20:30:52.403015 24163 caffe.cpp:318] Loss: 2.30259
I0618 20:30:52.403029 24163 caffe.cpp:330] accuracy = 0.5
I0618 20:30:52.403041 24163 caffe.cpp:330] loss = 2.30259 (* 1 = 2.30259 loss)

———add eps 5 sa =1   normalised to check————————
/home/jungmo/apps/caffe/build/examples/cifar10/convert_cifar_data.bin /home/wanli/cifar10_newdata /home/wanli/alexnet_otherdatast/cifar10_s1e5_data lmdb


nohup /home/jungmo/apps/caffe/build/tools/caffe.bin train --solver /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar10_full_solver.prototxt --gpu all >/home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar_s1e5.log &

I0617 15:24:06.543901 17406 solver.cpp:311] Iteration 60000, loss = 2.3026
I0617 15:24:06.543923 17406 solver.cpp:331] Iteration 60000, Testing net (#0)
I0617 15:24:07.862299 17416 data_layer.cpp:73] Restarting data prefetching from start.
I0617 15:24:07.915101 17406 solver.cpp:398]     Test net output #0: accuracy = 0.1
I0617 15:24:07.915127 17406 solver.cpp:398]     Test net output #1: loss = 2.30259 (* 1 = 2.30259 loss)


————add eps 1 sa =1 normalised to check————

/home/jungmo/apps/caffe/build/examples/cifar10/convert_cifar_data.bin /home/wanli/cifar10_newdata /home/wanli/alexnet_otherdatast/cifar10_s1e1_data lmdb

/home/jungmo/apps/caffe/build/examples/cifar10/convert_cifar_data.bin /home/wanli/cifar10_newdata /home/wanli/alexnet_otherdatast/cifar10_s1e1_data lmdb


nohup /home/jungmo/apps/caffe/build/tools/caffe.bin train --solver /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar10_full_solver.prototxt --gpu all >/home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar_s1e1.log &

I0609 19:41:56.410487 29271 solver.cpp:311] Iteration 60000, loss = 0.795319
I0609 19:41:56.410526 29271 solver.cpp:331] Iteration 60000, Testing net (#0)
I0609 19:41:57.748481 29280 data_layer.cpp:73] Restarting data prefetching from start.
I0609 19:41:57.801019 29271 solver.cpp:398]     Test net output #0: accuracy = 0.4431
I0609 19:41:57.801046 29271 solver.cpp:398]     Test net output #1: loss = 2.0495 (* 1 = 2.0495 loss)

/home/jungmo/apps/caffe/build/tools/caffe test -model /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar10_full_train_test.prototxt -weights /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar_noisetrain_iter_60000.caffemodel -gpu all -iterations 100

I0617 16:55:14.821965 18361 caffe.cpp:318] Loss: 2.30259
I0617 16:55:14.821979 18361 caffe.cpp:330] accuracy = 0.5
I0617 16:55:14.822005 18361 caffe.cpp:330] loss = 2.30259 (* 1 = 2.30259 loss)

图片更加模糊了但是精确度上升了

————add eps 0.5 sa =1 normalised to check————

/home/jungmo/apps/caffe/build/examples/cifar10/convert_cifar_data.bin /home/wanli/cifar10_newdata /home/wanli/alexnet_otherdatast/cifar10_s1e0_5_data lmdb


nohup /home/jungmo/apps/caffe/build/tools/caffe.bin train --solver /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar10_full_solver.prototxt --gpu all >/home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar_s1e0_5.log &

I0617 17:43:29.879876 18463 solver.cpp:311] Iteration 60000, loss = 2.3026
I0617 17:43:29.879911 18463 solver.cpp:331] Iteration 60000, Testing net (#0)
I0617 17:43:31.203712 18472 data_layer.cpp:73] Restarting data prefetching from start.
I0617 17:43:31.256686 18463 solver.cpp:398]     Test net output #0: accuracy = 0.5
I0617 17:43:31.256713 18463 solver.cpp:398]     Test net output #1: loss = 2.30259 (* 1 = 2.30259 loss)

/home/jungmo/apps/caffe/build/tools/caffe test -model /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar10_full_train_test.prototxt -weights /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar_noisetrain_iter_60000.caffemodel -gpu all -iterations 100

有问题 参数没更改

—————add eps 0.1 sa = 1 normalised to check———
/home/jungmo/apps/caffe/build/examples/cifar10/convert_cifar_data.bin /home/wanli/cifar10_newdata /home/wanli/alexnet_otherdatast/cifar10_s1e0_1_data lmdb

nohup /home/jungmo/apps/caffe/build/tools/caffe.bin train --solver /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar10_full_solver.prototxt --gpu all >/home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar_s1e0_1.log &

I0617 18:29:56.213620 18742 solver.cpp:311] Iteration 60000, loss = 2.3026
I0617 18:29:56.213641 18742 solver.cpp:331] Iteration 60000, Testing net (#0)
I0617 18:29:57.539074 18751 data_layer.cpp:73] Restarting data prefetching from start.
I0617 18:29:57.593412 18742 solver.cpp:398]     Test net output #0: accuracy = 0.5
I0617 18:29:57.593437 18742 solver.cpp:398]     Test net output #1: loss = 2.30259 (* 1 = 2.30259 loss)

I0617 18:38:25.056553 18970 caffe.cpp:318] Loss: 2.30259
I0617 18:38:25.056568 18970 caffe.cpp:330] accuracy = 0.5



————add eps 0.01 sa = 1

———————————————————————————————————————————————————

———add eps 5 sa=121 noise to CIFAR and train and test————————

/home/jungmo/apps/caffe/build/examples/cifar10/convert_cifar_data.bin /home/wanli/cifar10_newdata /home/wanli/alexnet_otherdatast/cifar10_s121e5_data lmdb

/home/jungmo/apps/caffe/build/tools/caffe.bin train --solver /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar10_full_solver.prototxt --gpu all

nvidia-smi
ps aux | grep wanli

nohup /home/jungmo/apps/caffe/build/tools/caffe.bin train --solver /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar10_full_solver.prototxt --gpu all >/home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar_s121e5.log &

take about 35 mins with 2 gpu

I0609 16:54:22.311400 16711 solver.cpp:311] Iteration 60000, loss = 0.377767
I0609 16:54:22.311424 16711 solver.cpp:331] Iteration 60000, Testing net (#0)
I0609 16:54:23.646196 16767 data_layer.cpp:73] Restarting data prefetching from start.
I0609 16:54:23.699950 16711 solver.cpp:398]     Test net output #0: accuracy = 0.7345
I0609 16:54:23.699982 16711 solver.cpp:398]     Test net output #1: loss = 0.807507 (* 1 = 0.807507 loss)

/home/jungmo/apps/caffe/build/tools/caffe test -model /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar10_full_train_test.prototxt -weights /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar_noisetrain_iter_60000.caffemodel -gpu all -iterations 100

I0609 17:29:51.070551 28223 caffe.cpp:318] Loss: 0.807507
I0609 17:29:51.070565 28223 caffe.cpp:330] accuracy = 0.7345
I0609 17:29:51.070577 28223 caffe.cpp:330] loss = 0.807507 (* 1 = 0.807507 loss)

———————————————————————

—————add eps 2.5 sa=121 noise to CIFAR and train and test————————

/home/jungmo/apps/caffe/build/examples/cifar10/convert_cifar_data.bin /home/wanli/cifar10_newdata /home/wanli/alexnet_otherdatast/cifar10_s121e2_5_data lmdb

nohup /home/jungmo/apps/caffe/build/tools/caffe.bin train --solver /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar10_full_solver.prototxt --gpu all >/home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar_s121e2_5.log &

I0609 18:30:09.807029 28372 solver.cpp:311] Iteration 60000, loss = 0.467952
I0609 18:30:09.807051 28372 solver.cpp:331] Iteration 60000, Testing net (#0)
I0609 18:30:11.146687 28381 data_layer.cpp:73] Restarting data prefetching from start.
I0609 18:30:11.201294 28372 solver.cpp:398]     Test net output #0: accuracy = 0.6092
I0609 18:30:11.201320 28372 solver.cpp:398]     Test net output #1: loss = 1.35986 (* 1 = 1.35986 loss)

/home/jungmo/apps/caffe/build/tools/caffe test -model /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar10_full_train_test.prototxt -weights /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar_noisetrain_iter_60000.caffemodel -gpu all -iterations 100

I0609 19:04:43.343973 29201 caffe.cpp:318] Loss: 1.35986
I0609 19:04:43.343986 29201 caffe.cpp:330] accuracy = 0.6092
I0609 19:04:43.344013 29201 caffe.cpp:330] loss = 1.35986 (* 1 = 1.35986 loss)

-------------------

——————add eps 1 sa=121 noise to CIFAR and train and test——————

/home/jungmo/apps/caffe/build/examples/cifar10/convert_cifar_data.bin /home/wanli/cifar10_newdata /home/wanli/alexnet_otherdatast/cifar10_s121e1_data lmdb

nohup /home/jungmo/apps/caffe/build/tools/caffe.bin train --solver /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar10_full_solver.prototxt --gpu all >/home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar_s121e1.log &

I0609 19:41:56.410487 29271 solver.cpp:311] Iteration 60000, loss = 0.795319
I0609 19:41:56.410526 29271 solver.cpp:331] Iteration 60000, Testing net (#0)
I0609 19:41:57.748481 29280 data_layer.cpp:73] Restarting data prefetching from start.
I0609 19:41:57.801019 29271 solver.cpp:398]     Test net output #0: accuracy = 0.4431
I0609 19:41:57.801046 29271 solver.cpp:398]     Test net output #1: loss = 2.0495 (* 1 = 2.0495 loss)

/home/jungmo/apps/caffe/build/tools/caffe test -model /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar10_full_train_test.prototxt -weights /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar_noisetrain_iter_60000.caffemodel -gpu all -iterations 100


I0609 20:28:02.517263 31636 caffe.cpp:318] Loss: 2.0495
I0609 20:28:02.517277 31636 caffe.cpp:330] accuracy = 0.4431
I0609 20:28:02.517302 31636 caffe.cpp:330] loss = 2.0495 (* 1 = 2.0495 loss)
———————————

——————————————add eps 0.5 sa=121 noise to CIFAR and train and test——————
之前没有处理 加了noise之后的mean 这次作为尝试 检查是否有accuracy的增加

/home/jungmo/apps/caffe/build/examples/cifar10/convert_cifar_data.bin /home/wanli/cifar10_newdata /home/wanli/alexnet_otherdatast/cifar10_s121e0_5_data lmdb

/home/jungmo/apps/caffe/build/tools/compute_image_mean -backend=lmdb \
/home/wanli/alexnet_otherdatast/cifar10_s121e0_5_data/cifar10_train_lmdb /home/wanli/alexnet_otherdatast/cifar10_s121e0_5_data/mean.binaryproto


nohup /home/jungmo/apps/caffe/build/tools/caffe.bin train --solver /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar10_full_solver.prototxt --gpu all >/home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar_s121e0_5.log &

I0609 21:21:44.553447 31976 solver.cpp:311] Iteration 60000, loss = 1.09565
I0609 21:21:44.553470 31976 solver.cpp:331] Iteration 60000, Testing net (#0)
I0609 21:21:45.889084 31985 data_layer.cpp:73] Restarting data prefetching from start.
I0609 21:21:45.943150 31976 solver.cpp:398]     Test net output #0: accuracy = 0.3675
I0609 21:21:45.943176 31976 solver.cpp:398]     Test net output #1: loss = 2.12235 (* 1 = 2.12235 loss)

/home/jungmo/apps/caffe/build/tools/caffe test -model /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar10_full_train_test.prototxt -weights /home/wanli/alexnet_otherdatast/cifar10_full_caffe/cifar_noisetrain_iter_60000.caffemodel -gpu all -iterations 100

I0612 14:59:45.580353 15212 caffe.cpp:318] Loss: 2.12235
I0612 14:59:45.580371 15212 caffe.cpp:330] accuracy = 0.3675
I0612 14:59:45.580389 15212 caffe.cpp:330] loss = 2.12235 (* 1 = 2.12235 loss)

———


———use the new algorazin adding noise————————————
/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=cifar10_full_train_test.prototxt --weights=cifarfull10_iter_60000.caffemodel --iterations 100 -gpu all

I0605 14:48:49.377022  7858 caffe.cpp:318] Loss: 0.648308
I0605 14:48:49.377035  7858 caffe.cpp:330] accuracy = 0.7841
I0605 14:48:49.377063  7858 caffe.cpp:330] loss = 0.648308 (* 1 = 0.648308 loss)



/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=wanli_cifar10_full_train_test.prototxt --weights=wanli_caffe_cifar_ip1_3.caffemodel --iterations 100 -gpu all

eps 2 lambda 100
I0620 19:37:44.818815 29351 caffe.cpp:318] Loss: 2.50617
I0620 19:37:44.818828 29351 caffe.cpp:330] accuracy = 0.498
I0620 19:37:44.818856 29351 caffe.cpp:330] loss = 2.50617 (* 1 = 2.50617 loss)

eps 20 lambda 1
I0620 19:32:04.321882 29218 caffe.cpp:318] Loss: 2.48277
I0620 19:32:04.321894 29218 caffe.cpp:330] accuracy = 0.4999
I0620 19:32:04.321923 29218 caffe.cpp:330] loss = 2.48277 (* 1 = 2.48277 loss)

eps 20 lambda 0.01
I0620 19:33:55.867460 29283 caffe.cpp:318] Loss: 5.42909
I0620 19:33:55.867473 29283 caffe.cpp:330] accuracy = 0.2505
I0620 19:33:55.867485 29283 caffe.cpp:330] loss = 5.42909 (* 1 = 5.42909 loss)


eps 10 lambda 6
I0620 19:28:52.126260 29095 caffe.cpp:318] Loss: 2.49709
I0620 19:28:52.126273 29095 caffe.cpp:330] accuracy = 0.4979
I0620 19:28:52.126302 29095 caffe.cpp:330] loss = 2.49709 (* 1 = 2.49709 loss)


eps 20 lambda=0.1
I0620 19:18:21.728862 28835 caffe.cpp:318] Loss: 3.10348
I0620 19:18:21.728874 28835 caffe.cpp:330] accuracy = 0.5174
I0620 19:18:21.728884 28835 caffe.cpp:330] loss = 3.10348 (* 1 = 3.10348 loss)


eps 2  lambda=0.1
I0620 19:20:39.098587 28899 caffe.cpp:318] Loss: 7.27391
I0620 19:20:39.098599 28899 caffe.cpp:330] accuracy = 0.219
I0620 19:20:39.098626 28899 caffe.cpp:330] loss = 7.27391 (* 1 = 7.27391 loss)

eps =2 lambda = 1
I0620 19:24:00.419878 28966 caffe.cpp:318] Loss: 2.83118
I0620 19:24:00.419893 28966 caffe.cpp:330] accuracy = 0.4732
I0620 19:24:00.419921 28966 caffe.cpp:330] loss = 2.83118 (* 1 = 2.83118 loss)


nvidia-smi
————— add noise to 10x1024 weight to check ———————
sensity=max(max(raw_weight)) - min(min(raw_weight));
epsilon=2;
单独在 10x1024上加噪音看
ip1_-1.npy

/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=cifar10_full_train_test.prototxt --weights=wanli_caffe_cifar_ip1_-1.caffemodel --iterations 100 -gpu all

sensity=max(max(raw_weight)) - min(min(raw_weight));



epsilon =2
I0625 14:06:59.772109 23215 caffe.cpp:318] Loss: 3.02585
I0625 14:06:59.772122 23215 caffe.cpp:330] accuracy = 0.4269
I0625 14:06:59.772150 23215 caffe.cpp:330] loss = 3.02585 (* 1 = 3.02585 loss)

epsilon = 5;
I0625 14:09:33.405725 23339 caffe.cpp:318] Loss: 0.955109
I0625 14:09:33.405740 23339 caffe.cpp:330] accuracy = 0.7016
I0625 14:09:33.405766 23339 caffe.cpp:330] loss = 0.955109 (* 1 = 0.955109 loss)

epsilon =8
I0625 14:08:21.837482 23277 caffe.cpp:318] Loss: 0.814962
I0625 14:08:21.837496 23277 caffe.cpp:330] accuracy = 0.7387
I0625 14:08:21.837524 23277 caffe.cpp:330] loss = 0.814962 (* 1 = 0.814962 loss)



epsilon=10;

I0623 16:17:25.560273 14479 caffe.cpp:318] Loss: 0.7306
I0623 16:17:25.560302 14479 caffe.cpp:330] accuracy = 0.7627
I0623 16:17:25.560330 14479 caffe.cpp:330] loss = 0.7306 (* 1 = 0.7306 loss)

sensity=max(max(raw_weight)) - min(min(raw_weight));
epsilon=20;

I0623 16:13:06.631662 14401 caffe.cpp:318] Loss: 0.683805
I0623 16:13:06.631675 14401 caffe.cpp:330] accuracy = 0.7728
I0623 16:13:06.631685 14401 caffe.cpp:330] loss = 0.683805 (* 1 = 0.683805 loss)


—— no noise SVD————
K=10;

/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=wanli_cifar10_full_train_test.prototxt --weights=wanli_caffe_cifar_ip1_2_5.caffemodel --iterations 100 -gpu all

I0628 15:32:56.373900  3402 caffe.cpp:318] Loss: 0.702712
I0628 15:32:56.373929  3402 caffe.cpp:330] accuracy = 0.7674
I0628 15:32:56.373951  3402 caffe.cpp:330] loss = 0.702712 (* 1 = 0.702712 loss)


———————noise weight first then SVD——————————
epsilon = 5;

/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=wanli_cifar10_full_train_test.prototxt --weights=wanli_caffe_cifar_ip1_-3_5.caffemodel --iterations 100 -gpu all

I0628 15:21:12.497283  1016 caffe.cpp:318] Loss: 1.17122
I0628 15:21:12.497297  1016 caffe.cpp:330] accuracy = 0.6638
I0628 15:21:12.497326  1016 caffe.cpp:330] loss = 1.17122 (* 1 = 1.17122 loss)


———————noise weight first then KSVD——————————
eps = 5;

/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=wanli_cifar10_full_train_test.prototxt --weights=wanli_caffe_cifar_ip1_-2_5.caffemodel --iterations 100 -gpu all

K = 8; ksvd
I0625 15:01:50.551841 23782 caffe.cpp:318] Loss: 1.43464
I0625 15:01:50.551853 23782 caffe.cpp:330] accuracy = 0.5499
I0625 15:01:50.551880 23782 caffe.cpp:330] loss = 1.43464 (* 1 = 1.43464 loss)

svd
I0625 15:34:10.760053 24082 caffe.cpp:318] Loss: 1.40623
I0625 15:34:10.760066 24082 caffe.cpp:330] accuracy = 0.5589
I0625 15:34:10.760092 24082 caffe.cpp:330] loss = 1.40623 (* 1 = 1.40623 loss)

K = 9;
I0625 14:54:50.768941 23640 caffe.cpp:318] Loss: 1.32882
I0625 14:54:50.768955 23640 caffe.cpp:330] accuracy = 0.5881
I0625 14:54:50.768982 23640 caffe.cpp:330] loss = 1.32882 (* 1 = 1.32882 loss)

K=10;
1e-6
I0625 15:07:03.543296 23876 caffe.cpp:318] Loss: 1.26001
I0625 15:07:03.543310 23876 caffe.cpp:330] accuracy = 0.6143
I0625 15:07:03.543339 23876 caffe.cpp:330] loss = 1.26001 (* 1 = 1.26001 loss)

1e-9
I0625 15:11:44.421167 23956 caffe.cpp:318] Loss: 1.25073
I0625 15:11:44.421197 23956 caffe.cpp:330] accuracy = 0.6231
I0625 15:11:44.421228 23956 caffe.cpp:330] loss = 1.25073 (* 1 = 1.25073 loss)



—— no noise SVD————
K=10;

/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=wanli_cifar10_full_train_test.prototxt --weights=wanli_caffe_cifar_ip1_2_5.caffemodel --iterations 100 -gpu all

I0628 15:32:56.373900  3402 caffe.cpp:318] Loss: 0.702712
I0628 15:32:56.373929  3402 caffe.cpp:330] accuracy = 0.7674
I0628 15:32:56.373951  3402 caffe.cpp:330] loss = 0.702712 (* 1 = 0.702712 loss)


—————— algorithm p q iteration k= 10
/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=cifar10_full_train_test.prototxt --weights=wanli_caffe_cifar_ip1_00.caffemodel --iterations 100 -gpu all

lambda = 0.001
I0704 20:30:31.587730  2055 caffe.cpp:318] Loss: 0.648075
I0704 20:30:31.587743  2055 caffe.cpp:330] accuracy = 0.7841
I0704 20:30:31.587770  2055 caffe.cpp:330] loss = 0.648075 (* 1 = 0.648075 loss)

lambda = 1
I0704 20:45:01.680884  2149 caffe.cpp:318] Loss: 0.81566
I0704 20:45:01.680898  2149 caffe.cpp:330] accuracy = 0.758
I0704 20:45:01.680927  2149 caffe.cpp:330] loss = 0.81566 (* 1 = 0.81566 loss)

lambda = 1, eps = 2, iteration = 1;

I0704 20:56:07.914211  2328 caffe.cpp:318] Loss: 0.854749
I0704 20:56:07.914227  2328 caffe.cpp:330] accuracy = 0.7318
I0704 20:56:07.914240  2328 caffe.cpp:330] loss = 0.854749 (* 1 = 0.854749 loss)

lambda = 1, eps=2, iteration =3;
I0704 21:18:40.203644  2510 caffe.cpp:318] Loss: 1.56126
I0704 21:18:40.203658  2510 caffe.cpp:330] accuracy = 0.5472
I0704 21:18:40.203671  2510 caffe.cpp:330] loss = 1.56126 (* 1 = 1.56126 loss)



k=9. eps=2 with algorithm
/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=wanli_cifar10_full_train_test.prototxt --weights=wanli_caffe_cifar_9k2eps.caffemodel --iterations 100 -gpu all
accuracy=0.52

no noise just make k=9 and test alternative least square
/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=wanli_cifar10_full_train_test.prototxt --weights=wanli_caffe_cifar_k9.caffemodel --iterations 100 -gpu all
acc=0.76

MNist===================================
==—————test on MNIST—————caffe

define mnist_wanli.protxt model file and deploy file: mnist_wanli_solver.protxt


nohup /home/jungmo/apps/caffe/build/tools/caffe.bin train --solver /home/wanli/MNSIT_wanli/mnist_wanli_solver.prototxt --gpu all >/home/wanli/MNSIT_wanli/mnist_test.log &


I0722 15:43:07.903643  4788 solver.cpp:311] Iteration 60000, loss = 0.259404
I0722 15:43:07.903687  4788 solver.cpp:331] Iteration 60000, Testing net (#0)
I0722 15:43:07.989668  4794 data_layer.cpp:73] Restarting data prefetching from start.
I0722 15:43:07.990293  4788 solver.cpp:398]     Test net output #0: accuracy = 0.9607
I0722 15:43:07.990334  4788 solver.cpp:398]     Test net output #1: loss = 0.159888 (* 1 = 0.159888 loss)

I0722 16:01:22.607353  4904 solver.cpp:398]     Test net output #0: accuracy = 0.9658
I0722 16:01:22.607388  4904 solver.cpp:398]     Test net output #1: loss = 0.14561 (* 1 = 0.14561 loss)

nvida-smi
取和tensor一样的设置，lot size = 600， 100 epochs （也就是60000 iteration

——test on MNIST with noise training data———
modified training data (add noise) and using mnist_wanli_noise.prototxt

./create_mnist_noise.sh  # generate lmdv with noise_training data

density = 1 = 255 as pixel


nohup /home/jungmo/apps/caffe/build/tools/caffe.bin train --solver /home/wanli/MNSIT_wanli/mnist_wanli_solver.prototxt --gpu all >/home/wanli/MNSIT_wanli/mnist_test_raw.log &

add eps = 0.5, density = 1;
nohup /home/jungmo/apps/caffe/build/tools/caffe.bin train --solver /home/wanli/MNSIT_wanli/mnist_wanli_solver.prototxt --gpu all >/home/wanli/MNSIT_wanli/mnist_test_data_0.5eps_s1.log &

I0725 20:24:49.031339  5751 data_layer.cpp:73] Restarting data prefetching from start.
I0725 20:24:49.031862  5742 solver.cpp:398]     Test net output #0: accuracy = 0.1286
I0725 20:24:49.031896  5742 solver.cpp:398]     Test net output #1: loss = 2.30599 (* 1 = 2.30599 loss)

add eps = 5, density = 1;
nohup /home/jungmo/apps/caffe/build/tools/caffe.bin train --solver /home/wanli/MNSIT_wanli/mnist_wanli_solver.prototxt --gpu all >/home/wanli/MNSIT_wanli/mnist_test_data_5eps_s1.log &

I0725 19:03:46.531630  5134 solver.cpp:398]     Test net output #0: accuracy = 0.129
I0725 19:03:46.531668  5134 solver.cpp:398]     Test net output #1: loss = 2.31161 (* 1 = 2.31161 loss)


add eps = 10, density = 1;
nohup /home/jungmo/apps/caffe/build/tools/caffe.bin train --solver /home/wanli/MNSIT_wanli/mnist_wanli_solver.prototxt --gpu all >/home/wanli/MNSIT_wanli/mnist_test_data_10eps_s1.log &

Test net output #0: accuracy = 0.129

add eps = 20, density = 1;
nohup /home/jungmo/apps/caffe/build/tools/caffe.bin train --solver /home/wanli/MNSIT_wanli/mnist_wanli_solver.prototxt --gpu all >/home/wanli/MNSIT_wanli/mnist_test_data_20eps_s1.log &

I0725 19:48:35.548415  5490 solver.cpp:398]     Test net output #0: accuracy = 0.1443
I0725 19:48:35.548457  5490 solver.cpp:398]     Test net output #1: loss = 2.29588 (* 1 = 2.29588 loss)

add eps = 50, density = 1;
nohup /home/jungmo/apps/caffe/build/tools/caffe.bin train --solver /home/wanli/MNSIT_wanli/mnist_wanli_solver.prototxt --gpu all >/home/wanli/MNSIT_wanli/mnist_test_data_50eps_s1.log &

I0725 20:02:08.048678  5583 solver.cpp:398]     Test net output #0: accuracy = 0.0831
I0725 20:02:08.048724  5583 solver.cpp:398]     Test net output #1: loss = 2.30783 (* 1 = 2.30783 loss)

/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=/home/wanli/MNSIT_wanli/mnist_wanli.prototxt --weights=/home/wanli/MNSIT_wanli/mnsit_model_results/data_5eps_s1.caffemodel --iterations 100 -gpu all

/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=/home/wanli/MNSIT_wanli/mnist_wanli.prototxt --weights=/home/wanli/MNSIT_wanli/mnsit_model_results/raw_iter_60000.caffemodel --iterations 100 -gpu all

/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=/home/wanli/MNSIT_wanli/mnist_wanli_2layer.prototxt --weights=/home/wanli/MNSIT_wanli/mnist_2layer_pure.caffemodel --iterations 100 -gpu all


—————add noise to weight

python /home/wanli/caffe_local/extract.py --model=/home/wanli/MNSIT_wanli/mnist_wanli.prototxt --weights=/home/wanli/MNSIT_wanli/mnsit_model_results/raw_iter_60000.caffemodel --output=mnist_extract


—————————
eps = 20
matrix error =

0.0442

/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=/home/wanli/MNSIT_wanli/mnist_wanli.prototxt --weights=/home/wanli/MNSIT_wanli/mnist_noise_weight/mnist_weight_20eps_sa.caffemodel --iterations 100 -gpu all

I0725 20:53:36.642285  5906 caffe.cpp:318] Loss: 0.152362
I0725 20:53:36.642302  5906 caffe.cpp:330] accuracy = 0.963
I0725 20:53:36.642330  5906 caffe.cpp:330] loss = 0.152362 (* 1 = 0.152362 loss)

——————
eps = 10
error =

0.0886

/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=/home/wanli/MNSIT_wanli/mnist_wanli.prototxt --weights=/home/wanli/MNSIT_wanli/mnist_noise_weight/mnist_weight_10eps_sa.caffemodel --iterations 100 -gpu all

I0725 20:56:35.818883  5957 caffe.cpp:318] Loss: 0.170044
I0725 20:56:35.818894  5957 caffe.cpp:330] accuracy = 0.9559
I0725 20:56:35.818919  5957 caffe.cpp:330] loss = 0.170044 (* 1 = 0.170044 loss)

——————-
eps = 5
error =

0.1755

/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=/home/wanli/MNSIT_wanli/mnist_wanli.prototxt --weights=/home/wanli/MNSIT_wanli/mnist_noise_weight/mnist_weight_5eps_sa.caffemodel --iterations 100 -gpu all

I0725 20:58:48.742980  6007 caffe.cpp:318] Loss: 0.430706
I0725 20:58:48.742991  6007 caffe.cpp:330] accuracy = 0.854
I0725 20:58:48.743017  6007 caffe.cpp:330] loss = 0.430706 (* 1 = 0.430706 loss)

—————————


———————————算法———————
第一层不动 784x1000 只处理第二层1000x10

/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=/home/wanli/MNSIT_wanli/mnist_wanli.prototxt --weights=/home/wanli/MNSIT_wanli/mnsit_model_results/raw_iter_60000.caffemodel --iterations 100 -gpu all

/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=/home/wanli/MNSIT_wanli/mnist_wanli.prototxt --weights=$ ./convert.py /home/wanli/MNSIT_wanli/mnist_wanli.prototxt --caffemodel /home/wanli/MNSIT_wanli/mnist_extract/mnist_noiseweight_-2.caffemodel --data-output-path=tf_output.npy --iterations 100 -gpu all
85%

/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=/home/wanli/MNSIT_wanli/mnist_wanli_2layer.prototxt --weights=/home/wanli/MNSIT_wanli/mnist_2layer_pure.caffemodel --iterations 100 -gpu all

只用算法分开不加噪音，精确度都是96% （.52 & .28)


用了算法加噪音 /ipQ1_10x1000.npy')/ipP1_10x10.npy')
/home/jungmo/apps/caffe/build/tools/caffe.bin test --model=/home/wanli/MNSIT_wanli/mnist_wanli_2layer.prototxt --weights=/home/wanli/MNSIT_wanli/mnist_2layer_noise1.caffemodel --iterations 100 -gpu all

for q,p
30K,3K
accuracy=0.9597
ip2_1.npy
err=2.0?


25K,2.5K
=0.96
ip2_2.npy
err=2.5

10K,1K
err=3.1
acc=0.95
ip2_3.npy

1k,100
err=8.43
acc=0.18

5k,500
err=3.61
acc=0.834


用attack中的0.5 盒0.09会慢很多但是更清楚

——————————————直接在 tf的tranin后加噪音 并且用算法 然后在attack———
原始的acc=0.9212


5k,500
err=1.60
acc=0.896
w2_tf_5k500.npy 直接用tf生成的w2用算法加噪音，在发回去attack



10k,1k
err=0.9325
acc=0.9108
w2_tf_10k1k.npy 直接用tf生成的w2用算法加噪音，在发回去attack



验证过了，dp_sgd提取的weight没问题，精确度高 （在local的dp_sgd算法里）


———————研究 private sgd———

1.找到正确的weight的保存
—-通过加少量噪音 attack开始

之前的尝试，加噪音然后再三个地方保存weight attack，结构都为噪音图片：可能是位置不对或者attack结果就是如此

