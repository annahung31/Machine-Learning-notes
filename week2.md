## General Guide of training model
觀察 training loss 和 testing loss，來知道 model 出了什麼問題。
1. Training loss 高：
    * model bias: 模型太簡單了，增加模型複雜度。
    * [optimization problem](#opt)
    * [判斷方法](#judge)

2. Training loss 低， testing loss 高：
    * [over-fitting](#over-fitting)
    * Mismatch: training data 和 testing data 的 distribution 不同。解決辦法：透過你對 data 的瞭解，妥善分配 training set 和 testing set。

3. [Testing loss 的陷阱](#cross-val)



<a id="judge"></a>

## 如何知道我的模型是太簡單，還是沒學到？  

透過測試不同的模型結構，由簡單到複雜，瞭解模型的 capacity。  

<img src=img/2-1.png width=500x>  

理論上，當加大了模型的複雜度，則 training loss 應該會降低，因為有更多的參數可以解決問題。但較複雜的模型的 training loss 仍然比較簡單的模型高，代表是 [optimization](#opt) 失敗的問題。  

所以針對沒做過的 task，應該先 train 小的/複雜度低的模型，知道可以得到怎麼樣的 loss，再增大模型複雜度，觀察 training loss。


<a id="over-fitting"></a>

## Over-fitting  

模型發生背答案的情況，導致 training loss 低，但是 testing loss 高。  
解決辦法：  

1. 增加 training data, 做 data augmentation
2. 給 model 增加一些限制，降低自由度。常見給予限制的方法：
    * 更少的參數、共用參數。例如：從 Fully-connecyed network 到 CNN。
    * 更少的 feature
    * Early stopping
    * Regularization
    * Dropout

然而，當給予 model 太多限制的話，也可能導致 model bias，而學不出東西。因此是一個 trade-off。通常當 training loss 不再降低， testing loss 增高之前的 model 為最佳。  

<div align="center">
<img src=img/2-2.png width=300x>  
</div>

<a id="cross-val"></a>

## Cross validation
* 不要只是根據 testing set / public score 來決定 model 的好壞，因為有可能過度 over-fitting 在 testing set 上，反而在 private set 上表現很差。
* 更好的做法是，透過 cross validation，專注在調整自己的 validation loss。而不是太在意 public loss。（過了 strong baseline 之後）
* N-fold cross validation: 把 training data 分成 N 份，輪流當 validation set，算 model 的平均表現來決定最佳 model。

<div align="center">
<img src=img/2-3.png width=300x>  
</div>

<a id="opt"></a>

## Optimization
* 當 training loss 一直降不下去，可能是遇到 `critical point`，也就是 gradient = 0 的點。
* critical point 有兩種： local minima, saddle point。
<div align="left">
<img src=img/2-4.png width=500x>  
</div>

* 透過討論該 critical point(以 _C_ 表示)與其附近點的 loss 之二階微分 (_H_ , Hessian)，可以辨別目前是 local minima 還是 saddle point：
    * _H_ 的所有 eigen values 都為正： 附近點比 _C_ 還高 -> _C_ 為 local minima。
    * _H_ 的所有 eigen values 都為負： 附近點比 _C_ 還低 -> _C_ 為 local maxima。
    * _H_ 的 eigen values 有些正有些負： 附近點有些比 _C_ 高，有些比 _C_ 低 -> _C_ 為 saddle point。
* Hessian 還能告訴我們該往哪邊 update，但是實務上很少這樣做，因為運算量很大。（但是假如有一天都沒有招了，還有這一招可以用）
* 但是 local minima 可能只是個假議題，因為在低維度中看起來是 local minima，但是在更高維度上看可能就只是個 saddle point，有降低的可能。 實驗證實，大部分的情況都只是 saddle point。 [Reference](https://docs.google.com/presentation/d/1siUFXARYRpNiMeSRwgFbt7mZVjkMPhR5od09w0Z8xaU/edit#slide=id.p3)  

<div align="center">
<img src=img/2-5.png width=500x>  
</div>


## Tips for training: batch and momentum

### Batch

* small batch 和 large batch 的比較：
    * 所花費時間：雖然 large batch 一次要處理的 data 比較多，但是每個 epoch 需要 update 的次數比較少，因此當有平行處理時，small batch 每個 epoch 所需花的時間反而較長：
    <div align="center">
    <img src=img/2-9.png width=500x>  
    </div>


    * 大的 batch size 比較 stable，但是可能導致 optimization failed。用小的 batch size 比較 noisy，反而可以幫助 model update。因為 small batch 每次 update 時的 loss 都是略有差異的，比較不容易被卡住。
    <div align="center">
    <img src=img/2-6.png width=500x>  
    </div>

    * 一個有趣的實驗結果與解釋：large batch 比較容易走進死胡同， small batch 的 noisy update 雖然比較容易跳來跳去，也因此只有在遇到平坦的 local minima 才會停下來，這使得 small batch 在 testing 的時候表現比較好。
    <div align="center">
    <img src=img/2-7.png width=800x>  
    </div>
    * 總比較：

    <div align="left">
    <img src=img/2-8.png width=500x>  
    </div>

    * 兼具 large batch 和 small batch 好處的研究：
        * Large Batch Optimization for Deep Learning: Training BERT in 76 minutes (https://arxiv.org/abs/1904.00962)
        * Extremely Large Minibatch SGD: Training ResNet-50 on ImageNet in 15 Minutes (https://arxiv.org/abs/1711.04325)
        * Stochastic Weight Averaging in Parallel: Large-Batch Training That Generalizes Well (https://arxiv.org/abs/2001.02312)
        * Large Batch Training of Convolutional Networks (https://arxiv.org/abs/1708.03888)
        * Accurate, large minibatch sgd: Training imagenet in 1 hour (https://arxiv.org/abs/1706.02677)





### Momentum

* 就像物理世界一樣，物體有動量，就不會被 saddle point 卡住。
* 原本的 update 方向 = gradient 的反方向。 加了 momentum 之後， update 方向 = gradient 的反方向 + 前一步的方向。
* Momentum 的另一種解讀：考慮過去所有 gradient 的總和。

    <div align="left">
    <img src=img/2-10.png width=800x>  
    </div>

* 有了 momentum，即使某個位置 gradient 告訴你該往回走，但是慣性告訴你該繼續向前走，你便有可能越過山丘，找到更好的 minima。

    <div align="left">
    <img src=img/2-11.png width=400x>  
    </div>

<!-- <a id=\"imp\"></a> -->