---
layout: post
title: Test Chordates
data: 2014-11-12 05:50:55 +0800
categories: jekyll
tags: English CCC
author: Sherry
mathjax: true
---
- Content
{:toc}


## What is the difference between String and string in C#? 

Example (note the case):

```c#
string s = "Hello world!";
String s = "Hello world!";
```


What are the guidelines for the use of each? And what are the differences?

#### Answer-1

> `string` is an alias in C# for `System.String`.
> So technically, there is no difference. It's like `int` vs. `System.Int32`.
>
> As far as guidelines, it's generally recommended to use string any time you're referring to an object.
>
> e.g.
>
> ```c#
> string place = "world";
> ```
>
>
> Likewise, I think it's generally recommended to use String if you need to refer specifically to the class.
>
> e.g.
>
> ```c#
> string greet = String.Format("Hello {0}!", place);
> ```
>
> **This is the style that Microsoft tends to use in their examples.** [Example]( https://docs.microsoft.com/en-us/dotnet/api/system.string.format?view=netframework-4.8#examples )
> It appears that the guidance in this area may have changed, as StyleCop now enforces the use of the C# specific aliases.

#### Answer-2

> Just for the sake of completeness, here's a brain dump of related information...
>
> As others have noted, `string` is an alias for `System.String`. They compile to the same code, so at execution time there is no difference whatsoever. This is just one of the aliases in C#. The complete list is:
>
> ```c#
> object:  System.Object
> string:  System.String
> bool:    System.Boolean
> byte:    System.Byte
> sbyte:   System.SByte
> short:   System.Int16
> ushort:  System.UInt16
> int:     System.Int32
> uint:    System.UInt32
> long:    System.Int64
> ulong:   System.UInt64
> float:   System.Single
> double:  System.Double
> decimal: System.Decimal
> char:    System.Char
> ```
>
> Apart from `string` and `object`, the aliases are all to value types. `decimal` is a value type, but not a primitive type in the CLR. The only primitive type which doesn't have an alias is `System.IntPtr`.
>
> In the spec, the value type aliases are known as "simple types". Literals can be used for constant values of every simple type; no other value types have literal forms available. (Compare this with VB, which allows `DateTime` literals, and has an alias for it too.)
>
> There is one circumstance in which you *have* to use the aliases: when explicitly specifying an enum's underlying type. For instance:
>
> ```c#
> public enum Foo : UInt32 {} // Invalid
> public enum Bar : uint   {} // Valid
> ```
>
> That's just a matter of the way the spec defines enum declarations - the part after the colon has to be the *integral-type* production, which is one token of `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`... as opposed to a *type* production as used by variable declarations for example. It doesn't indicate any other difference.
>
> Finally, when it comes to which to use: personally I use the aliases everywhere for the implementation, but the CLR type for any APIs. It really doesn't matter too much which you use in terms of implementation - consistency among your team is nice, but no-one else is going to care. On the other hand, it's genuinely important that if you refer to a type in an API, you do so in a language neutral way. A method called `ReadInt32` is unambiguous, whereas a method called `ReadInt` requires interpretation. The caller could be using a language which defines an `int` alias for `Int16`, for example. The .NET framework designers have followed this pattern, good examples being in the `BitConverter`, `BinaryReader` and `Convert` classes.

#### Answer-3

> `String` stands for `System.String` and it is a .NET Framework type. **`string` is an alias** in the C# language for `System.String`. Both of them are compiled to **`System.String` in IL** (Intermediate Language), so there is no difference. Choose what you like and use that. If you code in C#, I'd prefer `string` as it's a C# type alias and well-known by C# programmers.
>
> I can say the same about **(`int`, `System.Int32`)** etc..

#### Answer-4

> The best answer I have ever heard about using the provided type aliases in C# comes from Jeffrey Richter in his book CLR Via C#. Here are his 3 reasons:
>
> > - I've seen a number of developers confused, not knowing whether to use **string** or **String** in their code. Because in C# the string (a keyword) maps exactly to System.String (an FCL type), there is no difference and either can be used.
> > - In C#, **long** maps to **System.Int64**, but in a different programming language, **long** could map to an **Int16** or **Int32**. In fact, C++/CLI does in fact treat long as an **Int32**. Someone reading source code in one language could easily misinterpret the code's intention if he or she were used to programming in a different programming language. In fact, most languages won't even treat **long** as a keyword and won't compile code that uses it.
> > - The FCL has many methods that have type names as part of their method names. For example, the **BinaryReader** type offers methods such as **ReadBoolean**, **ReadInt32**, **ReadSingle**, and so on, and the **System.Convert** type offers methods such as **ToBoolean**, **ToInt32**, **ToSingle**, and so on. Although it's legal to write the following code, the line with float feels very unnatural to me, and it's not obvious that the line is correct:
>
> ```c#
> BinaryReader br = new BinaryReader(...);
> float val  = br.ReadSingle(); // OK, but feels unnatural
> Single val = br.ReadSingle(); // OK and feels good
> ```
>
> So there you have it. I think these are all really good points. I however, don't find myself using Jeffrey's advice in my own code. Maybe I am too stuck in my C# world but I end up trying to make my code look like the framework code.

#### Answer-5

> `string` is a reserved word, but `String` is just a class name. This means that `string` cannot be used as a variable name by itself.
>
> If for some reason you wanted a variable called *string*, you'd see only the first of these compiles:
>
> ```c#
> StringBuilder String = new StringBuilder();  // compiles
> StringBuilder string = new StringBuilder();  // doesn't compile 
> ```
>
> If you really want a variable name called *string* you can use `@` as a prefix:
>
> ```c#
> StringBuilder @string = new StringBuilder();
> ```
>
> Another critical difference: Stack Overflow highlights them differently.



## How to enumerate an enum?

How can you enumerate an `enum` in C#?

E.g. the following code does not compile:

```csharp
public enum Suit 
{
    Spades,
    Hearts,
    Clubs,
    Diamonds
}

public void EnumerateAllSuitsDemoMethod() 
{
    foreach (Suit suit in Suit) 
    {
        DoSomething(suit);
    }
}
```

And gives the following compile-time error:

> 'Suit' is a 'type' but is used like a 'variable'

It fails on the `Suit` keyword, the second one.

#### Answer-1

> ```cs
> foreach (Suit suit in (Suit[]) Enum.GetValues(typeof(Suit)))
> {
> }
> ```
>
> **Note**: The cast to `(Suit[])` is not strictly necessary, [but does make the code 0.5 ns faster.](https://gist.github.com/bartoszkp/9e059c3edccc07a5e588#gistcomment-2625454)

#### Answer-2

> It looks to me like you really want to print out the names of each enum, rather than the values. In which case `Enum.GetNames()` seems to be the right approach.
>
> ```cs
> public enum Suits
> {
>     Spades,
>     Hearts,
>     Clubs,
>     Diamonds,
>     NumSuits
> }
> 
> public void PrintAllSuits()
> {
>     foreach (string name in Enum.GetNames(typeof(Suits)))
>     {
>         System.Console.WriteLine(name);
>     }
> }
> ```
>
> By the way, incrementing the value is not a good way to enumerate the values of an enum. You should do this instead.
>
> I would use `Enum.GetValues(typeof(Suit))` instead.
>
> ```cs
> public enum Suits
> {
>     Spades,
>     Hearts,
>     Clubs,
>     Diamonds,
>     NumSuits
> }
> 
> public void PrintAllSuits()
> {
>     foreach (var suit in Enum.GetValues(typeof(Suits)))
>     {
>         System.Console.WriteLine(suit.ToString());
>     }
> }
> ```

#### Answer-3

> I made some extensions for easy enum usage, maybe someone can use it...
>
> ```cs
> public static class EnumExtensions
> {
>     /// <summary>
>     /// Gets all items for an enum value.
>     /// </summary>
>     /// <typeparam name="T"></typeparam>
>     /// <param name="value">The value.</param>
>     /// <returns></returns>
>     public static IEnumerable<T> GetAllItems<T>(this Enum value)
>     {
>         foreach (object item in Enum.GetValues(typeof(T)))
>         {
>             yield return (T)item;
>         }
>     }
> 
>     /// <summary>
>     /// Gets all items for an enum type.
>     /// </summary>
>     /// <typeparam name="T"></typeparam>
>     /// <param name="value">The value.</param>
>     /// <returns></returns>
>     public static IEnumerable<T> GetAllItems<T>() where T : struct
>     {
>         foreach (object item in Enum.GetValues(typeof(T)))
>         {
>             yield return (T)item;
>         }
>     }
> 
>     /// <summary>
>     /// Gets all combined items from an enum value.
>     /// </summary>
>     /// <typeparam name="T"></typeparam>
>     /// <param name="value">The value.</param>
>     /// <returns></returns>
>     /// <example>
>     /// Displays ValueA and ValueB.
>     /// <code>
>     /// EnumExample dummy = EnumExample.Combi;
>     /// foreach (var item in dummy.GetAllSelectedItems<EnumExample>())
>     /// {
>     ///    Console.WriteLine(item);
>     /// }
>     /// </code>
>     /// </example>
>     public static IEnumerable<T> GetAllSelectedItems<T>(this Enum value)
>     {
>         int valueAsInt = Convert.ToInt32(value, CultureInfo.InvariantCulture);
> 
>         foreach (object item in Enum.GetValues(typeof(T)))
>         {
>             int itemAsInt = Convert.ToInt32(item, CultureInfo.InvariantCulture);
> 
>             if (itemAsInt == (valueAsInt & itemAsInt))
>             {
>                 yield return (T)item;
>             }
>         }
>     }
> 
>     /// <summary>
>     /// Determines whether the enum value contains a specific value.
>     /// </summary>
>     /// <param name="value">The value.</param>
>     /// <param name="request">The request.</param>
>     /// <returns>
>     ///     <c>true</c> if value contains the specified value; otherwise, <c>false</c>.
>     /// </returns>
>     /// <example>
>     /// <code>
>     /// EnumExample dummy = EnumExample.Combi;
>     /// if (dummy.Contains<EnumExample>(EnumExample.ValueA))
>     /// {
>     ///     Console.WriteLine("dummy contains EnumExample.ValueA");
>     /// }
>     /// </code>
>     /// </example>
>     public static bool Contains<T>(this Enum value, T request)
>     {
>         int valueAsInt = Convert.ToInt32(value, CultureInfo.InvariantCulture);
>         int requestAsInt = Convert.ToInt32(request, CultureInfo.InvariantCulture);
> 
>         if (requestAsInt == (valueAsInt & requestAsInt))
>         {
>             return true;
>         }
> 
>         return false;
>     }
> }
> ```
>
> The enum itself must be decorated with the [FlagsAttribute](http://msdn.microsoft.com/en-us/library/system.flagsattribute.aspx)
>
> ```cs
> [Flags]
> public enum EnumExample
> {
>     ValueA = 1,
>     ValueB = 2,
>     ValueC = 4,
>     ValueD = 8,
>     Combi = ValueA | ValueB
> }
> ```

#### Answer-4

> Some versions of the .NET framework do not support `Enum.GetValues`. Here's a good workaround from [Ideas 2.0: Enum.GetValues in Compact Framework](https://web.archive.org/web/20100530072822/http://ideas.dalezak.ca/2008/11/enumgetvalues-in-compact-framework.html):
>
> ```cs
> public Enum[] GetValues(Enum enumeration)
> {
>     FieldInfo[] fields = enumeration.GetType().GetFields(BindingFlags.Static | BindingFlags.Public);
>     Enum[] enumerations = new Enum[fields.Length];
> 
>     for (var i = 0; i < fields.Length; i++)
>         enumerations[i] = (Enum) fields[i].GetValue(enumeration);
> 
>     return enumerations;
> }
> ```
>
> As with any code that involves [reflection](https://docs.microsoft.com/en-us/dotnet/framework/reflection-and-codedom/reflection), you should take steps to ensure it runs only once and results are cached.

#### Answer-5

> Why is no one using `Cast`?
>
> ```cs
> var suits = Enum.GetValues(typeof(Suit)).Cast<Suit>();
> ```
>
> There you go `IEnumerable`.



## How to cast int to enum?

 How can an `int` be cast to an `enum` in C#? 

#### Answer-1

> From a string:
>
> ```cs
> YourEnum foo = (YourEnum) Enum.Parse(typeof(YourEnum), yourString);
> // the foo.ToString().Contains(",") check is necessary for enumerations marked with an [Flags] attribute
> if (!Enum.IsDefined(typeof(YourEnum), foo) && !foo.ToString().Contains(","))
>   throw new InvalidOperationException($"{yourString} is not an underlying value of the YourEnum enumeration.")
> ```
>
> From an int:
>
> ```cs
> YourEnum foo = (YourEnum)yourInt;
> ```
>
> **Update:**
>
> From number you can also
>
> ```cs
> YourEnum foo = (YourEnum)Enum.ToObject(typeof(YourEnum) , yourInt);
> ```

#### Answer-2

> Just cast it:
>
> ```cs
> MyEnum e = (MyEnum)3;
> ```
>
> You can check if it's in range using [Enum.IsDefined](http://msdn.microsoft.com/en-us/library/system.enum.isdefined.aspx):
>
> ```cs
> if (Enum.IsDefined(typeof(MyEnum), 3)) { ... }
> ```

#### Answer-3

> Alternatively, use an extension method instead of a one-liner:
>
> ```cs
> public static T ToEnum<T>(this string enumString)
> {
>     return (T) Enum.Parse(typeof (T), enumString);
> }
> ```
>
> ***Usage:***
>
> ```cs
> Color colorEnum = "Red".ToEnum<Color>();
> ```
>
> OR
>
> ```cs
> string color = "Red";
> var colorEnum = color.ToEnum<Color>();
> ```

#### Answer-4

> I think to get a complete answer, people have to know how enums work internally in .NET.
>
> **How stuff works**
>
> An enum in .NET is a structure that maps a set of values (fields) to a basic type (the default is `int`). However, you can actually choose the integral type that your enum maps to:
>
> ```cs
> public enum Foo : short
> ```
>
> In this case the enum is mapped to the `short` data type, which means it will be stored in memory as a short and will behave as a short when you cast and use it.
>
> If you look at it from a IL point of view, a (normal, int) enum looks like this:
>
> ```cs
> .class public auto ansi serializable sealed BarFlag extends System.Enum
> {
>     .custom instance void System.FlagsAttribute::.ctor()
>     .custom instance void ComVisibleAttribute::.ctor(bool) = { bool(true) }
> 
>     .field public static literal valuetype BarFlag AllFlags = int32(0x3fff)
>     .field public static literal valuetype BarFlag Foo1 = int32(1)
>     .field public static literal valuetype BarFlag Foo2 = int32(0x2000)
> 
>     // and so on for all flags or enum values
> 
>     .field public specialname rtspecialname int32 value__
> }
> ```
>
> What should get your attention here is that the `value__` is stored separately from the enum values. In the case of the enum `Foo` above, the type of `value__` is int16. This basically means that you can store whatever you want in an enum, **as long as the types match**.
>
> At this point I'd like to point out that `System.Enum` is a value type, which basically means that `BarFlag` will take up 4 bytes in memory and `Foo` will take up 2 -- e.g. the size of the underlying type (it's actually more complicated than that, but hey...).
>
> **The answer**
>
> So, if you have an integer that you want to map to an enum, the runtime only has to do 2 things: copy the 4 bytes and name it something else (the name of the enum). Copying is implicit because the data is stored as value type - this basically means that if you use unmanaged code, you can simply interchange enums and integers without copying data.
>
> To make it safe, I think it's a best practice to **know that the underlying types are the same or implicitly convertible** and to ensure the enum values exist (they aren't checked by default!).
>
> To see how this works, try the following code:
>
> ```cs
> public enum MyEnum : int
> {
>     Foo = 1,
>     Bar = 2,
>     Mek = 5
> }
> 
> static void Main(string[] args)
> {
>     var e1 = (MyEnum)5;
>     var e2 = (MyEnum)6;
> 
>     Console.WriteLine("{0} {1}", e1, e2);
>     Console.ReadLine();
> }
> ```
>
> Note that casting to `e2` also works! From the compiler perspective above this makes sense: the `value__` field is simply filled with either 5 or 6 and when `Console.WriteLine` calls `ToString()`, the name of `e1` is resolved while the name of `e2` is not.
>
> If that's not what you intended, use `Enum.IsDefined(typeof(MyEnum), 6)` to check if the value you are casting maps to a defined enum.
>
> Also note that I'm explicit about the underlying type of the enum, even though the compiler actually checks this. I'm doing this to ensure I don't run into any surprises down the road. To see these surprises in action, you can use the following code (actually I've seen this happen a lot in database code):
>
> ```cs
> public enum MyEnum : short
> {
>     Mek = 5
> }
> 
> static void Main(string[] args)
> {
>     var e1 = (MyEnum)32769; // will not compile, out of bounds for a short
> 
>     object o = 5;
>     var e2 = (MyEnum)o;     // will throw at runtime, because o is of type int
> 
>     Console.WriteLine("{0} {1}", e1, e2);
>     Console.ReadLine();
> }
> ```

#### Answer-5

> Take the following example:
>
> ```cs
> int one = 1;
> MyEnum e = (MyEnum)one;
> ```



## Case insensitive 'Contains(string)'

Is there a way to make the following return true?

```cs
string title = "ASTRINGTOTEST";
title.Contains("string");
```

There doesn't seem to be an overload that allows me to set the case sensitivity.. Currently I UPPERCASE them both, but that's just silly (by which I am referring to the [i18n](http://en.wikipedia.org/wiki/Internationalization_and_localization) issues that come with up- and down casing).

**UPDATE**
This question is ancient and since then I have realized I asked for a simple answer for a really vast and difficult topic if you care to investigate it fully.
For most cases, in mono-lingual, English code bases [this](https://stackoverflow.com/a/444818/11333) answer will suffice. I'm suspecting because most people coming here fall in this category this is the most popular answer.
[This](https://stackoverflow.com/a/15464440/11333) answer however brings up the inherent problem that we can't compare text case insensitive until we know both texts are the same culture and we know what that culture is. This is maybe a less popular answer, but I think it is more correct and that's why I marked it as such.

#### Answer-1

> To test if the string `paragraph` contains the string `word` (thanks @QuarterMeister)
>
> ```cs
> culture.CompareInfo.IndexOf(paragraph, word, CompareOptions.IgnoreCase) >= 0
> ```
>
> Where `culture` is the instance of [`CultureInfo`](http://msdn.microsoft.com/en-gb/library/system.globalization.cultureinfo(v=vs.110).aspx) describing the language that the text is written in.
>
> This solution is transparent about **the definition of case-insensitivity, which is language dependent**. For example, the English language uses the characters `I` and `i` for the upper and lower case versions of the ninth letter, whereas the Turkish language uses these characters for the [eleventh and twelfth letters](http://en.wikipedia.org/wiki/Dotted_and_dotless_I) of its 29 letter-long alphabet. The Turkish upper case version of 'i' is the unfamiliar character 'İ'.
>
> Thus the strings `tin` and `TIN` are the same word *in English*, but different words *in Turkish*. As I understand, one means 'spirit' and the other is an onomatopoeia word. (Turks, please correct me if I'm wrong, or suggest a better example)
>
> To summarise, you can only answer the question 'are these two strings the same but in different cases' *if you know what language the text is in*. If you don't know, you'll have to take a punt. Given English's hegemony in software, you should probably resort to [`CultureInfo.InvariantCulture`](https://stackoverflow.com/questions/9760237/what-does-cultureinfo-invariantculture-mean), because it'll be wrong in familiar ways.

#### Answer-2

> You could use the [String.IndexOf Method](https://msdn.microsoft.com/en-us/library/ms224425(v=vs.110).aspx) and pass [`StringComparison.OrdinalIgnoreCase`](https://msdn.microsoft.com/en-us/library/system.stringcomparer.ordinalignorecase(v=vs.110).aspx) as the type of search to use:
>
> ```cs
> string title = "STRING";
> bool contains = title.IndexOf("string", StringComparison.OrdinalIgnoreCase) >= 0;
> ```
>
> Even better is defining a new extension method for string:
>
> ```cs
> public static class StringExtensions
> {
>     public static bool Contains(this string source, string toCheck, StringComparison comp)
>     {
>         return source?.IndexOf(toCheck, comp) >= 0;
>     }
> }
> ```
>
> Note, that [null propagation](https://stackoverflow.com/questions/25666993/c-sharp-null-propagating-operator-conditional-access-expression-if-blocks) `?.` is available since C# 6.0 (VS 2015), for older versions use
>
> ```cs
> if (source == null) return false;
> return source.IndexOf(toCheck, comp) >= 0;
> ```
>
> USAGE:
>
> ```cs
> string title = "STRING";
> bool contains = title.Contains("string", StringComparison.OrdinalIgnoreCase);
> ```

#### Answer-3

> You can use `IndexOf()` like this:
>
> ```cs
> string title = "STRING";
> 
> if (title.IndexOf("string", 0, StringComparison.CurrentCultureIgnoreCase) != -1)
> {
>     // The string exists in the original
> }
> ```
>
> Since 0 (zero) can be an index, you check against -1.
>
> [MSDN](https://msdn.microsoft.com/en-us/library/k8b1470s(v=vs.110).aspx)
>
> > The zero-based index position of value if that string is found, or -1 if it is not. If value is String.Empty, the return value is 0.

#### Answer-4

> Alternative solution using Regex:
>
> ```cs
> bool contains = Regex.IsMatch("StRiNG to search", Regex.Escape("string"), RegexOptions.IgnoreCase);
> ```

#### Answer-5

> You could always just up or downcase the strings first.
>
> ```cs
> string title = "string":
> title.ToUpper().Contains("STRING")  // returns true
> ```
>
> Oops, just saw that last bit. A case insensitive compare would `*`probably`*` do the same anyway, and if performance is not an issue, I don't see a problem with creating uppercase copies and comparing those. I could have sworn that I once saw a case-insensitive compare once...



## What is the best way to iterate over a dictionary?

 I've seen a few different ways to iterate over a dictionary in C#. Is there a standard way? 

#### Answer-1

> ```cs
> foreach(KeyValuePair<string, string> entry in myDictionary)
> {
>     // do something with entry.Value or entry.Key
> }
> ```

#### Answer-2

> If you are trying to use a generic Dictionary in C# like you would use an associative array in another language:
>
> ```cs
> foreach(var item in myDictionary)
> {
>   foo(item.Key);
>   bar(item.Value);
> }
> ```
>
> Or, if you only need to iterate over the collection of keys, use
>
> ```cs
> foreach(var item in myDictionary.Keys)
> {
>   foo(item);
> }
> ```
>
> And lastly, if you're only interested in the values:
>
> ```cs
> foreach(var item in myDictionary.Values)
> {
>   foo(item);
> }
> ```
>
> (Take note that the `var` keyword is an optional C# 3.0 and above feature, you could also use the exact type of your keys/values here)

#### Answer-3

> In some cases you may need a counter that may be provided by for-loop implementation. For that, LINQ provides [`ElementAt`](https://msdn.microsoft.com/en-us/library/bb299233(v=vs.110).aspx) which enables the following:
>
> ```cs
> for (int index = 0; index < dictionary.Count; index++) {
>   var item = dictionary.ElementAt(index);
>   var itemKey = item.Key;
>   var itemValue = item.Value;
> }
> ```

#### Answer-4

> Depends on whether you're after the keys or the values...
>
> From the MSDN [`Dictionary(TKey, TValue)`](http://msdn.microsoft.com/en-us/library/xfhwa508.aspx) Class description:
>
> ```cs
> // When you use foreach to enumerate dictionary elements,
> // the elements are retrieved as KeyValuePair objects.
> Console.WriteLine();
> foreach( KeyValuePair<string, string> kvp in openWith )
> {
>     Console.WriteLine("Key = {0}, Value = {1}", 
>         kvp.Key, kvp.Value);
> }
> 
> // To get the values alone, use the Values property.
> Dictionary<string, string>.ValueCollection valueColl =
>     openWith.Values;
> 
> // The elements of the ValueCollection are strongly typed
> // with the type that was specified for dictionary values.
> Console.WriteLine();
> foreach( string s in valueColl )
> {
>     Console.WriteLine("Value = {0}", s);
> }
> 
> // To get the keys alone, use the Keys property.
> Dictionary<string, string>.KeyCollection keyColl =
>     openWith.Keys;
> 
> // The elements of the KeyCollection are strongly typed
> // with the type that was specified for dictionary keys.
> Console.WriteLine();
> foreach( string s in keyColl )
> {
>     Console.WriteLine("Key = {0}", s);
> }
> ```

#### Answer-5

> Generally, asking for "the best way" without a specific context is like asking *what is the best color*?
>
> One the one hand, there are many colors and there's no best color. It depends on the need and often on taste, too.
>
> On the other hand, there are many ways to iterate over a Dictionary in C# and there's no best way. It depends on the need and often on taste, too.
>
> **Most straightforward way**
>
> ```cs
> foreach (var kvp in items)
> {
>     // key is kvp.Key
>     doStuff(kvp.Value)
> }
> ```
>
> If you need only the value (allows to call it `item`, more readable than `kvp.Value`).
>
> ```cs
> foreach (var item in items.Values)
> {
>     doStuff(item)
> }
> ```
>
> **If you need a specific sort order**
>
> Generally, beginners are surprised about order of enumeration of a Dictionary.
>
> LINQ provides a concise syntax that allows to specify order (and many other things), e.g.:
>
> ```cs
> foreach (var kvp in items.OrderBy(kvp => kvp.Key))
> {
>     // key is kvp.Key
>     doStuff(kvp.Value)
> }
> ```
>
> Again you might only need the value. LINQ also provides a concise solution to:
>
> - iterate directly on the value (allows to call it `item`, more readable than `kvp.Value`)
> - but sorted by the keys
>
> Here it is:
>
> ```cs
> foreach (var item in items.OrderBy(kvp => kvp.Key).Select(kvp => kvp.Value))
> {
>     doStuff(item)
> }
> ```
>
> There are many more real-world use case you can do from these examples. If you don't need a specific order, just stick to the "most straightforward way" (see above)!