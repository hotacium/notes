
# Python でデータ分析の課題が出たときに見るメモ

大学でもデータ分析の課題が出るが、いちいち調べるのは面倒なので、参考になるサイトなどを記録しておく


## 描画: `seaborn`
[seaborn](seaborn.pydata.org)

以前まで `pyplot` をつかっていたが、複雑さに幾度となく地獄を見ていた. `seaborn` はシンプルな API とドキュメントで、非常に書きやすい. 

どのようなグラフを書くべきかに悩んだ場合は、[Gallery](http://seaborn.pydata.org/examples) を見て真似すればよい.


#### 画像の保存
```python
import seaborn as sns
import matplotlib.pyplot as plt


sns.barplot(data)

// save image
plt.savefig('filename.png') 
```

## データの加工: `Numpy`
[Numpy - Documentation](https://numpy.org/doc/stable/)   

いろいろできる


#### CSV の読み込み
```python
import numpy as np

csv = np.genfromtxt("test.csv", delimiter=",")
```


