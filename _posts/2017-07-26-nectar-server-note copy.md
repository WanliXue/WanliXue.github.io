---
layout: post
title: "dl with dp example"
date: 2017-07-17
---

感觉 Moments模式 是可以设定 sigma的

if FLAGS.accountant_type == "Amortized":
priv_accountant = accountant.AmortizedAccountant(NUM_TRAINING_IMAGES)
sigma = None
pca_sigma = None
with_privacy = FLAGS.eps > 0
elif FLAGS.accountant_type == "Moments":
priv_accountant = accountant.GaussianMomentsAccountant(
NUM_TRAINING_IMAGES)
sigma = FLAGS.sigma
pca_sigma = FLAGS.pca_sigma
with_privacy = FLAGS.sigma > 0


对于MNist是可以的，但是对于CIFAR失败，3通道，读取dataset就不行，询问vic？


in the tf .py file setting line 268:
with tf.Graph().as_default(), tf.Session() as sess, tf.device('/cpu:0'):
to
with tf.Graph().as_default(), tf.Session() as sess, tf.device('/gpu:0','/gpu:1'):

get the dataset
./convert_minst.sh

# Build the codes.
bazel build -c opt differential_privacy/...

https://stackoverflow.com/questions/42623978/print-saved-weights-of-particular-layer-tensorflow

python ~/Documents/github_code/tensorflow/tensorflow/python/tools/inspect_checkpoint.py --file_name=/Users/wanli/PycharmProjects/test_tensorflow/tf_save/ckpt
hidden0_weight (DT_FLOAT) [784,1000]
logits_weight (DT_FLOAT) [1000,10]

python ~/Documents/github_code/tensorflow/tensorflow/python/tools/inspect_checkpoint.py --file_name=/Users/wanli/Dropbox/Deep\ learning/tmp_server_check/saved_file/sgd_result/transfer_files_between_server/servers3_eps05_nohiden_0pca/ckpt

python ~/Documents/github_code/tensorflow/tensorflow/python/tools/inspect_checkpoint.py --file_name=/Users/wanli/Dropbox/Deep\ learning/tmp_server_check/saved_file/sgd_result/transfer_files_between_server/servers2_eps2_nohiden_0pca/ckpt

python ~/Documents/github_code/tensorflow/tensorflow/python/tools/inspect_checkpoint.py --file_name=/Users/wanli/Dropbox/Deep\ learning/tmp_server_check/saved_file/sgd_result/transfer_files_between_server/servers1_eps8_nohiden_0pca/ckpt


Moment

--terminate_based_on_privacy=true

server 1 and 2 almost same environment but different path


Server 1 eps=8
nohup bazel-bin/differential_privacy/dp_sgd/dp_mnist/dp_mnist \
--training_data_path=/mnt/wanli/MNIST/mnist_train.tfrecord \
--eval_data_path=/mnt/wanli/MNIST/mnist_test.tfrecord \
--save_path=/mnt/wanli/mnist_result \
--accountant_type=Moments \
--projection_dimensions=0 \
--target_eps=8 \
--sigma=2 \
--terminate_based_on_privacy=true \
--num_hidden_layers=0 > /mnt/wanli/mnist_result/sgd_eps8_mom_nohidlayer.out &

Server 2 eps=2

nohup bazel-bin/differential_privacy/dp_sgd/dp_mnist/dp_mnist \
--training_data_path=/mnt/wanli/tf_runing/MNIST_data/mnist_train.tfrecord \
--eval_data_path=/mnt/wanli/tf_runing/MNIST_data/mnist_test.tfrecord \
--save_path=/mnt/wanli/mnist_result \
--accountant_type=Moments \
--target_eps=2 \
--sigma=4 \
--num_hidden_layers=0 \
--projection_dimensions=0 \
--terminate_based_on_privacy=true > /mnt/wanli/mnist_result/sgd_eps2_mom_nohidlayer_0pca.out &

server 3  eps=0.5
nohup bazel-bin/differential_privacy/dp_sgd/dp_mnist/dp_mnist \
--training_data_path=/mnt/wanli/mnist_data/mnist_train.tfrecord \
--eval_data_path=/mnt/wanli/mnist_data/mnist_test.tfrecord \
--save_path=/mnt/wanli/mnist_result \
--accountant_type=Moments \
--target_eps=0.5 \
--sigma=8 \
--num_hidden_layers=0 \
--projection_dimensions=0 \
--terminate_based_on_privacy=true > /mnt/wanli/mnist_result/sgd_eps05_mom_nohidlayer_0pca.out &

-------------
for server 3
nohup bazel-bin/differential_privacy/dp_sgd/dp_mnist/dp_mnist \
--training_data_path=/mnt/wanli/mnist_data/mnist_train.tfrecord \
--eval_data_path=/mnt/wanli/mnist_data/mnist_test.tfrecord \
--save_path=/mnt/wanli/mnist_result \
--accountant_type=Moments \
--target_eps=8 \
--sigma=2 \
--pca_sigma=4 \
--terminate_based_on_privacy=true > /mnt/wanli/mnist_result/sgd_eps8_pca_mom.out &

for server 2
nohup bazel-bin/differential_privacy/dp_sgd/dp_mnist/dp_mnist \
--training_data_path=/mnt/wanli/tf_runing/MNIST_data/mnist_train.tfrecord \
--eval_data_path=/mnt/wanli/tf_runing/MNIST_data/mnist_test.tfrecord \
--save_path=/mnt/wanli/mnist_result \
--accountant_type=Moments \
--target_eps=2 \
--sigma=4 \
--pca_sigma=7 \
--terminate_based_on_privacy=true > /mnt/wanli/mnist_result/sgd_eps2_pca_mom.out &

for 1
nohup bazel-bin/differential_privacy/dp_sgd/dp_mnist/dp_mnist \
--training_data_path=/mnt/wanli/MNIST/mnist_train.tfrecord \
--eval_data_path=/mnt/wanli/MNIST/mnist_test.tfrecord \
--save_path=/mnt/wanli/mnist_result \
--accountant_type=Moments \
--target_eps=0.5 \
--sigma=8 \
--pca_sigma=16  > /mnt/wanli/mnist_result/sgd_eps05_pca_mom.out &

nohup bazel-bin/differential_privacy/dp_sgd/dp_mnist/dp_mnist \
--training_data_path=/mnt/wanli/MNIST/mnist_train.tfrecord \
--eval_data_path=/mnt/wanli/MNIST/mnist_test.tfrecord \
--save_path=/mnt/wanli/mnist_result \
--num_training_steps=1000 \
--accountant_type=Amortized \
--projection_dimensions=0 \
--target_eps=10 \
--terminate_based_on_privacy=true \
--num_hidden_layers=0 > /mnt/wanli/mnist_result/sgd_eps10_amor.out &

nohup bazel-bin/differential_privacy/dp_sgd/dp_mnist/dp_mnist \
--training_data_path=/mnt/wanli/MNIST/mnist_train.tfrecord \
--eval_data_path=/mnt/wanli/MNIST/mnist_test.tfrecord \
--save_path=/mnt/wanli/mnist_result \
--num_training_steps=1000 \
--accountant_type=Moments \
--projection_dimensions=0 \
--target_eps=0.5 \
--terminate_based_on_privacy=true \
--num_hidden_layers=0 > /mnt/wanli/mnist_result/sgd_eps05_mon.out &


nohup bazel-bin/differential_privacy/dp_sgd/dp_mnist/dp_mnist \
--training_data_path=/mnt/wanli/MNIST/mnist_train.tfrecord \
--eval_data_path=/mnt/wanli/MNIST/mnist_test.tfrecord \
--save_path=/mnt/wanli/mnist_result \
--num_training_steps=5000 \
--accountant_type=Amortized \
--projection_dimensions=0 \
--target_eps=5 \
--num_hidden_layers=1 > /mnt/wanli/mnist_result/sgd_eps5_amor.out &

# Run the mnist differntial privacy training codes.
nohup bazel-bin/differential_privacy/dp_sgd/dp_mnist/dp_mnist \
--training_data_path=/mnt/wanli/MNIST/mnist_train.tfrecord \
--eval_data_path=/mnt/wanli/MNIST/mnist_test.tfrecord \
--save_path=/mnt/wanli/mnist_result \
--num_training_steps=5000 \
--accountant_type=Amortized \
--eps=5.0 \
--projection_dimensions=0 \
--num_hidden_layers=1 > /mnt/wanli/mnist_result/sgd_eps5.out &


# Run the mnist differntial privacy training codes.
bazel-bin/differential_privacy/dp_sgd/dp_mnist/dp_mnist \
--training_data_path=data/mnist_train.tfrecord \
--eval_data_path=data/mnist_test.tfrecord \
--save_path=/mnt/wanli/tmp/mnist_dir \
--num_training_steps=5000 \
--accountant_type=Moments \
--eps=5.0 \
--projection_dimensions=0

bazel-bin/differential_privacy/dp_sgd/dp_mnist/dp_mnist \
--training_data_path=data/mnist_train.tfrecord \
--eval_data_path=data/mnist_test.tfrecord \
--save_path=/tmp/mnist_dir

输出结果在tmp/mnist_dir， 默认为／tmp/mnist_dir

./convert_cifar10.sh

#delete pca and try make conv layer to 3

# Build the codes.
bazel build -c opt differential_privacy/...

# Run the mnist differntial privacy training codes.
bazel-bin/differential_privacy/dp_sgd/dp_mnist/dp_mnist \
--training_data_path=/Users/wanli/Downloads/CIFAR/cifar10_train.tfrecord \
--eval_data_path=/Users/wanli/Downloads/CIFAR/cifar10_test.tfrecord \
--save_path=tmp/cifar_dir \
--num_conv_layers=2 \
--projection_dimensions=0 \
--num_training_steps=500 \
--num_hidden_layers=2
