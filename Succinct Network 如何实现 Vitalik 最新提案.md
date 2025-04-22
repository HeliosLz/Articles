Vitalik 近期在以太坊研究论坛上发布的"长期L1执行层提案：用 RISC-V 替代 EVM "的深入思考。该提案建议在以太坊执行层用 RISC-V 替代现有的 EVM，以提高效率和简化系统。在分析这一提案的过程中，我发现 Succinct Network 正在构建一个基于 RISC-V 的全球零知识证明基础设施，两者在技术愿景上存在显著共鸣。以下是我读完了它的白皮书后学习到的内容：

![](https://private-us-east-1.manuscdn.com/sessionFile/68cxbbyd6MS9Um8GHPceWh/sandbox/tHBvnwiLQDCECWZWoIz6PP-images_1745234797727_na1fn_L2hvbWUvdWJ1bnR1L2ltYWdlcy9zcDFfYXJjaGl0ZWN0dXJl.png?Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9wcml2YXRlLXVzLWVhc3QtMS5tYW51c2Nkbi5jb20vc2Vzc2lvbkZpbGUvNjhjeGJieWQ2TVM5VW04R0hQY2VXaC9zYW5kYm94L3RIQnZud2lMUURDRUNXWldvSXo2UFAtaW1hZ2VzXzE3NDUyMzQ3OTc3MjdfbmExZm5fTDJodmJXVXZkV0oxYm5SMUwybHRZV2RsY3k5emNERmZZWEpqYUdsMFpXTjBkWEpsLnBuZyIsIkNvbmRpdGlvbiI6eyJEYXRlTGVzc1RoYW4iOnsiQVdTOkVwb2NoVGltZSI6MTc2NzIyNTYwMH19fV19&Key-Pair-Id=K2HSFNDJXOU9YS&Signature=RL60E8EUVLdzh45W~~53rGusSPjW7ZWrJIVI9uWuxAxLsut3DfZDTRYMMpGm2kXVp7tW95fP1X3Lqs1NDLVazdLXk0Oh6Z2vn1KokzN9EuwRZa-H1697voMawdqDm4Y9ZN92OTZP36Z2sq6fXjGHJg~204c4is2Xh~vF-j4b84Q7CwEGw0jJPAH7eGP2JPJ99WNPUsB9kz-nDZEjJ7CoFBzbQsvWTNIBHBVjXRmw6LlADroWcqxhNkjxlPIieP~HMnxA-TJB3bBGSu1EXcbCym8gWYjugDD9l75MAGkjXs1CepZNmw5naps9xsu7gIexH5b5sUHISkGk7U-2gxeTqQ__)

1）首先，Succinct Network的SP1 zkVM完美呼应了Vitalik提出的将EVM替换为RISC-V的愿景。SP1采用RISC-V指令集架构作为其虚拟机的基础，能够为任意Rust程序（或任何LLVM编译语言）生成零知识证明。这与Vitalik的提案高度一致 - 都是利用RISC-V这一更高效、更标准化的指令集架构来提升区块链执行层的性能。

**重要说明：**

- SP1 zkVM支持完整的Rust标准库，这意味着开发者可以利用现有的技能和工具
- 与传统EVM相比，RISC-V架构提供了更高的灵活性和效率，特别是在ZK证明生成方面
- SP1通过STARK递归证明和Groth16包装器，使证明可以在任何EVM链上以约30万gas的成本验证

2）**数学模型与平衡机制**

![Succinct Network (1)](https://github.com/user-attachments/assets/8aacf73a-f93d-4bd7-910c-f8c43e032d82)


架构图底部显示的数学公式代表了证明者选择机制：

**α参数控制证明者选择概率 - 证明者被选中概率: bi^α / Σ(bj^α)**

这个公式中：

- α是控制参数，影响效率与去中心化的平衡
- bi是证明者i的出价
- 当α=0时，所有证明者被选中概率相等，最大化去中心化
- 当α增大时，出价更高的证明者被选中概率增加，提高效率

这种设计允许网络根据需求动态调整去中心化和效率之间的平衡，类似于比特币的挖矿难度调整机制。

老实说，Succinct Network的"证明竞赛"机制乍看有些激进，但深层次剖析，这种去中心化的证明者网络恰恰是解决ZK技术大规模应用的关键。面对ZK证明生成的高计算需求，Succinct选择构建一个由多样化证明者组成的网络，通过证明竞赛机制匹配证明请求，这不仅提高了证明的可用性，还通过规模经济降低了成本。

因为，在纯技术指标对比下，传统的中心化证明基础设施难以满足未来区块链应用的需求，而Succinct的去中心化证明网络在保持高性能的同时，维护了区块链的核心价值 - 去中心化和开放性。

恐怕有人会问了这如何维护去中心化了？上面公式中提到了 **α参数控制证明者选择概率，我们暂时先让 α = 1，**假设有三个证明者，出价分别为：

- 证明者A：出价100
- 证明者B：出价50
- 证明者C：出价25

当α=1时，各证明者被选中的概率为：

- 证明者A：100/(100+50+25) = 100/175 ≈ 57.1%
- 证明者B：50/175 ≈ 28.6%
- 证明者C：25/175 ≈ 14.3%

想象一下，Succinct 随机生成一个 0到1 之间的数字：

- 如果随机数在0到0.571之间，选择证明者A(57.1%的概率)
- 如果随机数在0.571到0.857之间，选择证明者B(28.6%的概率)
- 如果随机数在0.857到1之间，选择证明者C(14.3%的概率)

这样，即使证明者B和C的出价较低，它们仍然有各自28.6%和14.3%的概率被选中。这就是为什么低出价证明者依然有机会参与系统的原因。

**那什么因素会影响 α 参数会改变了？**

在Succinct Network 白皮书第11页提到该参数是"由协议设置的”，所以 α参数的调整是一个协议治理决策，而不是自动变化的，当α值增大时，高出价者的优势会进一步放大；当α值减小时，出价差异对概率的影响会减弱。α=1代表了一个平衡点，既保持了经济激励，又避免了单一证明者完全垄断网络。

3）不过为新技术兴奋喝彩之余要明白，SP1的"预编译中心"架构虽然带来了显著的性能提升，但这种模块化方法的全面部署仍需时日。从技术实现路径看，这种架构允许用户添加"预编译"到核心zkVM逻辑中，从而在各种用例中获得与电路相当的性能。

**性能对比：**

| **程序** | **SP1证明时间** | **其他zkVM证明时间** | **性能提升** |
| --- | --- | --- | --- |
| 基准测试 | - | - | 最高28倍 |
| ZK Tendermint轻客户端 | 4.6分钟 | 2.2小时 | ~28倍 |

来源：[**https://blog.succinct.xyz/introducing-sp1/**](https://blog.succinct.xyz/introducing-sp1/)

相较于其他zkVM解决方案（如Risc0和Jolt），SP1的开源方法（MIT/Apache 2.0许可）和社区驱动的开发模式，使其更具透明度和安全性。这种开放的方法鼓励协作和持续改进，也便于第三方审计。

以上。在我看来，Succinct Network不仅是简单的技术创新，而是区块链面对扩容挑战的一种战略性应对。其SP1 zkVM与以太坊路线图中关于RISC-V替代EVM的愿景高度契合，代表了ZK技术在区块链领域的实际应用方向。随着ZK技术的不断成熟，Succinct Network这类项目将在构建更高效、更灵活的区块链基础设施中发挥关键作用。
