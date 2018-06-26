
---
layout: post
title: Beam搜索
---
上午看了一下beam search。搜了半天没几个网页讲了这算法到底怎么做的。
终于找到了一个靠谱的 ，简直活雷锋，[这位的博客](http://hongbomin.com/2017/06/23/beam-search/)

设beam size B，每步取最大的K个。

模型输出的预测结果是一个N*L的矩阵，其中N是词语的个数，L是序列的长度。其中的第n行第l列代表序列中第l个
元素为词语n的对数概率。

以前，在测试时，我是把矩阵按列取argmax作为模型的预测。这样做的问题是，按列取argmax所得到的序列，
不一定就是使得likelihood最大的序列。正确的做法应该是从所有可能的序列里找出那么一个。【这么看来，beam search
就只在测试时有用了】

但是从所有可能序列里找一个将会很慢。所以人们发明了beam search。具体步骤如下：

1) 初始化B个空的序列。

2) 每一步，从下一个元素里选K个最有可能的，为这B个序列每个都加上这些元素。共得到KxB个序列。老序列的对数似然
加上新加元素的对数似然得到新序列的对数似然。

3) 从这KxB个序列里取对数似然最大的B个序列

4) 重复2) 3)直到这B个序列里的每一个都得到EOS

```python
import torch
import pdb

def beam_search(logits, B, K, SOS_token=0):
    """
    beam search for prediction
    :param logits: bsize x length x nWord
    :return: bsize x length
    """
    bsize, length, nword = logits.size()
    # initialize
    beam = logits.new(bsize, 1, B).long().fill_(SOS_token)
    beam_logits = logits.new(bsize, B).fill_(0)
    # beam = torch.LongTensor(bsize, 1, B).fill_(SOS_token)
    # beam_logits = torch.Tensor(bsize, B).fill_(0)
    for i in range(length):
        # pdb.set_trace()
        # find top k of the next frame and apppend them to beam
        topk_data, topk_ind = torch.topk(logits[:, i], K, 1)
        topk_data = topk_data.view(bsize, -1, 1)
        beam_logits = beam_logits.view(bsize, 1, -1)
        beam_logits = beam_logits.expand(bsize, K, B) + topk_data.expand(bsize, K, B)  # beam_logits(i,j)=topk(i)+beam(j)
        # find top k to consist the new beam
        beam_logits, id = torch.topk(beam_logits.view(bsize, K*B), B, 1)
        beam = beam.view(bsize, -1, 1, B)
        beam = torch.cat((beam.expand(bsize, -1, K, B), topk_ind.view(bsize, 1, -1, 1).expand(bsize, 1, K, B)), 1)
        beam = beam.view(bsize, -1, K*B)
        id = id.unsqueeze(1).expand(bsize, beam.size(1), -1)
        beam = torch.gather(beam, 2, id)
    return beam[:, 1:, 0]


if __name__ == '__main__':
    data = torch.Tensor(4, 3, 9).normal_()
    sb = beam_search(data, 2, 5, 8)
    pdb.set_trace()
```
