

* Policy 可以是一個 deep learning network 也可以是一個 rule-based function， input 就是目前看到的東西（例如遊戲畫面）。讓 machine 看到什麼東西，會決定 machine 好不好 training。 output 則是每一個行為的機率。

* 藉由 gradient ascent 更新 model，使得 reward 變高。假如在某個 state 進行某個 action， reward 會變高，那就要增加這一項決策的機率。

* 先用目前的 model 去和環境互動，蒐集一堆遊戲紀錄，再用這些遊戲紀錄來 update model。

* 實作上，和原本 classification 唯一不同的地方在 loss 的地方， summation 裡面每一項都要乘上一個 weight，也就是 reward，是在 state s 採取 action a，使得在整場遊戲最後得到的 reward。

* tip 1: add a baseline
在很多遊戲裡面，大部分的 reward 都是正的，就代表告訴 model 說，不管採取什麼 action，都要提升該 action 的機率。這件事本來不是問題，因為雖然大家的機率都要提高，但是整體來說大家的機率有大有小，所以經過 normalize 之後，相當於有升有降。但是在 training 時實際上並不是對”所有的“ action 做計算，而是只 sample 一些 action 來看。那麼那些沒有 sample 到的 action，就會自動被認為機率應該要下降，這樣顯然是有問題的。
為了解決這樣的問題，可以設計讓 reward 不要總是是正的。把 reward 減掉一項 b，這個 b 就是 baseline。

如何取 baseline? 最簡單的做法是，算 tao(n) 的平均值當作 b 來用。 b ~ E[R(tao)]
實作上，你會一直把 R(tao) 記錄下來，並一直計算 R(tao)的平均值，拿來當作 b。

* tip-2: assign suitable credit  
原本的做法是，每個 action 乘上的 reward 是最終遊戲的 reward。但是一場遊戲贏了，不代表過程中的每個 action 都是對的，所以希望可以在 action 前面承上一個 weight，忠實呈現這個 action 的好壞，實際上對最後分數貢獻多大。

作法：不是乘上整場遊戲的 reward，而是乘上從這個 action 之後，遊戲累積的 reward，因為這個遊戲在這個 action 之後發生的事情，才是有受到這個 action 影響的部分。

另外，也會對未來的 action 做一些 discount，因為太後來的 reward，可能就不是這個時間點的功勞了。 實作上，會在 reward 前面乘上 gamma^(t' - t)，通常 < 1，例如 0.9/0.99，當未來時間點 t' 離現在 t 越遠，就會乘上越多個 gamma，於是 reward 就會變小。

可以把 reward 跟 b 合稱為 advantage function (A)， depend on (s, a)，並且是根據去跟環境互動的這個 model (theta) 。這個 A 也可以是由 model 預測出來的，這樣的方法叫做 actor-critic。


## From on-policy to off-policy



## Reference
* [DRL lecture 1](https://www.youtube.com/watch?v=z95ZYgPgXOY)