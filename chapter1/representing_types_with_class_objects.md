# 1.5 通过类对象表示类型

Java 反射使用 `Class` 类的实例表示 **类型**，例如 `getMethod(String name, Class[] paramTypes)` 方法，使用一个 `Class` 数组表示 `name` 方法接受的参数类型列表。

当参数是对象时没有问题，但如果参数 **不是对象** 呢？

```
public class Vector ... {
	public synchronized boolean addAll( Collection c ) ...
	public synchronized void    copyInto( Object[] anArray ) ...
	public synchronized Object  get( int index ) ...
}
```
上面是 `java.lang.Vector` 类中的片段，其中的三个方法分别具有不同类型的参数：

* **接口** `Collection`
* **数组** `Object[]`
* **原始类型** `int`

要使用反射编程，就必须知道在这类方法上如何使用自省。

对于原始类型、数组、接口，Java 通过引入 `Class` 对象（`Class` 对象就是普通类）来表示，这些 `Class` 对象与普通 `Class` 对象相比有很多 **局限性***，比如无法创建原始类型、接口的实例，但它们对于自省而言非常重要，`Class` 中支持 **类型表示** 的方法如下表所示：

| 方法 | 说明 |
| --- | --- |
| String getName() | 返回 `Class` 对象的 **全限定名** |
| Class getComponentType() | 若调用方法的 `Class` 对象代表数组，则返回 **数组元素** 的类型 |
| boolean isArray() | 如果调用方法的 `Class` 对象代表数组，则为 `true` |
| boolean isInterface() | 如果调用方法的 `Class` 对象代表接口，则为 `true` |
| boolean isPrimitive() | 如果调用方法的 `Class` 对象代表原始类型，则为 `true` |

下面分别介绍 Java 如何使用 `Class` 对象表示原始类型、数组和接口。