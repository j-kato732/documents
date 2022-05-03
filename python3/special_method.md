# 特殊メソッド
pythonには特殊メソッドなるもの（__hoge__的なもの）が存在する。
特殊メソッドを実装することで、特定の処理を行なった時に自動的に呼び出される処理を記述できる。

よく使われる特殊メソッドは`__init__'。
initirizeの意味を持つ特殊メソッドで、クラスがインスタンス化された時に自動的に呼び出される。

いくつかの特殊メソッドを紹介する。

# __repr__

これはオブジェクトがprint関数の引数となった場合に、objectから継承した
__repr__メソッドを呼び出し、__repr__が返した値を出力する。

すべてのオブジェクトはobjectを継承するので、`__repr__`メソッドをオーバーライドすることで
printで呼び出された時の処理を実装できる。

```python
class Car:
    def __init__(self, name):
        self.name = name

c1 = Car("kato")
print(c1)
```

```
<__main__.Car object at 0x7fbd18d44a00>
```

のように出力されるところを、__repr__メソッドを追加すると処理を変更できる。

```python
class Car:
    def __init__(self, name):
        self.name = name
    
    def __repr__(self):
        return self.name

c1 = Car("kato")
print(c1)
```

```
kato
```