[TOC]

# クラス解説(継承/多態性)

ここでは継承機能の説明を行いますが、表面的に解釈して

- クラスの機能を継承して機能を拡張出来る

**機能とだけ**解釈するというのは、是非**初心者に通って欲しい**気がします。そこからデザインパターンに触れて、**継承と多態性を使った再利用性を考慮した設計について考える**というのは、ソフトウェア技術者の成長の過程として凄くスッキリしている感じがします

## 継承

真面目に説明しだすと、それこそオブジェクト設計の勘所を避けて通れないので、取り敢えず軽くいきます(目標はC++のコードを読めるようになる事なので)では早速コードを示します

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

クラスに親子関係があり、親から変数や関数を受け継いでいるのが分かるでしょうか。

デストラクタにはvirtualなるキーワードが付いてますが、これは親クラスの関数を子クラスの関数で上書き(override)する為に使用します。**継承関係を用いる場合のデストラクタはvirtualを付けて仮想関数としておく**ものと覚えておけば良いと思います。

## 多態性(ポリモーフィズム)

先ずは、そのままのコードを書いてみます。いちいちpublic指定するのが面倒なので構造体にしていますが、classにしてpublic:を挟んでも一緒です

```cpp
struct Parents{
    virtual void func() { puts( "Base::func()" ); }
};

struct child0 : public Parents{
    virtual void func() { puts( "child0::func()" ); }
};

struct child1 : public Parents{
    virtual void func() { puts( "child1::func()" ); }
};

int main()
{
    Parents *p = new Parents();
    p->func(); // Base::func()
    delete p;

    p = new child0();
    p->func(); // child0::func()
    delete p;

    p = new child1();
    p->func(); // child1::func()
    delete p;
}
```

言語機能をそのまま書き下すのであれば

- 親のポインタで子クラスのオブジェクトを指す出来る
- virtual指定された関数は仮想関数と呼ばれ、**関数呼び出し時に実体のオブジェクトに合わせた関数への呼び出しに置き換わる**これが多態性(ポリモーフィズム)と呼ぶ
- 親クラスの関数がvirtual xx() = 0となっているのは、純粋仮想関数という実際の効能が無いもの。
  - これが含まれる関数は抽象クラスと呼ばれ、オブジェクト生成が出来ない
  - ただしポインタは作る事が出来るので、多態性を利用するのに使用出来る

という機能です。機能だけ書かれてもサッパリですね

### 少し意味のあるサンプル

という訳で、用途が分かるサンプルを書いてみます。以下のような構成になっており、**複数タイプのデータベースへのアクセス方法がIDataBaseによって定義**され、**使う側はあくまでIDataBaseにある関数のみを使う事で、異なるタイプのデータベースアクセス可能**となります

- IDataBaseは、DBクラスへのアクセス方法を定義する為のインターフェースを示す
- Hoge_DBは、IDataBaseで定義された操作方法を持つ何らかのデータベース
- Fuga_DBは、Hoge_DBと同等の操作を持つが、Hoge_DBとは別のタイプのデータベース

```cpp
#include <cstdio>

class IDataBase{
public:
    virtual void Open() = 0;
    virtual void Close() = 0;
    virtual void Access() = 0;
};

class Hoge_DB : public IDataBase{
public:
    virtual void Open() { puts( "Hoge_DBをオープンします" ); }
    virtual void Close() { puts( "Hoge_DBをクローズします" ); }
    virtual void Access() { puts( "Hoge_DBへアクセスします" ); }
};

class Fuga_DB : public IDataBase{
public:
    virtual void Open() { puts( "Fuga_DBをオープンします" ); }
    virtual void Close() { puts( "Fuga_DBをクローズします" ); }
    virtual void Access() { puts( "Fuga_DBへアクセスします" ); }
};

int main()
{
    IDataBase *pDB = new Hoge_DB();
    pDB->Open();    // Hoge_DBをオープンします
    pDB->Access();  // Hoge_DBへアクセスします
    pDB->Close();   // Hoge_DBをクローズします
    delete pDB;

    pDB = new Fuga_DB();
    pDB->Open();    // Fuga_DBをオープンします
    pDB->Access();  // Fuga_DBへアクセスします
    pDB->Close();   // Fuga_DBをクローズします
    delete pDB;
}
```

上の方で少し触れたデザインパターンにある**Template Method パターン**っぽくしてみました