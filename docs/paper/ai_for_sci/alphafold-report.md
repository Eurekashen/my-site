!!! warning
    现在AlphaFold-latest只是放出早期的一个技术报告，对于模型的实现是缄口不提。而且这篇报告写的也比较简略晦涩，我没有很好的理解其中的一些内容，在这里也只是简单的介绍它的一些实验结果和突破。如果有什么问题还请指出。新一代的AlphaFold传递出的一个关键信息就是它变得更加的“通用”，除了像前代一样能够预测蛋白质的三级结构，还在蛋白质四级结构、配体设计等任务上超过了之前的SoTA方法。

## Some Related Knowledge

### 不同层次的蛋白质结构

- 蛋白质一级结构：组成蛋白质多肽链的线性氨基酸序列，也就是说蛋白质的一级结构就是肽键。
- 蛋白质二级结构：依靠不同氨基酸之间的C=O和N-H基团间的氢键形成的稳定结构，主要为α螺旋和β折叠。
- 蛋白质三级结构：通过多个二级结构元素在三维空间的排列所形成的一个蛋白质分子的三维结构。
- 蛋白质四级结构：用于描述由不同多肽链（亚基）间相互作用形成具有功能的蛋白质复合物分子。

??? note "构象变化"
    蛋白质可以在多个类似结构上转换，除此之外还可以在三级机构和四级结构上转换，称之为构象变化。

### post-translational modifications

PTMs：翻译后修饰，指蛋白质在翻译后的化学修饰。对于大部分的蛋白质来说，这是蛋白质生物合成的较后步骤。在这个过程中蛋白质可以被切成多条链在链间形成S-S二硫键，或者出现乙酰化、磷酸化等修饰。

??? note "蛋白质的合成过程"
    在细胞核内先从DNA==转录==为mRNA——mRNA在细胞质基质中依靠tRNA以及核糖体==翻译==成肽链——肽链经过加工修饰之后成为最终的蛋白质。

??? note "covalent modifications"
    共价修饰（Covalent modification）是酶中的氨基酸残基因发生共价修饰而使酶发生活性变化的过程，这是酶的一种活性调节机制，为可逆的。

### Docking method

这是之前的方法这种方法需要严格的参考蛋白质结构和配体的建议结合位置。

在蛋白质配体设计中，"docking method" 是一种计算生物学方法，用于模拟和分析蛋白质与潜在小分子药物（配体）之间的相互作用。它的主要目的是预测蛋白质与配体之间的结合方式，以便确定哪些分子具有潜在的药用价值。

Docking方法的基本原理包括以下步骤：

1. 生成蛋白质和配体的三维结构：首先，需要获得蛋白质和配体的三维结构数据，通常是通过实验方法如X射线晶体学或核磁共振来获得，或者使用计算方法来预测。

2. 蛋白质与配体的构象搜索：在这一步中，计算程序会尝试不同的构象（结合方式），以找到能够形成稳定结合的最佳结构。这通常涉及到配体在蛋白质表面的不同位置和姿态。

3. 能量评分：每个生成的结合构象都会根据相互作用的能量进行评分，包括范德华力、静电相互作用、氢键等等。这些能量项用于确定哪种结合方式最稳定。

4. 结果分析：最终，计算程序会输出一个或多个最有可能的结合方式，通常伴随着相应的结合自由能值。这些结果可以用于预测配体的结合亲和性，指导药物设计，或者进行虚拟筛选以发现潜在的药物候选化合物。

Docking方法在药物发现和生物分子相互作用研究中发挥了关键作用，帮助科学家识别潜在的药物分子，优化已有的药物，以及理解蛋白质与小分子配体之间的相互作用机制。不同的docking程序和算法存在，它们在准确性和速度上有所差异，因此在具体应用中需要谨慎选择适合的方法。

### Model I/O

报告中仅仅用了一小段话来介绍新的模型的输入和输出：

??? quote "Model Inputs and Outputs"
    AlphaFold-latest takes as input a description of the biological assembly, with sequences for polymers and SMILES for ligands, and optionally the sequence location of covalently bonded ligands, and outputs a prediction for the 3D position of each heavy atom. Water and hydrogens are excluded. All experimental structures used for training the model were from PDB with release dates up to 2021-09-30. Templates were filtered to only those released prior to 2021-09-30. 

    Inputs are “tokenized” to get model inputs, with one token per standard polymer residue and one token per heavy atom for ligands and nonstandard polymer residues. The number of tokens is the primary driver of compute time and limits of prediction sizes on different hardware. We evaluate system performance on complexes up to 5,120 tokens for computational ease, but the system is capable of running larger complexes on accelerators with large amounts of memory. 

    Each output structure comes with per-atom, per-token-pair, and aggregated structure-level confidence measures. In addition, each entity within the structure and each interface between entities within the structure has an associated confidence measure.

新的AlphaFold的训练输入和AlphaFold的差别非常的大，在这里先回顾一下AlphaFold2的Pipeline

!!! info Review of AlphaFold2
    <a href="https://www.imagehub.cc/image/139kvT"><img src="https://s1.imagehub.cc/images/2023/11/07/01951c7aadfdef84be39dff0c91af00a.png" alt="01951c7aadfdef84be39dff0c91af00a.png" border="0" /></a>

在这里所谓的生物组装描述指的可能是配体结合时的酸碱度、特定的化学物质等外界条件、配体的SMILES表示、聚合物的序列并且可以选择性的指定配体之间结合的共价键。最后模型的输出时每一个重原子的3D坐标位置。

输入的信息会被打成一个个的token，在报告中给出了tokenize的措施：每一个标准聚合物的残基是一个token、每一个配体的重原子是一个token、每一个非标准聚合物的残基是一个token。

!!! note 知识补充
    * heacy atom:除了氢原子之外所有的原子
    * SMILES:简化分子线性输入规范，是使用ASCII字符串描述分子结构的标准

最后的输出的每个重原子、token-pair和聚合结构都有置信度的衡量。

现在模型的细节没有公布，不知道是完全重新设计的模型还是在原来的AlphaFold2上面加了一个旁路来添加上面所说的更适用于配体设计等任务的输入。

## Results

### Ligands

<a href="https://www.imagehub.cc/image/139ZYZ"><img src="https://s1.imagehub.cc/images/2023/11/07/eda32ea51e325e565dcef619418abbf8.png" alt="eda32ea51e325e565dcef619418abbf8.png" border="0" /></a>


该模型能够泛化到各种与治疗相关的蛋白质和模态。它实现了这一点，而无需使用真实蛋白质坐标或任何口袋信息——允许预测之前尚未进行结构表征的全新蛋白质。（也就是说现在可以生成一个蛋白质并且直接生成对应的配体，这里和RFDiffusion的不同还是很大的，讲的时候可以顺便讲一下RFDiffusion的实现）。该节提供的示例包括分子粘合剂和共价抑制剂，变构位点，整合膜蛋白，大环化合物以及具有独特构象和先前未表征的结合位点的蛋白质。示例的选择考虑了与训练集的序列相似性、与训练集的配体相似性或蛋白质-配体对的新颖性。总体而言，该模型展示了在结构上表征具有挑战性的药物靶标的潜力，并在治疗设计领域显示出了前景。

==**对所有原子的位置进行联合建模，使其能够代表蛋白质和核酸在与其他分子相互作用时的全部固有灵活性——这是使用对接方法不可能实现的。**==（我的理解是可以不仅仅可以像rfdiffusion那样只建模hotpot几个接触位点的活性）

### Proteins

该模型能够泛化到各种与治疗相关的蛋白质和模态。它实现了这一点，而无需使用真实蛋白质坐标或任何口袋信息。该节提供的示例包括分子粘合剂和共价抑制剂，变构位点，整合膜蛋白，大环化合物以及具有独特构象和先前未表征的结合位点的蛋白质。示例的选择考虑了与训练集的序列相似性、与训练集的配体相似性或蛋白质-配体对的新颖性。总体而言，该模型展示了在结构上表征具有挑战性的药物靶标的潜力，并在治疗设计领域显示出了前景。

<a href="https://www.imagehub.cc/image/13TbyU"><img src="https://s1.imagehub.cc/images/2023/11/07/0d177490bcf6e45c73f64442fb4a8c12.png" alt="0d177490bcf6e45c73f64442fb4a8c12.png" border="0" />
</a>

### Nucleic Acids

核酸蛋白质的相互作用——典型的就是染色体、核糖体，原来的SoTA模型是RoseTTAFoldNA。
AlphaFold-latest在预测核酸结构方面的性能。该模型在蛋白质-RNA和蛋白质-双链DNA界面结构预测方面达到了最新颖的准确性。它还在CASP 15 RNA类别中预测RNA结构方面表现出高准确性。本节强调了对最近解决的结构，包括RNA聚合酶、冠状病毒刺突蛋白和CRP/FNR家族转录调节蛋白等的特定高准确性预测。这些结果表明AlphaFold有潜力准确预测核酸的结构以及它们与蛋白质的相互作用。

<a href="https://www.imagehub.cc/image/13T3Tb"><img src="https://s1.imagehub.cc/images/2023/11/07/7e2135015a0bdb612d8dee495eb72f2e.png" alt="7e2135015a0bdb612d8dee495eb72f2e.png" border="0" /></a>

报告中有提到建设蛋白质和核酸的结合界面时残基特异性的，也就是序列特异性的。

### Convalent Modifications

模型在预测配体-蛋白质相互作用方面取得了高准确性，表现优于AutoDock Vina等传统系统。它还表现出在预测蛋白质的糖基化和修饰位点，以及核酸中的修饰核苷方面的准确性。本节总结了模型在这些共价修饰类别中的能力和性能。总的来说，AlphaFold-latest在准确预测生物分子结构中各种类型的共价修饰方面表现出了令人鼓舞的结果。

??? quote "在输入中指定共价修饰"
    Covalent modifications are specified in the input to AlphaFold in the same way as they are
    represented in the PDB, i.e. they can either be defined as residues with non-standard CCD codes (e.g.“SEP” for phosphoserine) or by additional entries in the bonds table, e.g. a covalent bond between a ligand and a residue.

在输入AlphaFold时，共价修饰的表示方式与它们在PDB（蛋白质数据银行）中的表示方式相同。也就是说，它们可以通过定义非标准的CCD代码（例如，用于磷酸丝氨酸的“SEP”）作为氨基酸残基的方式，或者可以通过在键表中添加额外的条目来表示，例如一个配体与氨基酸残基之间的共价键。——也就是说AlphaFold可以处理PDB文件中的共价修饰信息，这些修饰可以通过非标准的氨基酸残基标识或在键表中指定的共价键来表示。

### Confidence

<a href="https://www.imagehub.cc/image/13THWZ"><img src="https://s1.imagehub.cc/images/2023/11/07/39875175bc978a499bf6b78545706e17.png" alt="39875175bc978a499bf6b78545706e17.png" border="0" /></a>

上面是使用箱线图表示的一个“错误率-评价指标”的函数关系。

!!! quote Reference

    https://zh.wikipedia.org/zh-hans/%E8%9B%8B%E7%99%BD%E8%B4%A8%E7%BB%93%E6%9E%84

    https://storage.googleapis.com/deepmind-media/DeepMind.com/Blog/a-glimpse-of-the-next-generation-of-alphafold/alphafold_latest_oct2023.pdf

    https://deepmind.google/discover/blog/a-glimpse-of-the-next-generation-of-alphafold/