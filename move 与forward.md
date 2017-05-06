# move 与 forward
在日常的使用中， move 和 forward 的情景其实非常容易区分。
forward用来把是一个引用的参数传递出去。这可以是左值引用也可以是右值引用。当然不管是什么引用，这个引用如果不是形式上被声明成右值，那多半都是用错了。还记得因为引用折叠，T&&也可能是左值引用的事情吗？

使用forward的时候，不能忽略它的模板参数。最好的写法是，被forward的参数的类型如果声明为X&&，那么你就要写std::forward<X>。当X是一个非引用类型的时候，forward会把类型为X&&的参数处理成右值引用。如果X是一个左值引用类型的时候，forward会把类型为X&&的参数处理成左值引用。

这也就是为什么这个函数叫forward，因为单词就是向前传递的意思。大家看到这里可能云里雾里，下面我举一个跟之前选择题很像的一个简单明了的例子来解释。注意这里的例子与选择题的题干的区别是，这里多了一个forward。看完之后好好对比一下选择题，会有更深刻的理解。
```javascript
void Print(int& x)
{
  cout << " x is int& " << endl;
}

void Print(int&& x)
{
  cout << " x is int&& " << endl;
}

template<typename T>
void CallPrint(T&& x)
{
  Print(std::forward<T>(x));
}

int main()
{
  int x = 0;
  CallPrint(x);
  CallPrint(0);
  return 0;
}
```
大家会看到， CallPrint 分别被调用了两次。第一次是变量x，这里它充当左值引用的角色。第二次是常数0，这里它充当右值引用的角色。虽然调用的都是同一个 CallPrint，但是由于你传递进去的类型的不同，将会分别调用两个不同的 Print 函数。
CallPrint(x)的类型计算过程是，首先x的类型是int&，但是CallPrint的参数却是T&&。由于类型折叠的关系，想要让 T&& 变成 int& ，那么 T 就只能是 int& &&, 然后 
