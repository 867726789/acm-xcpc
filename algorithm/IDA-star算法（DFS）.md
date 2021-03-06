# IDA*算法

### 基本思路
首先将初始状态结点的H值设为阈值maxH，然后进行深度优先搜索，搜索过程中忽略所有H值大于maxH的结点；如果没有找到解，则加大阈值maxH，再重复上述搜索，直到找到一个解。在保证H值的计算满足A\*算法的要求下，可以证明找到的这个解一定是最优解。在程序实现上，IDA\* 要比 A\* 方便，因为不需要保存结点，不需要判重复，也不需要根据 H值对结点排序，占用空间小。而这里在IDA\*算法中也使用合适的估价函数，来评估与目标状态的距离。

在一般的问题中是这样使用IDA\*算法的，<b>当前局面的估价函数值+当前的搜索深度 > 预定义的最大搜索深度</b>时，就进行剪枝。这个估计函数的选取没有统一的标准，找到合适的该函数并不容易，但是可以大致按照这个原则：在一定范围内加大各个状态启发函数值的差别。

