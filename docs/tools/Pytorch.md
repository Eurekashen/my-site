!!! abstract
	这里记录一些我使用Pytorch遇到的一些问题，和一些功能的使用方法，记下来方便之后的查找。

## Pytorch上使用多卡训练

### `torch.nn.DataParallel`

这时最简单的使用单机多卡训练的方式，对代码不会有太多的修改，采用单个进程多个线程的模型。但是这样做的性能通常不是最好的，因为在每一次模型forward的时候都需要重复模型。而且单进程多线程的并行度也会收到GIL争用的影响。

这种方法使用起来非常的简单，只需要添加一点点代码：

1. 获取现在device，和原来一样

   ```python
   device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
   ```

2. 把模型放到GPU上：

   ```python
   model = Model(input_size, output_size)
   if torch.cuda.device_count() > 1:
     model = nn.DataParallel(model)
   model.to(device)
   ```

   像上面这样使用会默认使用到全部GPU，如果想要指定使用GPU的数量和id，则需要：

   ```python
   model = YourModel()
   device_ids = [0, 1]  # 0号和1号GPU
   model = nn.DataParallel(model, device_ids=device_ids)
   ```

3. 像其他的损失函数、优化器以及训练输入的tensor也一样需要放到device上面。

==需要注意的是，如果在一张GPU都跑不满的状况下强行使用多张GPU由于通讯开销等因素反而会更慢。==

### `torch.nn.parallel.DistributedDataParallel`

在PyTorch中，使用`torch.nn.parallel.DistributedDataParallel`（DDP）来进行单机多GPU训练时，需要进行一些代码的修改。DDP用于分布式训练，但也可以用于单机多GPU的训练，以提高训练速度。





!!! reference

​	https://zhuanlan.zhihu.com/p/76638962

https://zhuanlan.zhihu.com/p/86441879
