深度求索发布 DeepSeek Coder

## 简单体验

简单测试了一下，没有深度的使用。

* 上手使用之后第一感觉就是“快”，不管是响应的速度还是代码生成的速度都比ChatGPT、Copilot快非常多；上下文能力也还不错，我在中间穿插问了一些其他的问题之后再让他续写代码也可以比较好的生成。我让模型写一个VAE的训练代码并且一步步的指导模型增加代码的功能，其中穿插着问了一些关于VAE的知识，他回答也还不错。

<center>
<a href="https://www.imagehub.cc/image/10L61I"><img src="https://s1.imagehub.cc/images/2023/11/04/77d580f98838581c15b233d2bb8b51f9.png" alt="77d580f98838581c15b233d2bb8b51f9.png" border="0" /></a>
</center>

* 通用能力也还不错，我穿插着问关于KL散度和VAE的知识可以比较好的解释，这是我认为这种代码大模型和LLM相较于Copilot最大的优势——遇到不懂得地方可以直接问，毕竟只拿到代码一点用没有。但是上下文能力还是有一定的欠缺，我在其中穿插着问了大约4、5个和代码有关的问题之后再想让模型修改添加最开始的代码模型就不知道我说的“最开始的”
指的是哪里，模型就开始写新的代码而不是按照我的要求修改原来的代码。 ==不过也有可能是我使用的prompt有问题。==
<center>
  <a href="https://www.imagehub.cc/image/10Lnxh"><img src="https://s1.imagehub.cc/images/2023/11/04/b6f07e0009a6e7411870adc5c5e73dc5.png" alt="b6f07e0009a6e7411870adc5c5e73dc5.png" border="0" />
  </a>
</center>
  

* 最大的优点——开源，可以直接从huggingface上下载部署

根据官方的数据

??? quote "Benchmark"
    在国际权威数据集 HumanEval 编程多语言测试上，DeepSeek Coder 在各个语言上的表现都领先已有的开源模型。与之前最好的开源大模型 CodeLlama 相比，DeepSeek Coder 在代码生成任务上（使用标准数据集 HumanEval、MBPP 和 DS-1000 进行评测）分别领先 **9.3%**、**10.8%** 和 **5.9%**。其中 DeepSeek Coder 的 70 亿参数版本在代码能力上达到了 CodeLlama 的 340 亿参数水平。经过指令调优后的 DeepSeek Coder 模型更是全面超越了 GPT3.5-Turbo。
    <center>
    <a href="https://www.imagehub.cc/image/10LWnv"><img src="https://s1.imagehub.cc/images/2023/11/04/43c9aedc8f50af22a1b21f202e7f33e8.png" alt="43c9aedc8f50af22a1b21f202e7f33e8.png" border="0" /></a>
    </center>

但是现在内测模型还有一些问题，比如说只能够开一个对话窗口等，但是这些问题解决起来也会相对的容易，



## 训练过程

根据官方的说法

??? quote "训练过程"
    * 数据处理

        * 步骤1：从 GitHub 收集代码数据，并利用过滤规则高效地筛选数据。
        
        * 步骤2：解析同一项目中代码文件之间的依赖关系，根据它们的依赖关系重新排列文件位置。

        * 步骤3：组织依赖文件，并使用项目级别的 minhash 算法进行去重。

        * 步骤4：进一步过滤掉低质量的代码，例如语法错误或可读性差的代码。

    * 模型训练

        * 步骤1：使用 4K 的窗口大小在 1.8 万亿单词上进行模型的预训练。

        * 步骤2：使用 16K 的窗口在 2 千亿单词进一步进行预训练，从而得到基础版本模型（DeepSeek-Coder-Base）。

        * 步骤3：使用 20 亿单词的指令数据进行微调，得到经过指令调优的模型（DeepSeek-Coder-Instruct）。