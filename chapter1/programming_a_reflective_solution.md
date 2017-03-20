# 1.2.2 实现反射方案

为了解决团队的难题，George 编写了一个静态工具方法 `setObjectColor`，如列表所示。现在只需要给 `setObjectColor` 传递一个组件对象和颜色即可实现调用该对象的 `setColor` 方法。

```
public static void setObjectColor(Object obj, Color color) {
	Class cls = obj.getClass();

	try {
		Method method = cls.getMethod("setColor", new Class[]{ Color.class });

		method.invoke(obj, new Object[]{ color });
	} catch () {

	}
}
```

`setObjectColor` 方法可以在不了解组件具体类型情况下调用组件的 `setColor` 方法，该方案没有入侵任何组件的源码，也避免了代码膨胀、内存膨胀，以及不必要的耦合，非常灵活有效。

下面两行代码使用反射检查参数 `obj` 的结构：

```
// 查询对象 obj 的类型
Class cls = obj.getClass();
// 查询一个名字为 setColor，参数为 Color 的方法的 Method 类型
Method method = cls.getMethod("setColor", new Class[]{ Color.class });
```

综合来看，上面两行完成了第一个任务：找到一个可以调用的 `setColor` 方法。

上面两个查询都是某种形式的自省（introspection），自省是指一种可以让程序检查自身的反射特性，我们讲 `setObjectColor` 方法在参数 `obj` 上进行自省。

下面这行改变了程序的行为：

```
method.invoke(obj, new Object[] { color });
```
这行代码在 `obj` 对象上使用 `color` 作为参数调用 `method(setColor)` 方法，这种反射调用又被称为动态调用。动态调用是指程序可以在运行时调用方法，而不必在编译时指定所调用的方法。

在上面的例子中，George 在编写 `setObjectColor` 的时候并不知道最终会调用哪个 `setColor` 方法，因为他根本不知道参数 `obj` 的类型，事实上，`setObjectColor` 在运行时通过自省发现调用的是哪个 `setColor` 方法。动态调用让该程序可以根据运行时自省的结果做出不同行为。

并非所有类都支持 `setColor` 方法，静态调用 `setColor` 时，如果对象不支持 `setColor` 方法，编译器会立即报错，而使用自省（动态调用）时，只有在运行时才知道 `setColor` 方法是否可用。

如果参数 `obj` 的类型不支持 `setColor` 方法，将抛出 `NoSuchMethodException` 异常。使用内省的代码都要处理此类异常。虽然团队保证所有组件都支持 `setColor` 方法，但是如果 `obj` 的类型不支持，则传入的是非法参数，需要处理。



