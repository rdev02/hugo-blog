---
title: 'Creating dynamic plugin architecture with C#.NET'
author: Viktor Halitsyn
date: 2009-12-12T23:11:00+00:00
type: post
categories:
  - tech
  - code
disqusIdentifier: 5821225733
tags:
  - .net
  - c#

---
There are a few articles on the web describing how to load assemblies using reflection, thus allowing you to execute third-party code inside your own application. While applicable in most cases, my customer wanted those plugins to be replaceable on-the-fly. Keep mind that once an assembly has been loaded into an application domain it cannot be unloaded unless you unload the domain(which is you main app domain by default)=close the application. Also Loading dll&#8217;s in the main application domain is very risky in terms of security concerns. But back to the problem &#8211; as long as your assembly is loaded into any domain it is locked, meaning that you can&#8217;t replace it with the newer version.
  
Naturally you&#8217;ll turn to creating a separate app domain. and try to AppDomain.load() assembly there&#8230; Now that&#8217;s the time you&#8217;ll start experiencing problems. [Here ][1]we can get smth like &#8220;<span style="font-family: Verdana,Arial,Helvetica,sans-serif; font-size: 11px;">This method should only be used to load an assembly into the current application domain. This method is defined for interoperability callers who cannot call the static <a href="http://msdn.microsoft.com/en-us/library/system.reflection.assembly.load.aspx" style="color: #0033cc; text-decoration: none;">Load</a> method.<span style="font-family: 'Times New Roman'; font-size: medium;">&#8221; In short &#8211; you&#8217;ll fail to load the assembly having its path. Well, actually what you really need is to be able to get some object out of there, right? Suppose you use simple &#8220;middle-tier-interface&#8221; assembly like<br /> </span></span>

```cs
namespace PInterface
{
    public interface PluginInterface
    {
        string DoSmth();
    }
}
```

Then when you want to load Some type, let it be like this:

```cs
namespace SamplePlugin
{
    public class Plugin1 :MarshalByRefObject, PluginInterface
    {
        #region PluginInterface

        public string DoSmth()
        {
            return "Hello!";
        }

        #endregion
    }
}
```

You would simply do it like:

```cs
AppDomain newDomain = AppDomain.CreateDomain("newDOmain");
newDomain.CreateInstanceFromAndUnwrap(pathToDLL, pluginClassName);
```

Assuming you know the class name(s) of course:). If you don&#8217;t, then it&#8217;s 2 choices: either you specify that the plugin assembly has to have a &#8220;marker&#8221; type (say &#8220;class PluginType{}&#8221;) and then after loading an assembly into app domain execute the callback on it to get the class names via reflection load OR you might end up with a crossDomain walker like this:

```cs
using System;
using System.Collections.Generic;
using System.Windows.Forms;
using System.Reflection;

namespace WindowsApplication1
{
    static class Program
    {
        private readonly static string PLUGIN_INTERFACE_NAME = "PluginInterfaceName";
        private readonly static string PLUGIN_CLASSES_NAMES = "PluginClasses";
        /// <summary>
        /// The main entry point for the application.
        /// </summary>
        [STAThread]
        static int Main(String[] args)
        {
            object interfaceName =  AppDomain.CurrentDomain.GetData(PLUGIN_INTERFACE_NAME);
            List<string> pluginClassesNames = new List<string>(3);
            if (args.Length > 0)
            {
                string sInterfaceName = (string)interfaceName;
                foreach(string arg in args)
                try
                {
                    Assembly asm = Assembly.LoadFrom(arg);
                    Type[] possiblePlugins = asm.GetTypes();
                    List<Type> realCandidates = new List<Type>(3);
                    foreach (Type t in possiblePlugins)
                    {
                        if (!t.IsAbstract && !t.IsInterface && (t.GetInterface(sInterfaceName) != null))
                            pluginClassesNames.Add(t.FullName);
                    }
                }
                catch (Exception ex)
                {
                    pluginClassesNames.Add("Exception " + ex.ToString());
                }
            }

            AppDomain.CurrentDomain.SetData(PLUGIN_CLASSES_NAMES, pluginClassesNames.ToArray());
            return pluginClassesNames.Count;            
        }
    }
}
```

using it won&#8217;t be a problem:

```cs
int execRes = newDomain.ExecuteAssembly(_classFinderAssemblyPath, AppDomain.CurrentDomain.Evidence, new string[] { path });
if (execRes <= 0)
    throw new Exception("No plugins found in " + path);
string[] pluginClasses = (string[])newDomain.GetData(PLUGIN_CLASSES_NAMES);
```

 Then calling a simple AppDomain.Unload(); would solve your problem. If you are lucky enough to have a pure .NET world and are not concerned about security, then you&#8217;re definitely lucky. And I am not :):(.
  
    Let us now assume you want those plugins to be consumable from the c++ application. Suppose your client has a big c++ app and now realized developing plugins in .NET would be easier. Then your natural choice is to make it as much less painful for c++ app to use you code as possible. A solution to that would be to have a .NET plugin manager, exposed to c++ app via the COM object. You may end up with smth. like this:
  


```cs
using System;
using System.Collections.Generic;
using System.Text;
using System.Runtime.InteropServices;
using System.Reflection;
using System.IO;
using System.Runtime.Remoting;
using System.Security.Policy;
using System.Security;
using System.Security.Permissions;

namespace ComPluginManager
{
    [ComVisible(true)]
    public class PluginManger: IPluginManager
    {
        private IDictionary<String, AppDomain> _pluginStore;
        private String _interfaceName = "PluginInterface";
        private String _classFinderAssemblyPath;
        private readonly static string PLUGIN_INTERFACE_NAME = "PluginInterfaceName";
        private readonly static string PLUGIN_CLASSES_NAMES = "PluginClasses";

        public PluginManger()
        {
            _pluginStore = new Dictionary<String, AppDomain>(5);
        }

        public object[] LoadPlugin(string path) {
            List<object> result = new List<object>(3);
            
            AppDomainSetup setup = new AppDomainSetup();
            setup.ApplicationBase = Path.GetDirectoryName(path); // plugin path
            PermissionSet trustPermissionSet = new PermissionSet(PermissionState.Unrestricted);
            AppDomain newDomain = AppDomain.CreateDomain(
                String.Format("Domain{0}", Path.GetFileName(path)),
                null,
                setup,
                trustPermissionSet);

            try
            {
                newDomain.SetData(PLUGIN_INTERFACE_NAME, "PluginInterface");
                int execRes = newDomain.ExecuteAssembly(_classFinderAssemblyPath, AppDomain.CurrentDomain.Evidence, new string[] { path });
                if (execRes <= 0)
                    throw new Exception("No plugins found in " + path);
                string[] pluginClasses = (string[])newDomain.GetData(PLUGIN_CLASSES_NAMES);

                _pluginStore[path] = newDomain;
                foreach (string pluginClass in pluginClasses)
             {
                    ObjectHandle objHandle = Activator.CreateInstanceFrom(newDomain, path, pluginClass);

                    if (objHandle != null)
                    {
                        object unwrappedInstance = objHandle.Unwrap();
                        result.Add(unwrappedInstance);
                    }

                    result.Add(_pluginStore[path].CreateInstanceFromAndUnwrap(path, pluginClass));
             }
                
            }
            catch (Exception ex) { 
                result.Add(ex);
            }       
            return result.ToArray();
        }     

        public void UnloadPlugin(string path) {
            try
            {
                AppDomain domain = _pluginStore[path];
                _pluginStore.Remove(path);
                AppDomain.Unload(domain);
            }
            catch (Exception ex)
            { ;}
        }

        public String InterfaceName { get { return _interfaceName; } set { _interfaceName = value; } }

        public String ClassFinderAssemblyPath { get { return _classFinderAssemblyPath; } set { _classFinderAssemblyPath = value; } }
        
    }
}
```

And it&#8217;s interface of course like:
  


```cs
using System;
using System.Collections.Generic;
using System.Text;
using System.Runtime.InteropServices;

namespace ComPluginManager
{
    [ComVisible(true)]
    public interface IPluginManager
    {
        String InterfaceName { get; set; }

        String ClassFinderAssemblyPath { get; set;}
        void UnloadPlugin(String path);
        Object[] LoadPlugin(String path);
    }
}
```

Two things to notice here: I haven&#8217;t managed to get the plain class&#8217;s public methods visible to C++ so I used an interface. And second, probably the most important &#8211; <span style="font-family: monospace; white-space: pre;">PermissionSet.</span> If you do not set this you wont be able to anything inside you app domain, except the very basic things like calculations and maybe some other data manipulation. You can find more info on this [here][2] and [here][3].
  
Another important thing is that as long as your code is going to be executed from an unmanaged environment and want to do smth with partially trusted code &#8211; you have to use
  


```cs
[assembly: AllowPartiallyTrustedCallersAttribute()]
```

In both manager and plugin assemblies. Further explanation on how it works can be found [here][4].
  
Now if you <span style="text-decoration: line-through;">crossed your fingers at the right moment</span> did everything wright this plugin manager would load assemblies for you, return you the instantiated objects and free the dlls on your request flawlesly.
  
And you will be able to put that shiny &#8220;Update&#8221; button on the toolbar without asking the user to reboot/restart an application whenever you need the  plugin update. And not bother with temporary files and &#8220;special&#8221; startup routines&#8230; etc. I think the idea is clear 🙂

 [1]: http://msdn.microsoft.com/en-us/library/36az8x58.aspx
 [2]: http://blogs.msdn.com/shawnfa/archive/2004/11/02/251239.aspx
 [3]: http://blogs.msdn.com/shawnfa/archive/2004/10/26/248114.aspx
 [4]: http://msdn.microsoft.com/en-us/magazine/cc163701.aspx