
### Cross Entropy
1. 什麼是 Cross Entropy:
    <div align="center">
    <img src=img/3-2.png width=500x>  
    </div>


2. 為什麼 Cross Entropy 比 MSE 更適合用在 classification?  
因為當 Loss 大的時候， cross-entropy 會有 gradient，可以透過 gradient descent 更新， MSE 則是很平坦，沒有斜率可以更新。  
    <div align="center">
    <img src=img/3-1.png width=500x>  
    </div>

3. 關於 cross entropy 的數學推論： Refer to [2].     
    <div align="center">
    <img src=img/3-3.png width=500x>  
    </div>

Quote from [2]:    
```
這裡的X^n ，表示第一個樣本點，y^n表示第n個樣本點的class標籤，最終這個summation的形式，裡面其實是兩個Bernouli distribution的cross entropy(交叉熵)，把f(X^n) & y^n 都當成Bernoulli distribution，兩者的cross entropy， 就是我們要minimize 的對象。
cross entropy的含意就是這兩個distribution 有多近，如果 p和 q 兩個distribution 一模一樣的話，那他們的 cross entropy 就是0。這裡的 f(X^n)就是function 的 output ，y^n 表示預期的target，因此 cross entropy 實際上表達的是希望這個function 的 output 和他的 target 越接近越好。
```

4. Logistic regression 和 linear regression 的比較：  
    <div align="center">
    <img src=img/3-4.png width=500x>  
    </div>


## Reference
[1] [Lecture 4: classification 筆記](https://medium.com/%E4%B8%89%E5%8D%81%E4%B8%8D%E5%93%AD/%E6%A9%9F%E5%99%A8%E5%AD%B8%E7%BF%92%E8%87%AA%E5%AD%B8%E7%AD%86%E8%A8%9805-classification-probabilistic-generative-model-ddcf65e244a5)  
[2] [ML Lecture 5: Logistic Regression 筆記](https://medium.com/%E4%B8%89%E5%8D%81%E4%B8%8D%E5%93%AD/%E6%A9%9F%E5%99%A8%E5%AD%B8%E7%BF%92%E8%87%AA%E5%AD%B8%E7%AD%86%E8%A8%9806-logistic-regression-3c0dbc10400e)