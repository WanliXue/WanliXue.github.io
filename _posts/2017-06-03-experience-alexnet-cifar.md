---
layout: post
title: "experience alexnet cifar"
date: 2017-06-03
---


first time, give a error run (by mistake) cropsize 31, output class 27 (supposed to be 10 for CIFAR-10), and iteration 450K get almost 85% accuracy.

So, i notice the output class is wrong, then I turn the setting to crop size 32 and output 10 and think 150K is enough,
however after 150K iteraiton ,the accuracy is only 0.1.

After discussion with Jungmo, looks like the crop size matters, output class is not matter and 150K is also enought to convergence (update the accuracy), the crop size 32 make the window size not an interger

(input size - kernel size)/stride + 1
(31-3)/2+1

After this, make a new train, with crop size 31, output class 10 (CIFAR-10) and iteration 450K.

failed, still 0.1 accuracy

next will try if learn rate matters

didnt try;


directly try running cifar10 on alexnet with only cropsize 32


------old one -----
Alexnet CIFAR-10

traing Alexnet with CIFAR-10 for 450,000 times iteration stored in alex_cifar.log.

input size 32*32 crop to 31 (this not good dont do anymore will cause dimesnion crash
https://stackoverflow.com/questions/37300317/caffe-error-cannot-copy-param-0-weights-from-layer-shape-mismatch)


finally becaues got 27 class outpu, retrain the mdoel with 32*32 and 10 output. 150,000 times iter.

fc6 seems get modfied  1024
fc7 1024
fc8 1024
------------------------


