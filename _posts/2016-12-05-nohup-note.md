---
layout: post
title: "nohup note"
date: 2016-12-05
---


nohup

nohup sh custom-script.sh > custom-out.log &

nohup /home/jungmo/apps/caffe/build/tools/caffe.bin train --solver=solver_alexnet_cifar.prototxt >alex_cifar_31_27.log &

nohup /home/jungmo/apps/caffe/build/tools/caffe.bin train --solver=solver.prototxt >alex_para_update_cifar.log &


$ ps aux | grep wanli
wanli  12034  0.0  0.1   4912  1080 pts/2    S    14:10   0:00 sh custom-script.sh

nvidia-smi


nohup /home/jungmo/apps/caffe/build/tools/caffe.bin train -solver /home/wanli/alexnet_otherdatast/solver_alexnet_cifar.prototxt -snapshot /home/wanli/alexnet_otherdatast/alex_cifar_iter_150000.solverstate >alex_cifar_45k_32.log &


kill -9 ID
