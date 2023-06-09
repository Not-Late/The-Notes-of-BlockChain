参考https://blog.csdn.net/lvyibin890/article/details/106143781?ydreferer=aHR0cHM6Ly93d3cuYmluZy5jb20v
https://zhuanlan.zhihu.com/p/340630187

# 1.Fabric的四种节点
## 1.1 客户端节点
介于应用程序与底层之间，是两者之间交互的媒介节点。它不能独立存在，必须与Orderer节点与Peer节点建立连接，才可以发挥作用，比如连接到Orderer节点进行通道创建，连接到Peer节点进行交易模拟执行

## 1.2 Peer节点
Peer节点是一个统称，包含了Leader（主节点），Anchor（锚节点），Endorser（背书节点）以及Committer（记账节点）

1、**Leader主节点**连接到Orderer节点并与之通信，从Orderer节点获取区块，并通知给组织内部的其他节点，主节点可以通过自动选举产生。

2、**Anchor锚节点**是在通道上可以被所有其他Peer节点发现的节点，负责和其他组织通信，可以有多个。

3、**Endorser背书节点**可以简单理解为担保，它就是为交易做担保的。Farbic中的共识的第一步，就是应用程序向背书节点发送一个交易提案，交易背书节点经过交易模拟执行以后，返回给应用程序背书结果以及签名。

Endorser背书节点不是一个固定的节点类型，是和智能合约绑定的。每一个智能合约在安装到区块链上的时候，都会设置其专有的背书策略，指定该智能合约的交易由哪些节点背书以后才是有效的。也就是说只有在Endorser背书节点上才会运行智能合约。

如果一个Peer节点不是某些背书策略里的一员，那么就是一个普通的Peer节点，也就是最后的Committer记账节点。

4、**Committer记账节点**，所有的Peer节点都是记账节点，无论它是否是Anchor锚节点还是Endorser背书节点。记账节点的主要功能是用于验证从Orderer节点接收到的区块，验证区块的有效性以及交易的有效性，验证完以后记录到本地的账本中。如果交易有效，同时更改区块链上的状态数据。

Peer节点的几种类型不是互相排斥的，一个节点可能同时是几种类型，也有可能只是Committer记账节点

## 1.3 Orderer节点
Orderer节点可以称之为排序节点，主要有两个功能

- 从全网的客户端节点接收交易，然后将交易按照一定的规则进行排序
- 将排序好的交易按照固定的时间间隔打包成区块，然后分发给其他组织的主节点

有两种常用类型的排序
- solo，整个网络中只有一个排序节点，它收到的交易的顺序就是整个网络中的排好序的交易顺序。整个模式仅仅适用于开发和测试时用，如果Orderer节点挂掉了，整个网络就是瘫痪的
- kafka，将整个网络中的交易排序过程转交给了kafka集群，每一个Orderer节点都是kafka集群的生产者和消费者，生产者将从客户端节点接收到的交易转发给kafka集群，同时消费者中kafka集群里面获取交易，这样或得到的交易就已经是排好序的了

## 1.4 CA节点（可选）
证书颁发机构，鉴定一个区块链的身份是否是合法的，只有被认可的身份才能在区块链中进行交易。

可有选择官方的CA，也可以选择第三方CA，这样就没有CA节点。


# 2.交易流程
在区块链中，从链码（智能合约）的编写到最终的执行和提交过程中涉及到多个阶段和参与者。下面是这些阶段的概述：

- 提案（Proposal）：
提案阶段是指链码的调用或事务的生成过程。在这个阶段，客户端创建一个包含要执行的链码函数和参数的提案。提案通常使用链码的标识符、输入参数和签名等信息。提案被广播到区块链网络的节点，以便进行背书和共识验证。

- 背书（Endorsement）：
背书阶段发生在链码被调用的节点上。在这个阶段，提案被发送到网络中的各个背书节点。每个背书节点将根据链码的逻辑执行提案，并生成一个背书结果。背书结果包括执行结果和背书节点的签名。这些背书结果用于后续的共识验证。

- 排序（Ordering）：
排序阶段发生在区块链网络中的排序服务节点上。在这个阶段，背书结果被收集并按照确定的顺序（通常是时间顺序）组成一个区块。排序节点负责确保所有交易按照相同的顺序被添加到区块链中。排序阶段的结果是生成一个有序的区块，包含了待提交的交易。

- 验证（Validation）：
验证阶段发生在区块链网络中的验证节点上。在这个阶段，验证节点对区块中的每个交易进行验证。验证节点执行一系列的检查，包括检查交易的格式、签名的有效性、背书节点的合法性等。如果交易通过验证，验证节点将交易标记为有效。

- 提交（Commitment）：
提交阶段是指将经过验证的区块添加到区块链中的过程。在这个阶段，经过验证的区块被广播到网络中的所有节点。每个节点都将验证并存储该区块，使其成为区块链的一部分。提交阶段完成后，区块中的交易被视为永久性的，并且状态的改变被应用到系统中。

通过这些阶段的顺序进行，区块链网络可以实现链码的调用、背书、共识验证和区块的提交，确保交易的一致性和系统的正确性。每个阶段都涉及不同的参与者和操作，以确保交易的有效性和安全性。
