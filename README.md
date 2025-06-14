# AllowableStress
## 許容応力の確率論的決定
#### [Shinsuke Sakai](https://sites.google.com/view/shinsukesakai/%E3%83%9B%E3%83%BC%E3%83%A0)   
 Emeritus Professor, The University of Tokyo, Japan   
 Visiting Professor, Yokohama National University, Japan

 ## 参考文献
 [酒井信介、"許容応力の確率論的決定法"、圧力技術60巻1号(2022)、pp.18-23.](https://www.jstage.jst.go.jp/article/hpi/60/1/60_18/_article/-char/ja/)

 ## 使用プログラム言語
 Python
 
 ## ライセンス
 このプロジェクトは，MITライセンスのもとにライセンスが付与される。詳細は[LICENSE](LICENSE)を参照のこと。
 
 ## インストール方法
 ターミナルモードにおいて
 ```python
 pip install AllowableStress
 ```

 ## 事前準備
 ```python
 from AllowableStress import AllowableStress as Astress
pas=Astress.PAStress()
```
## メソッドの詳細は下記コマンドで確認できる。
```python
help(AStress)
```
# 正規分布

 ## $\mu$,$\sigma$ともに既知の場合
引張強さの分布が$N(400,30^2)$の正規分布であるとき，P=0.01満足する許容応力$S_a$を決定する。
```python
pas.LowerLimit(mu=400, sigm=30, P=0.01)
#330.2095637787748
```

## $\mu$のみが未知の場合
引張強さの許容応力を決定するために，5本の強度実験を実施した。その結果$\hat{\mu}=400MPa$が得られた。信頼度90%，信頼水準95%を満足するための許容応力$S_a$を求めよ。ただし，過去の経験から強度の標準偏差$\sigma=20MPa$が与えられているものとする。
```python
pas.SetParam(param='n',val=5)
pas.SetParam(param='mu',val=400)
pas.SetParam(param='sigm',val=20)
pas.Bbasis2()
#359.6569505975057
```

## $\mu$,$\sigma$ともに未知の場合
引張強さの許容応力を決定するために，5本の強度実験を実施した。その結果$\hat{\mu}=400MPa$，$\hat{\sigma}=20MPa$が得られた。信頼度90%，信頼水準95%を満足するための許容応力$\hat{S_a}$を求めよ。
```python
pas.SetParam(param='n',val=5)
pas.SetParam(param='mu',val=400)
pas.SetParam(param='sigm',val=20)
pas.Bbasis()
#331.867335129965
```
## A許容値，B許容値の決定
5本の引張試験(サンプルサイズ$n$=5)の結果，引張強さ(408.4, 374.0, 395.9, 405.8, 412.3)(単位:MPa)が得られた。この材料の$\mu$,$\sigma$ともに未知であるとするとき，A許容値，B許容値を決定せよ
```python
data=[408.4, 374.0, 395.9, 405.8,412.3]
pas.SetData(data)
SaA=pas.Abasis()
SaB=pas.Bbasis()
SaA,SaB
#311.00403498262483 346.8989842383104
```

# 対数正規分布
母集団の強度Sが対数正規分布に従い、logSの平均値を$\mu_L$,標準偏差を$\sigma_L$とする。まず、対数正規分布に従うサンプルデータ10点を下記により発生する。$\mu_L$のみが未知の場合と、$\mu_L$,$\sigma_L$ともに未知の場合について、A許容値、B許容値の評価プログラムを示す。
```python
import numpy as np
sigm=np.log(1.1)
mu=np.log(400)
n=10
s=np.random.lognormal(mu,sigm,n)
slog=np.log(s)
```
## $\mu_L$のみが未知の場合
```python
mu=slog.mean()
pas.SetParam(param='n',val=10)
pas.SetParam(param='mu',val=mu)
pas.SetParam(param='sigm',val=sigm)
Abasis=np.exp(pas.Abasis2())
Bbasis=np.exp(pas.Bbasis2())
print('Absis=',Abasis,':Bbasis=',Bbasis)
#Absis= 291.2633407447084 :Bbasis= 321.76051705594955
```
## $\mu_L$,$\sigma_L$ともに未知の場合
```python
sigm=slog.std()
mu=slog.mean()
pas.SetParam(param='n',val=10)
pas.SetParam(param='mu',val=mu)
pas.SetParam(param='sigm',val=sigm)
Abasis=np.exp(pas.Abasis())
Bbasis=np.exp(pas.Bbasis())
print('Absis=',Abasis,':Bbasis=',Bbasis)
# Absis= 246.67391288011243 :Bbasis= 294.9455976839429
```

# 二母数ワイブル分布
母集団の強度Sが二母数ワイブル分布に従い、形状母数$\alpha$が既知とする。まず、形状母数$\alpha$，尺度母数形状母数$\beta$である二母数ワイブル分布に従うサンプルデータ10点を下記により発生する。A許容値、B許容値の評価プログラムを示す。
```python
import numpy as np
### 例題 ###
#### 二母数ワイブル分布の乱数発生 ###
#alpha 形状母数　beta 尺度母数 n 個数
alpha=8.0
beta=400.0
n=10
dd=np.random.weibull(alpha,n)*beta
```

```python
# 形状母数は既知であるとして、A許容値、B許容値を評価する #
pas=Astress.PAStressW()
pas.SetData(dd,alpha)
print('Abasis=',pas.Abasis(),'Bbasis=',pas.Bbasis())
#Abasis= 205.56913798387714 Bbasis= 275.7528938820833
```

