# CMake環境構築手順

<!-- omit in toc -->
## はじめに
本書では、CMakeを利用して複数のC/C++ファイルから成るプロジェクトをビルドする方法を記す。  

<!-- omit in toc -->
## 目次
- [実行環境](#実行環境)
- [環境の準備](#環境の準備)
  - [Cmakeのインストール](#cmakeのインストール)
  - [VSCodeの拡張機能](#vscodeの拡張機能)
- [Sample Projectのビルド](#sample-projectのビルド)
  - [ワークスペースの準備](#ワークスペースの準備)
  - [キットの選択](#キットの選択)
  - [ビルド](#ビルド)
  - [デバッグ](#デバッグ)
    - [デバッグのみ実行する場合](#デバッグのみ実行する場合)
  - [リリース版のビルド](#リリース版のビルド)


## 実行環境
- Windows11(64ビット)
- GCC（MinGW）15.1.0 ←「[GCC(WinMG)環境手順書](../GCC(MinGW)環境構築手順書/GCC(MinGW)環境構築手順書.md)」参照
- Cmake 4.1.0

## 環境の準備

### Cmakeのインストール
1. 以下のリンクからCmakeのWindows x64 Installer（cmake-4.1.0-windows-x86_64.msi）をダウンロードする。  
https://cmake.org/download/  

2. ダウンロードしたmsiファイルを実行する。  

3. インストーラーが立ち上がるので画面の指示に従ってインストールする。  

4. 以下のコマンドを実行しインストールできたか確認する。  
~~~
cmake --version  
~~~  

5. VSCode上でパスを設定する  
- VSCodeの「設定」画面を開く（Ctrl + ,）。
- 検索バーに「CMake Path」と入力。
- CMake: Cmake Path に CMake の実行ファイルのフルパスを入力：  
~~~  
C:\Program Files\CMake\bin\cmake.exe  
~~~  

### VSCodeの拡張機能
- CMake Tools v1.21.36
- C/C++ v1.26.3
- CMake Language Support v0.0.9
※ CMake Tools と C/C++ はC/C++ Extension Packに含まれている。  

## Sample Projectのビルド
本章では以下のサイトを参考にしてSample Projectをビルドする。  
https://izadori.net/vscode-cmake-cpp-build/

### ワークスペースの準備
VSCodeで新しいウィンドウを開き、「ファイル」タブから「フォルダーをワークスペースに追加」をクリック。以下のフォルダ及び配下のファイルを作成する。  
-- cmake_test  
 |- CMakeLists.txt  
 |- main.cpp  
 |- sub.cpp  
 |- sub.h  

main.cpp
~~~ 
#include <iostream>  

#include "sub.h"  

int main(int argc, char * argv[])  
{  
    sub_test();  

    return 0;  
}  
~~~ 

sub.h
~~~ 
#ifndef SUB_H  
#define SUB_H  

void sub_test();  

#endif  
~~~  

sub.cpp
~~~
#include "sub.h"  

#include <iostream>  

void sub_test()  
{  
    std::cout << "Hello, world!" << std::endl;  
}  
~~~  

CMakeLists.txt
~~~  
cmake_minimum_required(VERSION 3.8)  
project(main CXX)  

add_executable(main main.cpp sub.cpp)  
~~~  

### キットの選択
ビルドに用いるコンパイラを選択する。  
Ctrl + Shift + P を押してコマンドパレットを開き、「Cmake:構成」を選択する。  
選択可能なキットの一覧が表示されるので、利用するコンパイラを選択する。  
今回は「GCC(MinGW)環境構築手順」で構築したGCCを用いるので「GCC 15.1.0 x86_64-w64-mingw32」を選択する。

### ビルド
Cmakeのアクティビティバーを開き、「ビルド」タブの右側に表示された「ビルド」のアイコンをクリックする。
ビルドに成功すると、cmake_test/buildにmain.exeが作成される。  

### デバッグ
Cmakeのアクティビティバーを開き、「デバッグ」タブの右側に表示された「デバッグ」のアイコンをクリックする。  
ソースファイル側でブレークポイントを設定しておくとそこでプログラムが一時停止される。  

#### デバッグのみ実行する場合
上記のやり方だと、毎回ビルド＋デバッグを行ってしまう。よってここではデバッグのみ実行するやり方を記す。  
1. アクティビティバーの「実行とデバッグ」を開く。  
2. 「実行とデバッグをカスタマイズするには、launch.jsonファイルを作成します。」の青字部分をクリックする。  
3. launch.jsonをどこに作成するか聞いてくるので、ルートフォルダ(cmake_test)を選択する。  
4. cmake_test/.vscode/launch.jsonが作成されるので、これを開く。すると、エディタの右下に「構成の追加」というボタンが出てくるので、これをクリックする。  
5. 「{} C/C++: (gdb) 起動」を選択する。  
6. 選択すると、ひな型が挿入されるので、実行ファイルとgdbへのパスを以下のように変更する。  
launch.json
~~~  
'''  
            "program": "${workspaceRoot}/build/main.exe",  
...  
            "miDebuggerPath": "gdb",  
~~~  
7. jsonファイルを上書き保存すると、デバッグモードの一番上に「(gdb) 起動 (cmake_test)」というボタンが表示されるので、これをクリックするとデバッガが立ち上がる。  

### リリース版のビルド
リリース版をビルドする際は、Cmakeのアクティビティバーの中にある「構成」タブのバリアントの設定（現在は「Debug」となっている）部分の「バリアントの選択」（鉛筆マーク）をクリックし「Release」を選択する。  
この状態でビルドすると、リリース版でビルドできる。  


