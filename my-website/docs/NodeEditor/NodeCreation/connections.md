---
sidebar_position: 2
---
# Connections (editor-only)
Connection are tools for connecting nodes to each other. There are two types of connections,
ConnectionIn and ConnectionOut. Their names speak for themselves, ConnectionIn is
created to get info from ConnectionOut.
<br></br>
Currently, since connections are fully editor-only, info from connection is needed to be
stored in separated field, I'll try to change it in newer versions.
<br></br> You have two ways of creating connections. The first one is via
AddConnection(ref ConnectionIn conn). **_Keep in mind that this method only works inside OnInitialize method_**
```csharp
namespace NodeEditor.Nodes.NPC
{
    public class StartingNode : Node
    {
        public NpcSpeechNode startingSpeech;
#if UNITY_EDITOR
        [SerializeField] private ConnectionIn startingConn;

        public override void OnInitialize()
        {
            AddConnection<NpcSpeechNode>(ref startingConn);
        }
        public override void OnDraw()
        {
            EditorGUILayout.LabelField("Starting node", propertyLabelStyle);
            startingSpeech = (NpcSpeechNode)startingConn.Draw();
        }
#endif
    }
}
```
Connections with direction out are created a little bit differently, you need to specify getter as a parameter.  
```csharp
AddConnection<AnswerNode>(ref SelfReference, () => this);  
```
SelfRefence is built in connection in Node class, that is being drawn on node label (ConnectionOut only).


You can see the **_Draw()_** method of connection. This method return an object,
that can be converted to the type you specified to this connection, basically, 
this method returns value of connection. 
<br></br>
Draw method must be called after the field you want to place next to.
Draw method will be drawn to the right of the field (if ConnectionOut) or to the left (if ConnectionIn).
# Second way, AddConnectionIn|Out
AddConnectionIn|Out is used to instantiate dynamic connections at runtime. Example:

```csharp
namespace NodeEditor.Nodes.NPC
{
    public class AnswerNode : Node
    {
        public List<string> answers = new();
        public List<NpcSpeechNode> nextNodes = new();
#if UNITY_EDITOR
        public override void OnInitialize()
        {
            nodeName = "Answer Node";
            Size = new Vector2(200, Styles.NodeLabelHeight + Styles.LabelHeight * 2 + 30f);
            AddConnection<AnswerNode>(ref SelfReference, () => this);
        }
        public override void OnDraw()
        {
            for (int i = 0; i < answers.Count; i++)
            {
                var textValue = answers[i];
                DrawTextField("Answer", ref textValue);

                var newNextNode = (NpcSpeechNode)DynamicConnectionIns[i].Draw();

                answers[i] = textValue;
                nextNodes[i] = newNextNode;

                Utils.DrawSeparator();
            }
            DrawButtons();
        }
        private void DrawButtons()
        {
            if (GUILayout.Button(new GUIContent("Add New"), GUILayout.Height(Styles.LabelHeight)))
            {
                answers.Add(null);
                nextNodes.Add(null);
                AddConnectionIn<NpcSpeechNode>();
                RecalculateHeight();
            }
            if (answers.Count > 0 && GUILayout.Button(new GUIContent("Remove Last"), GUILayout.Height(Styles.LabelHeight)))
            {
                answers.RemoveAt(answers.Count - 1);
                nextNodes.RemoveAt(nextNodes.Count - 1);
                RemoveLastDynamicConnectionIn();
                RecalculateHeight();
            }
        }
#endif
    }
}
```

This code creates and removes ConnectionIns when user clicks one of the buttons

