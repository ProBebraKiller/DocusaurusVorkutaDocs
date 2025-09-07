---
sidebar_position: 1
---

# Basics

## Node namespace specification
Firstly, to create a node you need to specify its namespace <br></br>
Namespace pattern looks like this:

```csharp
namespace NodeEditor.Nodes.Yournamespace
{
    // Node content
}
```
**"Yournamespace"** will be shown in "Nodes" section in right click context menu. <br></br>
Also, it will become a selectable workspace in NodeEditor automatically

## Node content
Since nodes have both editor-only part and build part, we need to separate them for project to build without issues. <br></br>
Also, do not forget to derive your custom node from **Node**
```csharp
public class MyNode : Node
```

### OnDraw abstract method (editor-only)
OnDraw is an abstract method of Node class that is being used for drawing the body of the node, it's better to do with built-in API, but nothing stops you
from writing your own fields

```csharp
namespace NodeEditor.Nodes.NPC
{
    public class MyNode : Node
    {
#if UNITY_EDITOR
        public override void OnDraw()
        {
           
        }
#endif
    }
}
```

Since OnDraw uses editor-only features to draw its UI, we need to wrap this method with preprocessor directives: 

```csharp
#if UNITY_EDITOR
    //content
#endif
```

This tells Unity to not include this part of code in an actual build of project

## OnInitialize virtual method (editor-only)
OnInitialize method is something like Start method in Monobehaviour. It's 
called once on node creation or on NodeGraph opening (workaround for fields that are not serializable).
```csharp
namespace NodeEditor.Nodes.NPC
{
    public class MyNode : Node
    {
        private RealyCoolClass _swagg;
#if UNITY_EDITOR
        public override void OnInitialize()
        {
           _swagg = new();
        }
#endif
    }
}
```

## Node configuration
At the moment, you can configure, width, color, and name (label) of the node.
All the configuration tweaks happens in OnInitialize method. You can tweak config in runtime though, 
but I'm not sure why would you do this.
Height is being calculated automatically.

```csharp
public override void OnInitialize()
{
    Width = 260f;
    nodeName = "Very Smart And Kind Node";
    backgroundColor = BackgroundColor.Green;
}
```

By default, width is matched with label width, nodeName is matched with class name, backgroundColor is gray
