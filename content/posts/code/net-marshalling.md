---
title: .NET Marshalling
author: Viktor Halitsyn
date: 2008-01-22T11:12:00+00:00
categories:
  - tech
  - code
tags:
  - .net

---
Interesting things happen when you try to marshal something in real life&#8230;.Even if you do not happen to dig into strange structure-marshalling: just a few methods or smth. strange problems may occur. For example marshalling char** for in and out usage. I was struggling for about a day for the code worked on developmnet environment and did not on test:). So the correct marshal is next [native] </p> 

```cpp
extern __declspec(dllexport) int SomeFunc(char** stringArray);
```
 [marshal] 

```cs
[DllImport("funct.dll", EntryPoint = "SomeFunc", ExactSpelling = true)]
internal static extern int SomeFunc([In,Out] String[] stringArray);
```

AND! Do not forget to allocate enough memory for the array elements:
  
[pseudocode] 

```cs
for (int i = 0; i <> {
           arr[i] = new String(' ', 256);
}
```

If you want to marshal a string and DO NOT use it afterwards(no result is written to it) use string but if you do use a StringBuilder with OUT modifier: [native] </p> 

```cpp
extern __declspec(dllexport) int SomeFunc(void* ad, char* buf, int size, float someParam);
```
 [marshal] 

```cs
[DllImport("funct.dll", EntryPoint = "SomeFunc", ExactSpelling = true)]
internal static extern int SomeFunc(IntPtr handle, [Out] StringBuilder buffer, int size, [In] float someParam);
```

Now let us imagine a more complicated example of function: two string are passed to the function: one is an ASCII string and one is a UNICODE one. The result has to be written into the second string. simple auto modifier in CharSet wont help we&#8217;ll have to use MarshalAs attribute: [native] </p> 

```cpp
extern __declspec(dllexport) int SomeFunc(void* ad, const char *name, wchar_t *value, int valueSize);
``` 

[marshal]

```cs
[DllImport("funct.dll", EntryPoint = "SomeFunc",CharSet=CharSet.Auto, ExactSpelling = true)]
internal static extern int SomeFunc(IntPtr handle, [MarshalAs(UnmanagedType.LPStr)] [In] string name, [MarshalAs(UnmanagedType.LPWStr)] [In,Out] string value, int size);
```