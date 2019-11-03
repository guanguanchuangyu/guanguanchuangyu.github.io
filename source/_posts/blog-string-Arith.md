---
title: 'C#关于字符串转简单运算表达式的相关方法记录'
tags:
  - 'C#'
categories:
  - 唐门暗器
typora-root-url: blog-string-Arith
date: 2019-11-03 01:32:28
---


遇到一个需求，将存储在数据库中的计算表达式字符串，如“(2+4+5)/5*100.0” 转换为可以在代码中执行的计算公式，呀，以前没做过，怎么办？还能怎么办，打开浏览器，输入“C#字符串转运算符计算公式”，然后一条条筛选，

<!--more -->

看看有没有灵感，于是乎开始了今天的话题，`关于字符串转简单运算表达式的相关方法记录`

## 网络搜索结果：

### 通过`COM`引入`JS`,使用`Js`的`Eval`函数

项目引用`COM`中的 `Microsoft Script Control 1.0`

Ps：此处`Microsoft.JScript.Vsa.VsaEngine`用法类似，不做细究

```c#
//项目中添加上命名空间 MSScriptControl
using MSScriptControl;
//省略非关键代码

/// <summary>
/// 通过COM方式调用JS执行字符串得到运算结果
/// </summary>
/// <param name="expression">运算公式字符串</param>
public void GetArithmeticValue(string expression)
{
    //实例化
    ScriptControl script = new ScriptControlClass();
    //设定脚本语言
    script.Language = "JavaScript";
    //通过Eval(string)执行字符串表达式
    var result = script.Eval(expression);
    Console.WriteLine(result);
}
```

引入时，`ScriptControlClass`出现异常提示， "无法嵌入互操作类型 ,请选择合适的接口类型“，可以通过在引用中**选中**该**程序集**，查看**属性**，将嵌入交互改为`False`即可

调用：

<img src="arith_com-js_demo.png" alt="image-20191101230729627" style="zoom:145%;" />

执行结果：

<img src="arith_js_result.png" alt="image-20191101230424701" style="zoom:145%;" />

优势：

相对来说性能比较好，实现简单

缺点：

引入`COM`组件，实现与其他语言的互操作交互，带来不必要的风险（下图为.Net Core 3.0出现异常）：

<img src="arith_js_exception.png" alt="arith_js_exception" style="zoom:145%;" />

### 使用`DataTable`中的`Compute`

```c#
public object GetArithmeticValue(string expression)
{
    //实例化DataTable
    DataTable dt = new DataTable();
    //进行运算
    var result = dt.Compute(expression,"");
    return result;
}
```

调用：

<img src="arith_datatable_demo.png" alt="image-20191101001353223" style="zoom:145%;" />

结果：

<img src="arith_datatable.png" alt="image-20191101001143356" style="zoom:145%;" />

### 使用`SQL`语句中`Select`语句实现

引入`System.Data.SqlClient`空间命名（此处以`SqlServer`为例）

```c#
public object GetArithmeticValue(string expression)
{
    //拼接Sql语句
    string SQL = "SELECT "+expression+" AS RESULT_VALUE";
    //构建sqlserver连接实例
    SqlConnection conn = new SqlConnection("sql连接语句");
    //构建sql指令实例
    SqlCommand cmd = new SqlCommand(SQL, conn);
    //执行sql语句
    object obj = cmd.ExecuteScalar(); //执行SQL. 
    //返回数据结果
    return obj;
}
```

为简化结果，此处仅仅是显示拼接的`Sql`语句

```
SELECT (3+4)/12*1.0 AS RESULT_VALUE
```

数据库执行结果：

<img src="arith_sql_result.png" alt="image-20191101003119075" style="zoom:145%;" />

### 通过` CalcParenthesesExpression `函数（ **后序式计算** ）

引入` GrapeCity.CalcEngine.Expressions`程序集

```c#
public object GetArithmeticValue(string expression)
{
    string result = new CalcParenthesesExpression()
        .CalculateParenthesesExpression(expression);
    return result;
}
```

调用：

**由于没有找到相关的`dll`故此函索仅仅作为网络参考**

执行结果：

**由于没有找到相关的`dll`故此函索仅仅作为网络参考**

### 使用开源计算引擎

#### `CalcEngine`

这是在`CodeProject`中找到的一个开源库，属于应对.NET的一个计算引擎，在`Nuget`中能找到一个相关的发布版本的`dll`包`Zirpl.CalEngine`（仅仅兼容`framewor 4.x`）对`Core`版本需要自己手动下载源码调整后进行编译，自行处理

```c#
using CalcEnginer = CalcEngine.CalcEngine;

public object GetArithmeticValue(string expression)
{
    //创建引擎的实例
    CalcEnginer calcEngine = new CalcEnginer();
    //执行表达式函数
    var result = calcEngine.Evaluate(expression);
    return result;
}
```

调用：

<img src="arith_calc_demo.png" alt="image-20191102122120249" style="zoom:145%;" />

结果：

<img src="arith_calc_result.png" alt="image-20191102121924790" style="zoom:145%;" />

####  ` NCalc `

项目中引入`Nuget`上的`NCalc`包，若为`Core`需要下载源码进行编译，线上程序集仅仅支持`Framework 4.x`

```c#
using NCalc;

public object GetArithmeticValue(string expression)
{
    //构造实例
    Expression method = new Expression(expression);
    //执行函数获取计算结果
    var result = method.Evaluate();
    return result;
}
```

调用：

<img src="arith_ncalc_demo.png" alt="image-20191102174503084" style="zoom:145%;" />

结果：

<img src="arith_ncalc_result.png" alt="image-20191102181945094" style="zoom:145%;" />

###  C#中的`CSharpCodeProvider`

通过查阅微软官方文档可以知道，该 `CSharpCodeProvider`类来源于` System.CodeDom.dll`，`Core/Framework 4.6+`可通过`Nuget`进行下载引用，需要注意的是其中部分类型`Core`不支持

```c#
using Microsoft.CSharp;//需要注意对应的命名空间
public object GetArithmeticValue(string expression)
{
    object result = null;
    //构建实例
    CSharpCodeProvider codeProvider = new CSharpCodeProvider();
    //构建添加有system.dll程序集引用的编译器实例
    CompilerParameters parameters = new CompilerParameters(
    new string[] { @"System.dll" });
    //构建一个表示带有一个函数的代码字符串，返回类型为字符串表达式
    StringBuilder builder = new StringBuilder("using System;class CalcExp{public static object Calc(){ return \"expression\";}}");
    builder.Replace("\"expression\"", expression);
    string code = builder.ToString();
    //构建从编译器返回的编译结果实例
    CompilerResults results = codeProvider.CompileAssemblyFromSource(parameters, new string[] { code });
    //判定是否编译有误
    if (results.Errors.HasErrors)
    {
        result = null;
    }
    else      
    {      
        //获取到编辑其中自定义类的程序集 
        System.Reflection.Assembly assembly = results.CompiledAssembly;
        //获取程序集指定名称的类型       
        Type type = assembly.GetType("CalcExp");        
        //执行指定名称符合条件的内部函数        
        result = type.InvokeMember("Calc", System.Reflection.BindingFlags.Public | System.Reflection.BindingFlags.Static | System.Reflection.BindingFlags.InvokeMethod
                , System.Type.DefaultBinder, null, null);
    }
    return result;
}
```

调用（Framework 4.6）：

<img src="arith_codeprovider_demo.png" alt="image-20191103010234950" style="zoom:145%;" />

结果：

<img src="arith_codeprovider_result.png" alt="image-20191103010624683" style="zoom:145%;" />

### 参考链接

[C#中字符串转换为计算公式（自定义公式的计算）](https://www.cnblogs.com/jearay/p/3613584.html )

[C#中字符串转换为计算公式,并计算结果](htps://www.cnblogs.com/wzf-Code/p/5776342.html)

[C#自动计算字符串公式的四种方法](https://blog.csdn.net/ifu25/article/details/53292134) 

[A Calculation Engine for .NET]( https://www.codeproject.com/articles/246374/a-calculation-engine-for-net)"CalcEngine"

[Flee](https://github.com/mparlak/Flee)“Flee-暂时没用上-需求太简单”

[C#如何对字符串公式执行计算？](https://pzy.io/archives/2019/10/calculating-string-formula.html)

[C#字符串中包含的运算符按正常计算 例如按四则运算等，类似公式计算，很好很强大](https://www.cnblogs.com/smartsmile/archive/2012/10/09/6234410.html)" CSharpCodeProvider实现方式参考"

