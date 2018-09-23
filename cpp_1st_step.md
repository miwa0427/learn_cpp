[TOC]

# C++事始め

すいません、準備中です m(_ _)m

## 本ページで触れる内容

言語要素としては以下のような内容について触れる予定です

- 型システム
  - 基本型(charやintやらの値型と呼ばれるもの)
  - 構造体・クラス型
- CからC++へ
  - クラスとは何なのか？
  - それが使えると何が嬉しいのか？

また、C++としては非常に強力かつ重要な要素なのですが、本ページでは敢えて触れないでおこうと考えているものがあります

- テンプレート
  - すなわちSTLは除外

    - 初学者がSTLを学ぶとエラーメッセージに困惑する事になります。例えば、以下のような初心者にありがちなコーディングミス

      ```cpp
      std::string s = hello world; // 文字列がダブルクォーテーション(")で括られていない!!
      ```

    - これに対するエラーメッセージはこうなります

      - E0415 "int" から "std::basic_string<char, std::char_traits<char>, std::allocator<char>>" に変換するための適切なコンストラクターが存在しません
      - C2440 '初期化中': 'int' から 'std::basic_string<char,std::char_traits<char>,std::allocator<char>>' に変換できません

  - リソース制約のある環境を扱うものとしています

    - [c++ テンプレート サイズ 肥大]辺りでググると解説があります
    - C++の応用範囲を著しく拡げてしまう為、先ずは蓋をしておきたい

- 例外
  - **例外をどういうポリシーで扱うか**？という設計の選択が発生します

    - 例えば前橋和弥氏が翻訳された[例外に関する議論例](http://d.hatena.ne.jp/kmaebashi/20100101/p1)辺りを見ると、熟練者であっても見解が統一されておらず、初学者にどう説明すべきか決められませんでした

  - リソースに余裕がなくRAIIが正しく活用されていない開発プロジェクトという想定の為

    - メモリリークが発生しうるサンプルです。
      - try/catchで例外を捕捉するか、一行目をコメント行のように置き換える必要があります

    ```cpp
    CHoge *h = new CHoge(); // std::shared_ptr< CHoge > h( new CHoge() );
    
    raise_exception_func(); // 例外が発生しうる関数
    
    delete h; // raise_exception_func()が例外を投げてしまうと、ここが実行されない
        
    ```

# C++のコードを見てみる

## 最初のコード。そして断念

**C++らしい入門コード**といえば、以下のようなものでしょうか

```cpp
#include <iostream>

int main( void )
{
    std::cout << "hello world!!" << std::endl;    
}
```

想定読者は**C言語をかじった人**なので、上のコードに含まれるC++っぽい部分に触れてみます。

- iostreamヘッダは、C++標準ライブラリiostreamの定義が入っている
- main()関数の返り値を省略する事が出来る(その場合return 0相当になる)
- std::coutは**「std名前空間(namespace)にあるcoutというオブジェクト」**
- <<は、演算子''<<''をオーバーロードしたもの

思った以上にC++っぽい要素が満載でした。 orz...

マトモな解説書では、詳細をボヤカしたり、真面目に説明したりするのでしょうが、

「取り敢えずC++言語をそれっぽく読めるようになればいい」

事が当面の目標なので、丸々スルーしてしまう事にします

## 少しハードルを下げた入門コード

気を取り直して、C++の入門コードを書いてみます

```cpp
#include <cstdio>

int main( void )
{
    printf( "hello world!!" );    
}
```

ほぼC言語のコードですね。「<stdio.h>ではなく<cstdio>」となっている点だけが異なります

## C言語からC++になった経緯

C言語によって幾つものUnix系OSが開発され、システムがリッチになるにつけ

- Lispみたいに潤沢なマシンパワーを利用してもいいんじゃない？
- smalltalkって言語で**オブジェクト指向**ってのが流行っているみたい

という時代の流れが生まれて、それをC言語にも取り込もうという流れが生まれました。C++設計者がこの辺りの話を書いてくれているので、

「C++の設計と進化」Bjarne Stroustrup

をご覧頂くのがお勧めです（大好きな書籍の一つです）

### はじめてのオブジェクト

では、早速オブジェクトを作ってみましょう。

```cpp
struct Hoge{
  int a;  
};
Hoge hoge;
```

すいません、わざと構造体で書いてみました。とはいえ、普通だとstruct Hogeの所を

```cpp
class Hoge{
public:
  int a;  
};
Hoge hoge;
```

としているだけなので敢えてこうしてみました。ご存知の方も多いと思いますが、

「構造体とクラスは、デフォルトのアクセシビリティがpublicかprivateの違いしかない」

ので、**C言語から移行される方に新しいキーワードなしでオブジェクトを示す**ならこれもアリかな、と

### クラス？オブジェクト？

C++を象徴するキーワードの「クラス」「オブジェクト」…この定義については、今まであらゆる説明が為されてきました。なので今まで私が解釈している感覚での説明でいくと

- クラスは定義
- オブジェクトは型定義を元に作られた変数

という風に覚えています。

```cpp
class Hoge{ // Hogeクラスの定義
public:
    int a;
};
Hoge a; // Hogeクラス型のaという変数を作る
```

## クラス基本機能の紹介

C++誕生の動機はクラスをC言語に取り込む事にあったと言っても過言ではありません。上で紹介した通り、クラス定義コードはC言語の構造体に似ています。しかし、それだけでは**言語仕様の総合デパート(私が勝手に付けました)**と呼ばれるクラスのほんの1割も紹介出来ていません。ここでは、初学者に伝えておきたい代表的な３つの要素を紹介しておきます。

### 関数を持てる

C言語には変数しか持たせる事が出来ませんでしたが、C++では関数を持たせる事が出来るようになりました。ただ、C言語でも構造体へ関数ポインタを持たせれば似せられる事が出来るので並べて紹介してみます

```c
// C言語版
typedef void ( *func_t )( void );
void say_hello( void ){ puts( "hello" ); }
typedef struct{
    int a;
    func_t func;
}Hoge_T;

int main()
{
    Hoge_T hoge;
    hoge.a = 100;
    hoge.func = say_hello;
    printf( "hoge.a is %d\n", hoge.a ); // hoge.a is 100
    hoge.func(); // say_hello()が呼ばれ、"hello"が出力される
    return 0;
}
```

```cpp
// C++版
class Hoge_T{
public:
    int a;
    void say_hello( void ){ puts( "hello" ); }
};

int main()
{
    Hoge_T hoge;
    hoge.a = 100;
    printf( "hoge.a is %d\n", hoge.a ); // hoge.a is 100
    hoge.func(); // Hoge_T::say_hello()が呼ばれ"hello"が出力される
}
```

コードがスッキリしてC++っぽさを感じられたが出たでしょうか？

### アクセシビリティ(アクセス制限)

プログラミング言語にはスコープという概念により、思いがけない形で変数が変更される事を防ぐ事が出来ます。C言語で言えば以下のような言語機能がこれに該当します

- 括弧({})によるローカルスコープとグローバルスコープ
- static宣言による変数・関数使用箇所の局所化

C++としての機能としては、後述する**名前空間**もこれに類するものですが、どちらかと言えば「名前の衝突を防ぐ」のが主題の機能のように思います。



では、クラスにおけるアクセシビリティ制御の例を紹介します

```cpp
class Hoge{
private: // 関数の外からはアクセス出来ない変数と関数
    int inner_value_;
    void inner_func()
    {
        inner_value_ *= 2;
    } 

public:
    void SetValue( int val )
    {
        inner_value_ = val;
    }
    int GetValue( void )
    {
        return inner_value_;
    }
    int CallInnerFunc( void )
    {
        inner_func();
    }
};

int main()
{
    Hoge h;
    h.SetValue( 100 );
    printf( "[before] h.inner_value:%d^n", h.GetValue() );
    h.CallInnerFunc(); // Hoge::inner_func()を間接的に呼ぶ
    printf( "[after]  h.inner_value:%d^n", h.GetValue() );
}
```

アクセス制御で使用されるprivate/protected/publicセクションは、続く変数及び関数定義に対して「どういうアクセス制限を持たせるか？」を指定する事が出来ます。説明の順序の関係でprotectedの説明に必要な情報が揃っていませんが、アクセス制限の本質は非常に奥が深いのであまり気にしないで下さい。

- private：クラス内の関数からしかアクセス出来ない
- public：何処からでもアクセス可能
- protected：親子関係にあるクラスからのみアクセス可能

### コンストラクタ(初期化)とデストラクタ(終了処理)

クラスという機能を用いる事で**非常に大きなデータとそれに対する操作**を一まとめにする事が出来るようになります。これはプログラムの本質である**データ構造とアルゴリズムを一つ**に集約する事を意味します

しかし、データ量が大きくなればなるほど、それを適切な形で扱う事が難しくなります。**アルゴリズムはメンバ関数で実装する**と定義すれば、以下のようにデータの変化を記す事が出来ます

1. データを初期化する(**コンストラクタ**)
2. データを操作する(**メンバ関数**でデータを処理する)
3. データの賞味期限が無くなったので解放する(**デストラクタ**)

```cpp
class Hoge{
// クラス定義の場合、public:までは強制的にprivate扱い
    int val_;
    
public:
    Hoge() : val_( 100 )
    {
        printf( "オブジェクトを初期化します\n" );
    }
    ~Hoge()
    {
        printf( "オブジェクトを破棄します\n" );
    }
    int GetValue( void )
    {
        return val_;
    }
};

int main()
{
    Hoge h; // ここでコンストラクタが走る
    printf( "h.val_ is %d^n", h.GetValue() );
}// ここでデストラクタが走る
```

クラスの中で上述のデータの変化を全て包含出来ている事がお分かり頂けたでしょうか



### 継承

継承を真面目に説明しだすと、それこそオブジェクト設計の勘所に触れる事になるので、そういうのは取り敢えず置いておきます。先ずはC++のコードを読める事が目標なので。では早速コードを示しましょう

```cpp
#include <cstdio>

class Base{ // 親クラス(aka.基底クラス)
protected:
    const char* base_;
public:
    Base() : base_( "Base.base_" )
    {
        printf( "Baseオブジェクトが生成されました\n" );
    }
    virtual ~Base()
    {
        printf( "Baseオブジェクトが破棄されました\n" );
    }
    void FuncBase( void )
    {
        printf( "base_ : %s\n", base_ );
    }
};

class Derived : public Base{ // 子クラス(aka.派生クラス)
    const char* derived_;
    // 親から受け継いだbase_も使える
public:
    Derived() : derived_( "Derived.derived_" )
    {
        printf( "Derivedオブジェクトが生成されました\n" );
    }
    virtual ~Derived()
    {
        printf( "Derivedオブジェクトが破棄されました\n" );
    }
    void FuncDerived( void )
    {
        printf( "base_ : %s derived_:%s\n", base_, derived_ );
    }
    // 親から受け継いだFuncBase()も使える
};

int main()
{
    { // Baseクラスの生存期間を持たせる為のスコープ
    	printf( "--- Baseクラス ---\n" );
        Base b;
        b.FuncBase();
    }
    { // Derivedクラスの生存期間を持たせる為のスコープ
    	printf( "--- Derivedクラス ---\n" );
        Derived d;
        d.FuncBase();
        d.FuncDerived();
    }
}
```

クラスに親子関係があり、親から変数や関数を受け継いでいるのが分かるでしょうか。ここまでで説明していないキーワードvirtualなるものがデストラクタ関数に付いています。取り敢えず**継承関係を用いる場合のデストラクタは、取り敢えずvirtualを付けて仮想関数としておく**とでも覚えておいて下さい。

### 多態性(ポリモーフィズム)

準備中

## その他の重要な言語要素

### 関数/演算子オーバーロード

準備中

### 名前空間(namespace)

準備中

# Appendix.色んな言語のオブジェクト指向

様々な言語にオブジェクト指向を表現する機構が備わっています。

色んな言語のオブジェクト指向表現を見る事で以下のようなメリットが得られます

- 共通項を見出す事により、本質が見える
- 言語によって、スポイルされているものがあり、避けるべきものが見つかる

後者の最たるものが**多重継承**ですね。

[other OOP lang](other_oop_lang.md)