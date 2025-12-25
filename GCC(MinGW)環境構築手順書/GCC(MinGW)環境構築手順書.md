# GCC（MinGW）環境構築手順

<!-- omit in toc -->
## はじめに
本書では、GCC（MinGW）のインストール及び初期設定方法を記す。

<!-- omit in toc -->
## 目次
- [実行環境](#実行環境)
- [GCC(MinGW)のインストール](#gccmingwのインストール)

## 実行環境
- Windows11（64ビット）
- GCC（MinGW）v15.1.0

## GCC(MinGW)のインストール
1. 以下のリンクからMinGWの7zipをインストールする。  
https://github.com/niXman/mingw-builds-binaries/releases/download/15.1.0-rt_v12-rev0/x86_64-15.1.0-release-win32-seh-ucrt-rt_v12-rev0.7z

2. ダウンロードした7zipファイルを展開する。  

3. 展開したフォルダの中にある「mingw64」フォルダをCドライブ直下の「Program Files (x86)」フォルダの中に移動する。  
※警告メッセージが出るが「続行」を選択する。  

4. 環境変数を設定する。  
システムの環境変数に以下を追加する。  
- C:\Program Files (x86)\mingw64\bin

5. コマンドプロンプトで以下を実行し正しく設定できたか確認する。
~~~
gcc --version
~~~

6. 参照元  
https://qiita.com/ochx/items/01449d09777187790ee4
