## Unity C# 代码规范

### 命名规范

#### 命名方法

- **Pascal命名法：每个单词首字母均大写。**
- **Camel命名法：第一个单词首字母小写，其余单词首字母大写。**

#### 文件夹、文件命名、命名空间

使用英文全拼，都必须遵循Pascal大小写规范。

#### 类、方法名、属性

- **Pascal 命名法；**

- 类名称尽量少用或不用缩写；

- 类命名中不要使用下划线及数字；

- 调用内部成员需要用this关键字修饰，调用基类需要用base关键字修饰；

- 若方法返回的值是bool变量，则方法名一般根据含义以Is/Can/Has/Try作为前缀;

- 若方法修改一个成员变量的值，则方法名一般为Set + 成员变量名;

- 若方法返回一个成员变量的值，则方法名一般为Get + 成员变量名;

- 两个方法之间空一行。

  ```c#
  public class FlightBuilder 
  {
  		public string FlightName { get; set;}
    
    	public void ClearData() { }
    
    	public bool IsEmpty() { }
    
    	public string GetFirstName() { }
    
    	public void SetFirstName(string firstName) { }
  }
  ```

#### 枚举

- 枚举名及枚举值名都使用**Pascal命名法**；

- 不要在枚举名称后跟上 Enum 后缀；

- 不要使用汉字作为枚举值。

  ```c#
  public enum SymbolStyle
  {
  		None,
  }
  ```

#### 接口

- **Pascal命名法**；

- 接口的命名要以“I”开头；

- 接口名称中不要使用下划线及数字。

  ```c#
  public interface ICache { }
  ```

#### 委托(delegate)与事件(Event)

- **Pascal命名法**

  ```c#
  delegate void RaiseEventHandler(string hand);
  
  public event RaiseEventHandler RaiseEvent;
  ```

#### 特性(Attribute)

- **Pascal命名法**。

- 用名词或名词短语+Attribute方式命名特性；

  ```c#
  public class ValidAttribute : Attribute
  {
  }
  ```

#### 异常(Exception)

- **Pascal命名法**

- 用名词或名词短语+Exception方式命名自定义异常。

  ```c#
  public class FlightException : ApplicationException
  {
  }
  ```

#### 私有变量名、方法参数名、局部变量名

- **Camel命名法**

  ```c#
  private string mFirstName; //私有字段名
  public void FindByFirstName(string firstName) {} //方法参数名
  string firstName; //局部变量名
  ```

#### 常量

- 常量的命名单词之间用“_”隔开，所有字母均大写。

  ```c#
  private const float MAX_SPEED = 100.0f;
  ```

#### SerializeField

- SerializeField 序列化字段使用“_” + Pascal命名法。

  ```c#
  [SerializeField] private GameObject _Gun;
  ```



### 编码规范

- **代码缩进使用Tab键实现**

- **声明私有变量时，一行只声明一个变量**

  ```c#
  private string firstName;
  private string lastName;
  ```

- **声明局部变量时，简单变量允许一行声明多个，采用就近原则，即将使用该变量时再声明**

  ```c#
  int i,j,k;
  ```

- **{  } 各占一行对齐**

  ```c#
  public void Example() 
  {
      int i = 1;
    	i ++;
  }
  ```

- **两个方法之间使用一个空行**

  ```c#
  public void ClearData() { }
    
  public bool IsEmpty() { }
  ```

- **方法中有多个参数，","后面应有一个空格**

  ```c#
  public void SetName(string firstName, string lastName) 
  {
  }
  ```

- **二元操作符，除了“.”在其两边各有一个空格**

  ```c#
  a = c + d;
  ```

- **if、while、for等关键词后应有一个空格，如果if、for、while语句内容只有一行，可以不加“{}”，但是必须和if、for、while语句位于同一行**。

  ```c#
  if (a == b) a + b;
  ```

- **for语句表达式子中“;” 后面应有一个空格**

  ```c#
  for (int i = 0; i < 0; i++)
  {    
  }
  ```

- **强制转换后面应有一个空格**

  ```c#
  char c;
  int a = 4;
  c = (char) a;
  ```

### Unity引擎相关

- 禁止通过 new 的方式实例化 MonoBehaviour 的派生类;
- 禁止在 Update/FixedUpdate/LateUpdate/OnGUI 等频繁调用的逻辑中使用协程;
- 尽量不使用 LINQ 表达式；
- **禁止使用 Invoke**("MethodA", 2) 来实现延时调用；
- 禁止逐帧使用 Find/GetComponent 等引擎接口调用，如有此类需求，应该将结果缓存供后续使用；
-  当设置 GameObject 对象之间的父子关系时，如果仅关注逻辑上的层次关系而无需保持子对象的空间方位, 则应该使用SetParent(parent, false) 避免空间转换的性能消耗；
-  代码中需要访问到 Transform 组件的位置数据时，尽可能使用 localPosition 代替对 position 属性的访问；
- **尽可能避免使用协程**，Unity 的协程是使用迭代器实现的会分配堆内存；
- **尽可能避免使用 MeshCollider 组件**， 如有此类需求也应尽可能减少网格碰撞体的面数，或使用 Sphere/Box/Capsule 的组合来代替；
- 禁止在 MonoBehaviour 的派生类中存在被引擎高频调用的空方法，如 Update里没有方法内容就应该删掉；
- 必须使用 **CompareTag** 接口比较 GameObject的Tag；
-  尽可能减少 UnityEngine.Object null 比较；
-  建议合理使用 OnBecameVisible/OnBecameInvisible/OnWillRenderObject 类似的引擎回调以避免不必要的渲染和计算；
- 尽可能不要使用foreach；
- 推荐使用条件属性而不是#if条件编译；
- 推荐使用is 或as操作符而不是强制类型转换。

### 注释规范

##### 代码头部注释

vscode插件：KoroFileHeader

```
/*
 * @Description: 资源调用工具类
 * @Autor: goulong
 * @Date: 2020-10-09 10:53:33
 * @LastEditors: goulong
 * @LastEditTime: 2020-10-09 14:31:41
 */
```

##### 方法注释

采用 /// 形式自动产生XML标签格式的注释。包括方法功能，参数含义，返回内容。

vscode插件：C# XML Documentation Comments

```c#
/// <summary>
/// 加载Audio
/// </summary>
/// <param name = "clipName">音效名称</param>
/// <returns>audio clip</returns>
public static AudioClip GetAudioClip(string clipName)
{
}
```

##### 类变量注释

采用 /// 形式自动产生XML标签格式的注释变量含义。

vscode插件：C# XML Documentation Comments

```c#
/// <summary>
/// 场景的名字
/// </summary>
private string mSceneName;
```

##### 局部变量注释

在变量声明语句的后面注释，与前后行变量声明的注释左对齐，注释与代码间以Tab隔开。

```c#
string firstName;   //姓
string lastName;    //名
```

##### 代码行注释

注释位于代码上行，与代码开始处左对齐，双斜线与注释之间以空格分开。

```c#
//设置场景的名字。
this.mSceneName = sceneName;
```

##### VSCode格式化

- **Mac:**  全局(**Shift + Option + F**)，选中(**Command + K + F**)

