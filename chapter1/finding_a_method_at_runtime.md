# 1.4 在运行时查询方法

前面的例子中，我们给 `setObjectColor` 方法传递了一个类型为 `Object` 的参数 `obj`，该方法只有在了解 `obj` 类型之后才能做自省，所以第一步是查询参数的类型：

```
Class cls = obj.getClass();
```

`getClass` 方法可以在运行时查询对象的类型，该方法经常用在反射程序的最开始，因为很多反射任务都需要查询对象的类型，`getClass` 方法定义在 `java.lang.Object` 对象中，所以任何 Java 对象都可以调用 `getClass` 方法。

`getClass` 方法返回 `java.lang.Class` 对象的一个实例，`Class` 对象的实例是 Java 中的**元对象**，我们使用 **类对象**（`class object`）来称呼 `java.lang.Class` 对象的实例。类对象是 Java 中最重要的元对象，因为所有 Java 程序都是由类组成的。

类对象提供关于类的域、方法、构造器、嵌套类等的元数据，还提供了关于继承层次的信息。

`setObjectColor` 方法发现参数的类型之后，方法通过该类型查询 `setColor` 方法：

```
Method method = cls.getMethod("setColor", new Class[]{ Color.class });
```

第一个参数是要查询的方法名字，第二个参数是**类对象数组**（`an array of class objects`），用来标志 `setColor` 方法的参数类型，本例中，`setColor` 参数为 `Color` 类型。

注意上面没有用 `getClass` 方法获取 `Color` 的类对象，因为 `Color` 只是一个类名，此时只能使用类字面值（`class literals`）的方式获取类对象，从语法上讲，任何 **类名** 后加 `.class` 都会得到一个类对象。

* `getClass` 方法：适用已知对象，获取对象的类对象。
* `.class` 类型字面值：适用于 **仅知道类型名**，获取该类型名的类对象。

`java.lang.Class` 类还有其他方法，用于方法自省，这些方法的签名与返回值如列表 1.1 所示。这些方法都使用一个 `Class` 数组指定所查询方法的参数类型。

| 		方法 		| 		描述		 |
|		 	---		  |		  --- 	 |
| `Method` getMethod(String name, `Class[]` parameterTypes) | 返回表示 `public` 方法的 `Method` 对象，**继承**、**自身声明** 皆可|
| Method[] getMethods() | 返回 `Method` 数组，包含所有 `public` 方法，**继承**、**声明**皆可 |
| Method getDeclaredMethod(String name, `Class[]` parameterTypes) | 返回表示 **自身声明** 的方法的 `Method` 对象 | 
| Method[] getDeclaredMethods() | 返回 Method 数组，表示该类 **声明** 的所有方法，包括 `public`/`private`/`protected`/`package` 方法 |

**注意**

* 若 `getMethod/getDeclaredMethod` 查询的方法并无参数，则可以传递 `null` 作为第二个参数，效果与传递一个空的 `java.lang.Class` 数组相同。
* `getMethod` 只返回 `public` 方法，可以是 **继承** 或者 **自身声明** 的。
* `getDeclaredMethod` 返回所有方法，包括 `public`/`protected`/`private`/`package` 方法，但只能是 **自身声明** 的。

上面四个方法，都可能抛出 `NoSuchMethodException`：

1. 使用 `getDeclatedMethod` 查询 **继承** 自父类的方法。
2. 使用 `getMethod` 查询非 `public` 方法。

获取到 `Method` 对象之后，就可以通过该对象获取方法的信息，甚至调用方法。

