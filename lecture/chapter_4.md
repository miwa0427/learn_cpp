[TOC]

## C++追加要素(オーバーロード)

### 関数オーバーロード

C++では、単一の関数に対して複数の引数タイプのものを定義する事が出来るようになりました。

- 引数のみが異なる関数の定義はOK。戻り値だけが異なる関数はNG

では、早速サンプルを紹介します。

```cpp
#include <cstdio>
#include <string>

void print_massage( void )
{
    printf( "Hello world\n" );
}

void print_massage( const char* name )
{
    printf( "Hello %s\n", name );
}

void print_massage( const std::string& name )
{
    printf( "Hello %s\n", name.c_str() );
}

int main()
{
    print_massage(); // print_message( void )
    print_massage( "mike" ); // print_message( const char* )
    print_massage( std::string( "name" ) ); // print_message( const std::string& )
}

```



### 演算子のオーバーロード

C++では演算子についてもオーバーロードする事が出来ます。C言語においては、ユーザー定義型に対する演算は、ほとんどの場合意味を為しませんでした(思いつく範囲では、規格で定義されているのは代入演算子だけだった気がします)

```c
#include <stdio.h>

typedef struct{
    int a;
    int b;
}Hoge_T;

int main()
{
    Hoge_T hoge0, hoge1;
    hoge0.a = 100;
    hoge0.b = 200;
    hoge1 = hoge0; // hoge0メンバー全てがhoge1へコピーされる
    printf( "hoge0.a:%d hoge0.b:%d\n", hoge0.a, hoge0.b );
    printf( "hoge1.a:%d hoge1.b:%d\n", hoge1.a, hoge1.b );
}
```

C++では、このように任意の算術演算子の挙動を実装する事が出来ます

```cpp
#include <cstdio>

struct Point{
    int x, y;

    Point( int set_x, int set_y )
        : x( set_x ), y( set_y )
    {}
};

Point operator+( const Point& lhs, const Point& rhs )
{
    return Point( lhs.x + rhs.x, lhs.y + rhs.y );
}

int main()
{
    Point pos0( 100, 200 );
    Point pos1( 200, 300 );
    Point pos2 = pos0 + pos1;
    printf( "pos0 x:%d y:%d\n", pos0.x, pos0.y ); // pos0 x:100 y:200
    printf( "pos1 x:%d y:%d\n", pos1.x, pos1.y ); // pos1 x:200 y:300
    printf( "pos2 x:%d y:%d\n", pos2.x, pos2.y ); // pos2 x:300 y:500
}
```