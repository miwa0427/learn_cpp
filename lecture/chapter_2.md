[TOC]

# クラス解説(関数/アクセス/初期化/終了)

C++誕生の動機はクラスをC言語に取り込む事にあったと言っても過言ではありません。

ここまでの内容にあった通り、クラス定義コードはC言語の構造体に似ています。しかし、それだけでは**言語仕様の総合デパート(私が勝手に付けました)**と呼ばれるクラスのほんの1割も紹介出来ていません。先ずは、C言語をベースとした場合、分かりやすい機能拡張部分を３点ほど紹介しておきます

## 関数を持てる

C言語には変数しか持たせる事が出来ませんでしたが、C++では関数を持たせる事が出来るようになりました。ただ、C言語でも構造体へ関数ポインタを持たせれば似せられる事が出来るので並べて紹介してみます

```c
// C言語版
typedef void ( *func_t )( void );
void say_hello( void )
{
    puts( "hello" );
}
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
    void say_hello( void )
    {
        puts( "hello" );
    }
};

int main()
{
    Hoge_T hoge;
    hoge.a = 100;
    printf( "hoge.a is %d\n", hoge.a ); // hoge.a is 100
    hoge.say_hello(); // Hoge_T::say_hello()が呼ばれ"hello"が出力される
}
```

コードがスッキリしてC++っぽさを感じられたが出たでしょうか？

## アクセシビリティ(アクセス制限)

プログラミング言語にはスコープという概念により、思いがけない形で変数が変更される事を防ぐ機構が備わっています。C言語で言えば以下のような言語機能がこれに該当します

- 括弧({})によるローカルスコープとグローバルスコープ
- static宣言による変数・関数使用箇所の局所化

C++としての機能としては、後のチャプターで触れる**名前空間**もこれに類するものですが、どちらかと言えば「名前の衝突を防ぐ」のが主題の機能のように思います。では、クラスにおけるアクセシビリティ制御の例を見てみましょう

```cpp
#include <cstdio>

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
    void CallInnerFunc( void )
    {
        inner_func();
    }
};

int main()
{
    Hoge h;
    
    h.SetValue( 100 );
    printf( "[before] h.inner_value:%d\n", h.GetValue() );
    h.CallInnerFunc(); // Hoge::inner_func()を間接的に呼ぶ
    printf( "[after]  h.inner_value:%d\n", h.GetValue() );
}
```

アクセス制御で使用されるprivate/protected/publicセクションは、続く変数及び関数定義に対して**どういうアクセス制限を持たせるか？**を指定する事が出来ます。説明の順序の関係でprotectedの説明に必要な情報が揃っていませんが、アクセス制限の本質は非常に奥が深いのであまり気にしないで下さい

- private：クラス内の関数からしかアクセス出来ない
- public：何処からでもアクセス可能
- protected：親子関係にあるクラスからのみアクセス可能

## コンストラクタ(初期化)とデストラクタ(終了処理)

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

### コンストラクタ初期化子

クラスの初期化にはコンストラクタを使用します。これは以下の二通りの書き方が出来ます

```cpp
// 関数定義の中で代入する
class Hoge(){
    int inner_val_;
public:
    Hoge( int set_val )
    {
        inner_val_ = set_val;
    }
}

// 初期化子リストを使う
class Hoge(){
    int inner_val_;
public:
    Hoge( int set_val ) : inner_val_( set_val )
       // 初期化リスト( : xxx(), yyy() )により、関数ブロックに先立って
       // inner_val_ = set_val相当の処理が実行される
    {}
}
```

見易さ及び他の言語と似ているのは前者ですが、C++ではよっぽどの理由が無い限り後者を使うのが正解です。理由は「Effective C++ Scott Meyers」に詳しいですが、多くの場合コストが理由になります。コンストラクタを二回呼んでおいて**見易さが大事だから**というのは、少なくとも組み込みの世界では話になりません