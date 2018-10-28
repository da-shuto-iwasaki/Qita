
# 仮想環境構築

***

## ◯Anacondaのインストール

<b>　[Anaconda](https://www.anaconda.com)は、Pythonと、様々なライブラリを収録したパッケージである。Numpy や scipy などの科学計算向けのライブラリが多数収録されているため、Python環境を簡単に構築することができる。そこで、まずはAnacondaをダウンロードする。</b>

<b>　ホームページの上部「Product」から「Anaconda Python/R Distribution」を選択し、書いてある指示に従い、自分の環境に合わせた installer をダウンロードする。</b>

![Anaconda1.png](attachment:Anaconda1.png)

![Anaconda2.png](attachment:Anaconda2.png)

<b>　インストールが終了したら、Anaconda Navigator を開く。Anaconda Navigator では、異なる実行環境を作成することができるため、プロジェクトに合わせてpythonやその他のパッケージのバージョンが異なる環境を用意することができる。</b>

<b>　まずは環境を構築する。Environment を選択した後 Create を押し、Python のバージョンを選んでOKを押すと、基本的なパッケージが自動的にインストールされる。</b>

![Anaconda%20Navigator.png](attachment:Anaconda%20Navigator.png)

![Anaconda%20Navigator2.png](attachment:Anaconda%20Navigator2.png)

![Anaconda%20Navigator3.png](attachment:Anaconda%20Navigator3.png)

<b>　環境が作成できたので、「Open Tarminal」からコマンドプロンプトを開く。これによって、その環境が有効にされた状態でコマンドプロンプトを開くことができる。あとは、その他必要なパッケージを各種インストールする（pipはすでに入っている。）ことで、環境構築は終了である。</b>

***

## ◯仮想環境を有効化するには

<b>　以上で環境構築と有効化の仕方がわかったが、毎回 Anaconda Navigator を開くのは面倒である。そこで、コマンドプロンプトから仮想環境を有効化する方法をまとめる。<br>　まずは、以下のコマンドで自分の作成した仮想環境を表示する。</b>

<b>`$ conda info --envs`</b>

<b>　次に、以下のコマンドを打つことで、選択した仮想環境を有効化する。なお、この時macOS/Linux では、`sorce` を前につける必要がある。</b>

<b>`$sorce activate 仮想環境の名前 # macOS/Linux`</b><br>
<b>`$activate 仮想環境の名前       # Windows`</b>

![virtual%20env.png](attachment:virtual%20env.png)
