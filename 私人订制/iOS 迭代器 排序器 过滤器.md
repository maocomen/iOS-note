## 迭代器

> 迭代的概念：迭代是重复反馈过程的活动，其目的通常是为了逼近所需目标或结果。每一次对过程的重复称为一次“迭代”，而每一次迭代得到的结果会作为下一次迭代的初始值

#### iOS 中的几种迭代器

* for

  ```objective-c
  for (int i = 0; i<[array count]; i++) 
  {
  }
  ```

* NSEnumerator

  ```objective-c
  NSEnumerator *enumerator = [array objectEnumerator];
  id value = nil;
  while ((value = enumerator.nextObject)) 
  {
  }
  ```

* enumerate

  ```objective-c
  [array enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {       
  }];
  ```

* (for in) 快速枚举

  ```objective-c
  for (id obj in array)
  {
  }
  ```

遍历平常我们会用的很多，下面这两个方法也经常会配套使用：

* ```objective-c
  - (NSUInteger)indexOfObject:(ObjectType)anObject;
  //是个比较耗时的操作，不推荐在迭代器中使用。
  ```

* ```objective-c
  - (ObjectType)objectAtIndex:(NSUInteger)index;
  //往往搭配第一种迭代器使用，效率还OK
  ```

##### 几种迭代器的优劣

* for 迭代器

  迭代中只能获取到下标 index ，如果想要获取到下标对应的 value ，需要搭配 objectAtIndex 操作。

* NSEnumerator 迭代器 

  迭代中只能获取到 value ，如果需要取下标，有两种方法

  * indexOfObject 比较耗时，如果迭代数据量太大，会造成cpu占用率过高，耗时长，不建议使用。
  * 搭配 count++ 迭代，耗时短，只是做一个数字累加工作，耗时短。

* 快速枚举 迭代器

  迭代中只能获取到 value ，如果需要取下标，有两种方法

  * indexOfObject 比较耗时，如果迭代数据量太大，会造成cpu占用率过高，耗时长，不建议使用。
  * 搭配 count++ 迭代，耗时短，只是做一个数字累加工作，耗时短。

* enumerate 迭代器

  迭代中可以获取到 value ，也可以获取到下标 index 。

##### 总结

* 如果只有单一需求的话 ，快速枚举的效率 > for > enumerate > NSEnumerator
* 如果需要获取 value 和 index ，快速枚举 和 NSEnumerator 采用第二种方案，快速枚举的效率 > enumerate > NSEnumerator > for
* 如果需要获取 value he index ，快速枚举 和 NSEnumerator 采用第一种方案，enumerate 的效率 > for > 快速枚举 ? NSEnumerator