# 様々な言語でのOOP表現

本ページは、幾つかのプログラミング言語でのオブジェクト指向表現を見てみます。

ただ、最近触っていない言語ばっかりなので、少しずつ資料を漁りながら完成度を上げていきます。今のところ、スカスカなのはどうかご容赦下さい。

## C#

C++をマイクロソフト(に入社したAnders Hejlsberg)流に設計しなおした言語、というのが私のイメージです。

- C言語由来の文法周りを丁寧に見直したのでとても読みやすい
- 悪名高きマクロを撤廃したのでIntelliSenseが素晴らしい働きをする
- C++以上にしっかりと型チェックが機能する

という特徴により、コンパイラから指摘されるwarning/errorを解消するだけで、思う通りの動作をしてくれるので生産性が極めて高い印象があります。ぶっちゃけ**オブジェクト指向を学ぶのであれば、C++ではなくC#で触る方がよっぽどあるべき設計に触れる事が出来る**と思います

```C#
using System;

namespace ConsoleApp1
{
    class Person
    {
        public string name_ = "";
        public int age_ = 0;
        public Person(string name, int age)
        {
            name_ = name;
            age_ = age;
        }
    }
    class Program
    {
        static void Main(string[] args)
        {
            var person = new Person("mike", 20 );
            Console.WriteLine("person name:{0} age:{1}", person.name_, person.age_);
        }
    }
}

```



## smalltalk

オブジェクト指向が広く用いられる原動力となったsmalltalk。オブジェクト指向に関する著名人マーティン・ファウラーは、かつての自身へのインタビューで「一番好きなプログラミング言語」と答えられたそうです。

実のところ、この言語を本格的に学んだ事が無いので資料探しから始めます

## ruby

**rubyは全てがオブジェクトから成る**というのが私の入りでした。

なので、オブジェクト指向の機能としては十分すぎる程備わっている言語なのですが、私自身がワンライナー的な使い方しかやってこなかった為、オブジェクト指向表現はあまり記憶していないというのが本音です。

```
class Student
  def initialize( name ) # コンストラクタ
    @name = name
  end
  
  def get_name
    @name
  end
end

student = Student.net( "mike" )
p student.get_name # >> mike
```



## Common Lisp

Lisp処理系としては最も正統なものでしょうか。とはいえ、LISPの持つS式と強力なマクロによりschemeやらClojureやらの親戚(と言うと原理主義の人が嫌がりそうですが)があります。著名なLISPerであるポールグレアムの著を読むと、LISPにとって何らかの言語表現を取り込む事は日常的な事のようです。

```commonlisp
(defclass city ()
     (id
      name
      country-code
      district
      population))

(make-instance 'city)
(defparameter *city* (make-instance 'city))
```



