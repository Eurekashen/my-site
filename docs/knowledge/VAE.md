!!! abstract
    VAE（Variational Autoencoder）是一种生成模型，用于学习概率分布中的数据表示。它是一种自编码器（Autoencoder）的变种，具有编码器（Encoder）和解码器（Decoder）两个主要组件。VAE的主要目标是学习数据的潜在空间（Latent Space）表示，并能够生成新的数据样本，同时还可以进行数据重构和插值。

## 模型架构简介

1. **编码器（Encoder）**：
   - 编码器将输入数据（通常是高维数据，如图像或文本）映射到潜在空间中的低维表示。
   - 它产生两个向量，一个表示潜在空间中的均值向量（$μ$），另一个表示方差向量（$σ^2$）。
   - 这些向量用于定义潜在空间中的高斯分布。
2. **潜在空间采样**：
   - 从编码器输出的均值和方差向量中采样潜在空间中的点。
   - 通常使用重参数化技巧，使梯度能够通过采样传回编码器，以便训练期间可以优化模型。
3. **解码器（Decoder）**：
   - 解码器将潜在空间中的样本映射回原始数据空间，尝试生成与输入数据相似的输出。
   - 这一步可以被视为从潜在空间中采样点并将其转化为数据样本。
4. **重构误差**：
   - VAE的训练目标是最小化重构误差，即输入数据和解码器输出之间的差异。
   - 还包括一个正则化项，它通过最小化潜在空间的均值和方差与标准正态分布之间的差异来使潜在表示更加连续和规则。
5. **生成新样本**：
   - 一旦训练完成，VAE可以从潜在空间中随机采样以生成新的数据样本。

==VAE的一个关键优势是它能够生成具有一定连续性的潜在空间表示，这使得它在生成新数据样本时可以进行插值。此外，VAE也常用于图像生成、图像去噪、数据降维和半监督学习等应用中。==

## 数学推导

### 背景知识和假设

给定真实的数据样本${X1,…,Xn}$，其整体用$X$来描述，我们想根据${X1,…,Xn}$得到X的分布$p(X)$，如果能得到的话，那我直接根据$p(X)$来采样，就可以得到所有可能的$X$。但是目前没有这样的理想的生成模型能够直接推出$X$的分布。

使用下面的推导公式：

$$
p(X)=\sum_{Z}p(X|Z)p(Z)
$$
我们假设后验分布$p(Z|X_k)$是正态分布，其中$X_k$代表真实的样本，并且每个$X_k$的分布是独立的，有多少个$X$样本就有多少个这样的分布。

我们知道正态分布有两个参数：均值和方差——$\mu,\sigma^2$.我们的encoder就是要根据给定的$X_k$训练拟合出对应的$\mu_k,\sigma_k^2$。而decoder是一个生成器$g(Z_k)$,将$X_k$对应的正态分布中采样出的$Z_k$还原回$\hat{X_k}$，通过最小化$distance(X_k,\hat{X_k})$就能够同时训练encoder和decoder了。

### 分布标准化

!!! quote
    ​最小化$distance(X_k,\hat{X_k})$，但是这个重构过程受到噪声的影响，因为$Z_k$是通过重新采样过的，不是直接由encoder算出来的。显然噪声会增加重构的难度，不过好在这个噪声强度（也就是方差）通过一个神经网络（encoder）算出来的，所以最终模型为了重构得更好，肯定会想尽办法让方差为0。而方差为0的话，也就没有随机性了，所以不管怎么采样其实都只是得到确定的结果（也就是均值），只拟合一个当然比拟合多个要容易，而均值是通过另外一个神经网络算出来的。

==最后会导致模型会慢慢退化成普通的AutoEncoder，噪声不再起作用。==

所以VAE让所有$p(Z|X)$都向标准正态分布对齐，这样就可以防止噪声为0，保证模型还是一个概率生成模型。

### KL散度

KL散度在其他文章里面已经介绍过了，这里就不再赘述。

前面说到我们想要让拟合出的分布向标准正态分布靠拢，这时就需要用到之前说的衡量两个概率分布之间的差距的指标——KL散度：

$$
KL(P||Q) = Σ [P(x) * log(P(x) / Q(x))]
$$

将KL散度作为一个额外的loss，然后通过梯度下降优化就可以让拟合出的分布靠近标准正态分布了。

### 重参数化

要从$p(Z|Xk)$中采样一个$Z_k$出来，尽管我们知道了$p(Z|Xk)$是正态分布，但是均值方差都是靠encoder算出来的，我们要靠这个过程反过来优化均值方差的模型，但是“采样”这个操作是不可导的，而采样的结果是可导的。我们利用

$$
\begin{aligned}
\frac{1}{\sqrt{2\pi \sigma^2}}exp(-\frac{(z-\mu)^2}{2\sigma^2})dz \\
=\frac{1}{\sqrt{2\pi}}exp[-\frac{1}{2}(\frac{z-\mu}{\sigma})^2]d(\frac{z-\mu}{\sigma})
\end{aligned}
$$

这说明$\frac{z−μ}{σ}=ε$是服从均值为0、方差为1的标准正态分布的。所以从$N(μ,σ2)$中采样一个$Z$，相当于从$N(0,1)$中采样一个$ε$，然后让$Z=μ+ε×σ$。

## 简单的CVAE实现

普通的VAE并没有condition的指导，也就是说训练完成之后没有办法控制最后的生成结果。比如说在MNIST数据集上训练完成之后，我如果想要生成一张“2”的图片，那么原始的VAE是做不到的。

??? "VAE的Pytorch实现"

    ```python
    import torch
    from torch import nn
    from torchvision import datasets, transforms
    from torch.utils.data import DataLoader
    from torch.utils.tensorboard import SummaryWriter
    from tqdm import tqdm
    import torchvision

    # 定义VAE的结构
    class VAE(nn.Module):
        def __init__(self):
            super(VAE, self).__init__()

            self.fc1 = nn.Linear(784, 400)
            self.fc21 = nn.Linear(400, 20)
            self.fc22 = nn.Linear(400, 20)
            self.fc3 = nn.Linear(20, 400)
            self.fc4 = nn.Linear(400, 784)

        def encode(self, x):
            h1 = torch.relu(self.fc1(x))
            return self.fc21(h1), self.fc22(h1)

        def reparameterize(self, mu, logvar):
            std = torch.exp(0.5 * logvar)
            eps = torch.randn_like(std)
            return mu + eps * std

        def decode(self, z):
            h3 = torch.relu(self.fc3(z))
            return torch.sigmoid(self.fc4(h3))

        def forward(self, x):
            mu, logvar = self.encode(x.view(-1, 784))
            z = self.reparameterize(mu, logvar)
            return self.decode(z), mu, logvar

    # 加载MNIST数据集
    transform = transforms.Compose([transforms.ToTensor()])
    mnist = datasets.MNIST("./data", download=True, transform=transform)
    dataloader = DataLoader(mnist, batch_size=128, shuffle=True)

    # 初始化VAE和优化器
    device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
    model = VAE().to(device)
    optimizer = torch.optim.Adam(model.parameters(), lr=1e-3)

    # 初始化tensorboard
    writer = SummaryWriter()


    ​    
    # 训练VAE
    def train(epoch):
        model.train()
        train_loss = 0
        for batch_idx, (data, _) in enumerate(tqdm(dataloader)):
            data = data.to(device)
            optimizer.zero_grad()
            recon_batch, mu, logvar = model(data)
            loss = loss_function(recon_batch, data, mu, logvar)
            loss.backward()
            torch.nn.utils.clip_grad_norm_(model.parameters(), max_norm=1)  # 梯度裁剪
            train_loss += loss.item()
            optimizer.step()
    
            # 记录损失到tensorboard
            writer.add_scalar(
                "Loss/train", loss.item(), epoch * len(dataloader) + batch_idx
            )
    
        print(
            "====> Epoch: {} Average loss: {:.4f}".format(
                epoch, train_loss / len(dataloader.dataset)
            )
        )


    # 定义损失函数
    def loss_function(recon_x, x, mu, logvar):
        BCE = nn.functional.binary_cross_entropy(recon_x, x.view(-1, 784), reduction="sum")
        KLD = -0.5 * torch.sum(1 + logvar - mu.pow(2) - logvar.exp())
        return BCE + KLD

    # 训练模型
    # for epoch in range(1, 51):
    #     train(epoch)

    # 保存模型
    # torch.save(model.state_dict(), "model.pth")

    # 加载模型
    # model = VAE().to(device)
    model.load_state_dict(torch.load("model.pth"))

    # 可视化生成结果
    with torch.no_grad():
        z = torch.randn(64, 20).to(device)
        sample = model.decode(z).view(64, 1, 28, 28)
        # writer.add_images("Images/sample", sample, 0)
        torchvision.utils.save_image(sample, f"image.png")

    writer.close()
    ```

!!! summary
    这里只是我学习了解VAE的一点笔记，更多细节建议去看苏神的原文。

!!! quote
	苏剑林. (Mar. 18, 2018). 《变分自编码器（一）：原来是这么一回事 》[Blog post]. Retrieved from https://kexue.fm/archives/5253