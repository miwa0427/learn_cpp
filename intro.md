# はじめに





## 開発環境導入

### Visual Studio Community

#### 導入方法

[Installerへのリンク](https://visualstudio.microsoft.com/ja/)

1. Visual Studio IDE下の[Windows用インストーラー]をクリック

2. [Community 2017]を選んで、インストーラーをダウンロードする

3. インストールするコンポーネントでC++を選ぶ

   オプションが山のようにあるので、初心者が適切な

#### 良い点

- C++開発環境としてこの上ない程にリッチな環境が手に入る

  - 強力なデバッガ
  - インテリセンスによる強力な入力補助
  - C++17に対応済み

- インストーラーから複数の開発言語の導入が出来る


#### 悪い点

- インストーラーが初心者には逆の意味でリッチ過ぎる

  なんとかインストールに成功して、ライブラリを拡張しようとすると[vcpkg](https://docs.microsoft.com/ja-jp/cpp/vcpkg)なるMS標準のVC++向けパッケージマネージャが用意されてはいるのですが、boost(C++の次期標準)一つ導入するのにも苦労する始末(多分、ソース取ってきて自前ビルド出来る人でも無いと正しく導入出来ない。。。)

- 標準プロジェクト設定で付いてくるプリコンパイルドヘッダが初心者には厄介

  - コンパイル時間を短縮する良い仕組みではあるのですが、C++の教科書に載っているコードがビルドエラーにされてしまう

    ```cpp
    // 最初の行に#include <pch.h>が必要
    #include <iostream>
    
    int main()
    {
        std::cout << "hello c++ world!!" << std::end;
    }
    ```


### Cygwin

#### 導入方法

1. [cygwin](https://www.cygwin.com/)のページへ行く
2. "Installing Cygwin"の下にあるダウンロードリンクを自身の環境に合わせてクリック(64bit Windowsならsetup-x86_64.exe、32bitならsetup-x86.exeを選ぶ)
3. cygwin環境を構築したいディレクトリに先ほどダウンロードしたインストーラを置く。
   - 半角スペースは色々と面倒な事になるので、以下のようなディレクトリに置くのがおススメ
     - ex.)cygwin環境ディレクトリ例
       - c:\cygwin
       - c:\devel\cygwin
4. インストーラーを起動しdevelパッケージを選択してインストールする
   - インストールは以下の流れで行うのがおススメです
     - 初回起動時は[Choose A Download Source]で一旦[Download Without Installing]で、ローカルにパッケージを置いておく(この時点ではcygwin環境は何も構築されていない)
     - 二回目では、同じ画面から[Install from Local Package]で一回目にダウンロードしたパッケージを選択してインストール
   - [Choose A Download Site]では、取り敢えず.jpドメインのものを選べばOK
   - [Select Package]では**[View] : Category**を選んでから、下の画面をマウスクリックで以下のように変更して下さい。
     - [Devel Default] -> [Devel  **Install**]
   - Unix練習環境として使いたいなら、ついでに他のパッケージを選ぶと良いでしょう。私の場合、取り敢えず以下のものを入れておきました
     - [Editor] - [Emacs]
     - [shells] - [zsh]
     - [Util] - [tmux]

#### 良い点



#### 悪い点

