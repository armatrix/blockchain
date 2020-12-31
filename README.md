# blockchain

Crypto-currency

鸽笼原理

观测系统对系统的影响 google搜索

比特币中用到的密码学性质

collision resistance 

hiding 单向 没有什么好的方法

Puzzle friendly  

BTC SHA256满足上述的三个条件

另一个是密码学的签名

涉及BTC系统的账户管理

开户条件： 创立公私钥对  非对称加密体系asymmetric encryption algorithm

公私钥好的随机源 a good source randomness

数据结构

哈希指针 hash pointers 一个个区块链组成的链表 用hash pointers 代替普通的指针

blockchain is a linked list using hash pointers 这种数据接口可以实现 temper-evident log 修改某一节点 会改变后边所有的hash，普通链表值不会改变其他的，这里不同，牵一发动全身

merkle tree。最下面数据块 上面都是哈希指针

digital commitment 

digital equivalent of a sealed envelop

解决困难 验证简单

Non-membership proof （sorted merkle tree ）

merkle proof

无环的都可以使用哈希指针代替普通的指针

### BTC 协议

货币的发行方

数字货币防止双花 double spending attack

货币如何发行，如何验证交易的有效性

发起交易方需要用私钥签名，交易来源需要追溯，



签名 可复制 如何防范双花 

a-b a需要知道b的地址并且证明有足够的余额，b需要提供其公钥的hash，所有节点都需要知道a的公钥用来验证交易是否是合法的 

输入需要输入公钥  前一个输出的公钥的hash要和当前的输入相同，用以验证来源（hash pointer）

ab双方交易 第三方如何获取公钥 a需要在交易信息中声明自己的公钥信息

分布式共识 

coinbase tx

blockheader blockbody  

header： version、hash of previous block header、merkle root hash、 target、 nonce

轻节点不参与链的构造和维护



每个人都可以广播交易，有些是正确的，有些是错误的，谁来决定打包写到区块里？ 打包哪些？ 不同节点的账本如何维持一致性？（即账本的内容要实现分布式共识distributed consensus） 简单的例子是一个 分布式哈希表distributed hash table 取得共识的内容是 key value对

其中有一些不可能的理论 impossibility result

最为著名的是FLP 

asynchronous （网络传输假设没有时延上限）系统中 ，假设有一个节点是faulty也无法达成共识

另一个是CAP

consistency， availability， partition tolerance

任何一个分布系统上述三个属性只能有两个满足

比较有名的paxos在理论上客观存在无法达成一致的可能性



consensus in bitcoin 取得的共识的内容 是什么，hash rate 

有些节点是恶意的，如何设计节点的共识协议

- 投票可以吗，一个候选区块，大家投票，投票超过半数  这里 恶意节点不停的提供候选区块该怎么办，节点不投票怎么办，网络延迟，哪一轮的投票。 最主要还是谁有投票权，membership，sybil attack女巫攻击 不停的产生账号来进行投票 。比特币中不是按照账号来投票，而是通过算力来投票，nBit难度目标阈值的编码 nonce长度32位
- 

什么叫接收一个区块 网络位置不同 接收到的可能不同 除了最长链之外都丢弃

21w个区块减半 50btc



### 比特币系统实现

基于交易的账本模式  transaction-based ledger

没有显示记录账户余额的操作，需追溯。

全节点维持UTXO  unspent transaction output

total inputs = total outputs + TX fee

大约每四年减半（21w*10min/60min/24h/355day）



每次求解可以看作 bernoulli trial： a random experiment with binary outcome

bernoulli process: a sequence of indenpendent bernoulli trials, its memoryless

挖矿的概率可以用poisson process来近似

出块时间服从指数分布 exponential distribution

概率分析 已经挖了多久和即将是否要出现时没有什么关系的，progress free（挖矿公平性的保证）

加密货币不能设计出这种抛了一万次硬币，9999次都是反面，下面一定是正面这种，否则算力多的矿工会有不成比例的优势



比特币的数量

几何序列 geometric series

21w * 50 + 21w *25 + 21w* 12.5+.... = 21w* *50**(1+1/2+1/4+....)=21w * 50 * 2 = 2100w

挖矿对维持比特币系统的安全性是至关重要的 bitcoin is secured by mining

转账方需要签名  

Bitcoin 缺省需要6个confirmation 即1个小时

irrevocable ledger（概率上的保证），当达到一定的确认数 时，修改难度乘指数增长

Selfish mining (隐藏已经挖出去的块，待交易确认后，把自己隐藏的链公布，需要极为强大的算力，分叉攻击)，其他的好处：减少了竞争，存在风险，等同于和其他节点的算力和做竞争

### 比特币网络

比特币的节点网络都是对等的，从种子节点找寻其他节点，使用p2p链接。

设计原则：简单、鲁棒、但不高效。simple robust but not efficient

消息在节点中采取flooding方式，邻居节点的选取是随机的，不考虑底层的拓扑结构

在网络中维持了一个待写入交易的集合，节点收到后转发给邻居节点，后面就不转发了

区块大小的限制，块大小和网络传输的关系

### 分叉

- state fork。 forking attack deliberate fork 人为造成的


- protocol fork 软件升级。hard fork soft fork

  硬分叉：典型例子，区块大小 block size limit。1M = 1,000,00个字节，假设每条消息250个字节,10分钟出一个块

  1000000/250/10/60  大概 7TX/sec

  如果一些大算力节点去修改配置，比如大节点将节点大小上限改为4M，之前的旧节点是被认可的，数据仍然是小于4M的。分叉后的数据，需要用类似chain ID的东西来标识

  软分叉：对比特币的协议进行修改，导致之前的区块不符合的时候，比如将原本1M改为0.5M。这种改动会导致原本没有升级软件的节点需要参与最长合法链的时候，发现跟不上新链，被迫放弃已经挖出来的块

coinbase的部分作为挖矿难度的调整参数 extra nonce，另外一部分作为UTXO的根hash.

证明一个账户有多少钱，全节点来计算input output可以，对轻节点如何更快的证明

软分叉的例子P2SH： pay to script hash

### QA

转账交易的时候对方不在线？ 不需要在线

全节点接收到一笔转账可能收款方账户从来没有听说过？ 可能

私钥丢失了怎么办？ 没什么办法，就是没了

### 系统的匿名性

很早之前的中国银行还没有开始实名制

如何提高自己在系统中的匿名性

比特币是建立在p2p网络上的应用，所以 如何在网络上提高这个匿名性，使用多路径转发可以有效提高的网络节点的匿名性。

第二个是application层，首先是coin mixing（一种常用的做法，将你和周围的人混在一起），信誉度比较低

#### 零知识证明

数学基础是同态隐藏

- 如果x，y 不同，那么他们的加密函数值E(x)， E(y) 也不同
- 给定E(x)的值， 很难反推出x的值
- 给定E(x)， E(y) 的值，我们可以很容易地计算出某些关于x，y的加密函数值（同态运算）
  - 同态加法：通过E(x)和 E(y)计算出E(x+y)的值
  - 同态乘法：通过E(x)和 E(y)计算出E(xy)的值
  - 扩展到多项式

例子

alice想要向bob证明她知道一组数x和y使得x+y等于7，同时不让bob知道x和y的具体数值

简单的版本

- alice把E(x) 和E(y)的数值发给bob（性质2）

- bob通过收到的E(x)和E(y)计算出E(x+y)的值（性质3，同态加法）

- bob同时计算出E(7)的值，如果E(x+y)= E(7) （性质1），那么验证通过，否则失败

  这里存在的小问题是，bob可以通过对x的穷举来计算出可能的值，通常要将输入进行混淆

盲签方法

- 用户A提供SerialNum，银行在不知道SerialNum的情况下返回签名Token，减少A的存款
- 用户A把SerialNum和Token交给B完成交易
- 用户B拿SerialNum和Token给银行验证，银行验证通过，增加B的存款
- 银行无法把A和B联系起来
- 中心化

单纯的数字序号，很容易复制，造成双花。中心化的方案，央行有个数据库，记录了每个序列号的归属，发生交易再去更新这个数据库。

去中心化的方案，在这里就演变成了，如何让央行记账，但又不让央行记录这个序列号。需要个体利用盲签

零币和零钞

- 零币和零钞在协议层就融合了匿名话处理，其匿名属性来自密码学保证
- 零币（zerocoin）系统中存在基础币和零币，通过基础币和零币来回转换，消除就抵制和新地址的关联性，其原理类似于混币服务
- 零钞（zerocash）系统使用zk—SNARKs协议，不依赖一种基础币，区块链中只记录交易的存在性和矿工用来验证系统正常运行所需要关键属性的证明。区块链上既不显示交易地址，也不显示交易金额，所有交易通过零知识验证的方式进行。

初始的随机源特别重要，需求较高。 强匿名性的需求也不高。 但所有的一切与现实产生交易的时候匿名性就会发生问题

哈希指针

指针指在本地有意义，实际系统中并不是真正的指针。

通常将区块的全部数据存在一个本地的keyvalue中

一个好的货币需要具备通货膨胀的功能

货币金融学

量子计算能否破解，离实用还有很远。 首先冲击传统金融业，也不会是这个地方，大多数金钱也不在这里

公钥逆运算，这个即使用量子也计算不出来。 哈希函数会造成信息的丢失

暴露公钥的hash用来收钱就可以了 付钱是付到公钥的hash。

以太坊

 memory hard  mining puzzle 

proof of work      ASIC resistance

由proof of work 转化为 proof of stake

新增的重要的功能： smart contract

 货币是可以去中心化的，还有什么是可以去中心化的

以太坊的特性

bitcoin： decentralized currency    取代了政府在货币上的一些职能

ethereum：decentralized contract   去中心化合同的好处

类比法币，去中心化货币在上面的好处（结算费，结算周期等）

类似跨国的合作参与方，合同问题（不在同一个司法管辖权下面）

### 以太坊账户

account-based ledger 这种天然的防护双花，想要做类似双花的行为的话，直接从账户里扣除两次金额就可以了。维护账户的安全从追溯来源改成了状态树。 但会带来新的隐患——replay attack。前者属于花钱的人不诚实，后者属于收钱的人不诚实。 加个计数器nonce，计数器也收到签名的保护。状态树维护该值

以太坊中有两类账户，一类叫做 外部账户 externally owned account  其状态有账户余额 balance 和 nonce （计数器） 应该用单词计数器或序列号这种

另一类叫合约账户 smart contract account， 合约可以调用合约，不能使用账户。除了上述balanace和nonce之外，还有code 和 storage

比特币中侧重的是隐私，类似打一枪换一个地方（不同的账号），以太坊中主要为合约服务，更注重的是合约的主体，希望该主体有个稳定的身份。不同说签了合同之后人就溜了，或者有人冒名过来。

类似的基于账户模型可以产生一些金融衍生品 financial derivative

### 数据结构

需要实现的功能： 从账户地址到账户状态的映射 addr-->state 以太坊的账户160bits  40个16进制的数

Merkel proof 

比特币中每个区块所构建的Merkel tree是不可更改的，账户放在hash表中显然是不可行的，一棵merkle tree是否需要排序也是问题。如果不排序，查询的代价大，排序了，插入的代价太大（重构Merkel tree的大部分节点）

引出trie（来自retrieva，前缀树l），0～f 加上标志符，共17。

对路径进行压缩，引出patricia tree。在这种情况带来的问题是，如果新增节点，原本被压缩的部分可能会被展开

让分布稀疏是此类系统的解决方法

MPT （Merkel Patricia tree）这里一样把普通的指针换成hash指针

Modified MPT

维护旧有状态是为了undo tx

状态树中维护的是key value，value 使用RLP（recursive length prefix，只支持nested array of bytes）编码后存储

维护了所有账户的状态，无论他们有没有发生交易。 不这样设计

### 交易树和收据树

只有状态树维护了所有的数据，每个交易树和收据树在每个高度单独维护发生变动的账户。

对某一类型的数据查询，引入bloom filter (有可能出现false positive）数据结构，简单的是不支持删除操作的。

轻节点只要存块头就可以了

以太坊的运行过程可以看作一个交易驱动的状态机（transaction-driven state machine） 

比特币中的状态是UTXO，两者的共同特征是其状态的转移是确定性的

这个里面有bloom filter的实现

### GHOST协议

比特币的flooding 传播出去要10几秒，出块有10min，比特币中只有一条最长合法链

mining centralization  大型矿池中成为最长合法链的机会会更多，centralization bias（中心化带来不成比例的优势）

基于GHOST协议的共识机制（挖矿成功但不是最长合法链 中，成为orphan block 或 stale block，有一部分奖励，被称为uncle block，得到7/8个块奖励，如果下一个块包含了一个uncle block，会得到额外的1/32的奖励，最多可以同时包含两个uncle block）

隔代的包含最多可以回溯6⃣️个，奖励从2/8～7/8（在七代以内 at most seven generation），类似这种机制用来激励出现分叉尽早合并

如何解决临时性分叉

### 以太坊挖矿算法

blockchain is secured by mining

btc 最初想做one cpu， one vote

后来的加密货币都想做到 ASIC resistance

对内存需求很高的puzzle， memory hard mining puzzle

其中有一个litecoin，算法使用scrypt。

Time-memory tradeoff

Difficult to solve, but easy to verify

加密货币的冷启动问题， 基于工作量证明的系统，少数人挖矿对系统的安全很有影响

```python
# 通过seed计算出cache的伪代码，省略了对cache中元素的进一步处理，只展示原理，即cache中元素按序生成，每个元素产生时与上一个元素相关。每隔30000个块会重新生成seed（对原seed求hash），并且利用新的seed生成新的cache。cache初始大小为16M，每隔30000个块重新生成并增大初始大小的1/128（128K）
def mkcache(cache_size, seed):
	o = [hash(seed)]
	for i in range(1, cache_size):
		o.append(hash(o[-1]))
	return o

# 通过cache来生成dataset中第i个元素的伪代码。这个dataset叫做DAG，初始大小是1G，也是每隔30000个块更新，同时增大初始大小的1/128（8M）。先通过cache中的第i%cache_size个元素生成初始的mix，因为两个不同的dataset元素可能对应同一个cache中的元素，为了保证每个初始的mix都不同，注意到i也参与了哈希计算。随后循环256次，每次通过get_int_from_item来根据当前的mix值求得下一个要访问的cache元素的下标，用这个cache元素和mix通过make_item求得新的mix值。注意到由于初始的mix值都不同，所以访问cache的序列也都是不同的。最终返回mix的哈希值，得到第i个dataset中的元素。多次调用这个函数，就可以得到完整的dataset。
def calc_dataset_item(cache, i):
	cache_size = cache.size
  mix = hash(cache[i % cache_size] ^ 1)
  for j in range(256):
    # 下一个用到的cache中的元素的位置是通过当前用到的cache的元素的值计算得到的，这样的具体的访问顺序事先不可预知，满足伪随机性
    cache_index = get_int_from_item(mix)
    mix = make_item(mix, cache[cache_index % cache_size])
  return hash(mix)

# 生成dataset中全部的full_size个元素
def calc_dataset(full_size, cache):
  return [calc_dataset_item(cache, i) for i in range(fill_size)]

# ethash算法的puzzle：通过区块头、nonce以及DAG求出一个与target比较的值，矿工和轻节点实现的方式是不一样的。先通过header和nonce求出一个初始的mix，然后进入64次循环，根据当前的mix值求出要访问的dataset的元素的下标，然后根据这个下标访问dataset中两个连续的值（伪随机值，两者并无直接关联）。注意轻节点是临时计算出用到的dataset的元素，而矿工是直接访存，也就是必须在内存中离存着这个1G的dataset。
def hashimoto_full(header, nonce, full_size, dataset):
  mix = hash(header, nonce)
  for i in range(64):
    dataset_index = get_int_from_item(mix) % full_size
    # 和轻节点中一样，由于矿工需要验证非常多的nonce，如果每次都从16M的cache中重新生成的话，挖矿效率会很低，其中有些又是重复计算，随机选取的dataset的元素中有很多是重复的，可以是之前尝试别的nonce时用过的。所以，miner采取以空间换时间的策略，把整个dataset保存下来。轻节点由于只验证一个nonce，验证的时候就直接生成要用到的dataset中的元素就行了。
    mix = make_item(mix, dataset[dataset_index])
    mix = make_item(mix, dataset[dataset_index + 1])
  return hash(mix)

def hashimoto_light(header, nonce, full_size, cache):
  mix = hash(header, nonce)
  for i in range(64):
    dataset_index = get_int_from_item(mix) % full_size
    mix = make_item(mix, calc_dataset_item(cache, dataset_index))
    mix = make_item(mix, calc_dataset_item(cache, dataset_index + 1))
  return hash(mix)

# 矿工挖矿函数，full_size指的是dataset的元素个数，dataset就是从cache生成的DAG，header是区块头，target挖矿目标，我们需要调整nonce来使hashimoto_full的返回值小于等于target。这里先随机出实话nonce，再一个个尝试nonce，直到得到的值小于target.
def mine(full_size, dataset, header, target):
  nonce = random.ranint(0, 2**64)
  while hashimoto_full(header, nonce, full_size, dataset) > target:
    nonce = (nonce + 1) % 2 ** 64
  return nonce
```

从工作量证明（PoW）转向权益证明（PoS proof of stake），市值上来了，才有ASCI研发的价值

Pre-mining

PoW中 挖矿决定安全，通用设备挖矿存在租用攻击的可能



### TODO

使用rust进行多签

