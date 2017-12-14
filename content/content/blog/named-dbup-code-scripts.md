+++
title = "Named Dbup Code Scripts"
draft = false
date = 2017-12-14
slug = "named-dbup-code-scripts"
+++

I'm a big fan of the [DbUp](https://github.com/DbUp/DbUp) library and its approach to database management. Rather than go over all the benefits of this approach, I'll [leave it to the docs](http://dbup.readthedocs.io/en/latest/philosophy-behind-dbup/) which do a much better job than I could. Suffice it to say, all the things it brings to the table - tight control, predictability, ORM independence, the power of raw SQL - are all good things!

Historically I have been able to do most things with this raw SQL approach. I've created these scripts with a fairly typical (I think) naming convention like so:

```
0001 - This is a script.sql
0002 - This is another script.sql
0003 - And this is another script.sql
```

These scripts are then excuted and applied in the expected order:

```
Id          ScriptName
----------- -----------------------------------------------------------
1           Project.Data.Scripts.0001 - This is a script.sql           
2           Project.Data.Scripts.0002 - This is another script.sql     
3           Project.Data.Scripts.0003 - And this is another script.sql 
```

Recently I required a bit more logic than usual, so updated my project to use the [EmbeddedScriptAndCodeProvider](http://dbup.readthedocs.io/en/latest/more-info/script-providers/#embeddedscriptandcodeprovider) so that it could utilise some code-based scripts, like so:

```
0004 - Code Script.cs
```

This is fine for a filename but not so much for a C# class, which by default gives us `_0004___Code_Script` (or a similar, conformant name). When executed this gives us:

```
Id          ScriptName                                                 
----------- -----------------------------------------------------------
1           Project.Data.Scripts._0004___Code_Script.cs                
2           Project.Data.Scripts.0001 - This is a script.sql           
3           Project.Data.Scripts.0002 - This is another script.sql     
4           Project.Data.Scripts.0003 - And this is another script.sql 
```

Not the order I wanted as the type name is used! I can rename the class some more and defer the problem to later if I really wanted:

```
Id          ScriptName                                                 
----------- -----------------------------------------------------------
1           Project.Data.Scripts.0001 - This is a script.sql            
2           Project.Data.Scripts.0002 - This is another script.sql      
3           Project.Data.Scripts.0003 - And this is another script.sql  
4           Project.Data.Scripts.0005 - Back to sql scripts.sql         
5           Project.Data.Scripts.Script0004_CodeScript.cs               
```

From what I can tell a more common approach is to name scripts not just with a number, but with an additional prefix like this (taken from the [DbUp Sample Application](https://github.com/DbUp/DbUp/tree/master/src/Samples/SampleApplication)):

```
Id          ScriptName                                               
----------- ---------------------------------------------------------
1           SampleApplication.Scripts.Script0001 - Create tables.sql 
2           SampleApplication.Scripts.Script0002 - Default feed.sql  
3           SampleApplication.Scripts.Script0003 - Settings.sql      
4           SampleApplication.Scripts.Script0004 - Redirects.sql     
5           SampleApplication.Scripts.Script0005ComplexUpdate.cs     
6           SampleApplication.Scripts.Script0006 - Transactions.sql  
```


I didn't want to mess about trying to rename existing scripts, and I'm also a bit pedantic and want them to have a similar style of naming, even if they are tucked away in a database table out of sight.

I wasn't able to find an existing solution to this, so decided to jump in to the DbUp code and have a look for myself. The [script provider in question](https://github.com/DbUp/DbUp/blob/master/src/DbUp/ScriptProviders/EmbeddedScriptAndCodeProvider.cs) gives us:

``` csharp
private IEnumerable<SqlScript> ScriptsFromScriptClasses(IConnectionManager connectionManager)
{
    var script = typeof(IScript);
    return connectionManager.ExecuteCommandsWithManagedConnection(dbCommandFactory => assembly
        .GetTypes()
        .Where(type => script.IsAssignableFrom(type) && type.IsClass)
        .Select(s => (SqlScript)new LazySqlScript(s.FullName + ".cs", () => ((IScript)Activator.CreateInstance(s)).ProvideScript(dbCommandFactory)))
        .ToList());
}
```

As you can see the full name of the type is used.

I decided the quickest solution for myself would be to create my own, slightly modified script provider. To start with, a simple, custom attribute:

``` csharp
using System;

namespace Slumber.Data.DbUp
{
    public class ScriptName : Attribute
    {
        public string Name { get; }

        public ScriptName(string name)
        {
            Name = name;
        }
    }
}
```

Applied to my script:

``` csharp
[ScriptName("0004 - Code Script")]
internal class Script0004_CodeScript : IScript
```

With a modified script provider:

``` csharp
private IEnumerable<SqlScript> ScriptsFromScriptClasses(IConnectionManager connectionManager)
{
    var script = typeof(IScript);
    return connectionManager.ExecuteCommandsWithManagedConnection(dbCommandFactory => _assembly
        .GetTypes()
        .Where(type => script.IsAssignableFrom(type) && type.IsClass)
        .Select(s =>
        {
            var scriptNameAttribute = s.GetCustomAttribute<ScriptName>(false);
            var scriptName = scriptNameAttribute != null
                ? scriptNameAttribute.Name + ".cs"
                : s.FullName + ".cs";
            return (SqlScript) new LazySqlScript(scriptName, () => ((IScript) Activator.CreateInstance(s)).ProvideScript(dbCommandFactory));
        })
        .ToList());
}
```

And voila!

```
Id          ScriptName                                                 
----------- -----------------------------------------------------------
1           Project.Data.Scripts.0001 - This is a script.sql           
2           Project.Data.Scripts.0002 - This is another script.sql     
3           Project.Data.Scripts.0003 - And this is another script.sql 
4           Project.Data.Scripts.0004 - Code Script.cs                 
5           Project.Data.Scripts.0005 - Back to sql scripts.sql        
```

I'm now free to be as pedantic as I want. If there's already a way to do this then please feel free to let me know!