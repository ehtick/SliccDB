# SliccDB
![C#](https://img.shields.io/badge/c%23-%23239120.svg?style=for-the-badge&logo=c-sharp&logoColor=white) ![.Net](https://img.shields.io/badge/.NET-5C2D91?style=for-the-badge&logo=.net&logoColor=white) ![Visual Studio](https://img.shields.io/badge/Visual%20Studio-5C2D91.svg?style=for-the-badge&logo=visual-studio&logoColor=white)
![Version](https://img.shields.io/badge/Version-0.0.1-brightgreen?style=for-the-badge)
### Light Embedded Graph Database for .net with support (in progress) for OpenCypher Query Language 
<img src="/SliccDB.Explorer/Res/SLICC_500.png" width="200" height="200" align="right">

#### What is it?
Think of it like SQLite for graph databases. There were some efforts to build something like this (such as Graph Engine) but Microsoft decided to abandon all of their graph database related projects. Be aware that I have no intention to make this some sort of Neo4J .net Clone. It will be not fully compliant with features of mentioned database. I intend on integrating Cypher, because it is an ISO standard GQL (Graph Query Language). More info and resources about cypher can be found on their site (http://opencypher.org/) 

#### Examples
Here are some simple examples:

##### Create Node Programatically 

```Csharp
DatabaseConnection connection = new DatabaseConnection("filename");
var properties = new Dictionary<string, string>();
var labels = new HashSet<string>();
properties.Add("Name", "Alice");
labels.Add("Person");

var nodeOne = connection.CreateNode(properties, labels);
properties.Clear();
labels.Clear();
properties.Add("Name", "Steve");
labels.Add("Person");

var nodeTwo = connection.CreateNode(properties, labels);
```

##### Create Relations between two nodes
Note that relations also have ability to hold data like Properties and Labels
```Csharp
var properties = new Dictionary<string, string>();
var labels = new HashSet<string>();
properties.Add("How Much", "Very Much");
labels.Add("Label on a node!");
connection.CreateRelation(
"Likes", 
sn => sn.First(x => x.Hash == nodeOne.Hash), tn => tn.First(a => a.Hash == nodeTwo.Hash), properties, labels);
```

#### Queries

You can query nodes and relations as you would any collection (With Linq). 

###### Query Nodes

```Csharp
var selectedNode = connection.QueryNodes(x => x.Where(x => x.Properties["Name"] == "Steve").ToList()).First();
Console.WriteLine(selectedNode.Labels.Count);
```

###### Query Relations

```Csharp
var selectedEdge = connection.QueryRelations(x => x.Where(x => x.RelationName == s).ToList()).First();
Console.WriteLine(selectedEdge.Labels.Count);
```
###### Cypher Interpreter
Although it is not fully implemented, you can use some of cypher commands. For detailed info about current features see [Progress](#Progress)

```Csharp
CypherInterpreter interpreter = new CypherInterpreter(connection);
Directory.CreateDirectory(Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location) + "\\temp\\");
File.WriteAllText(Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location) + "\\temp\\cypherstring.cyp", "MATCH(n) RETURN n";
var result = interpreter.Interpret(Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location) + "\\temp\\cypherstring.cyp");           
```
Result is of type "QueryResult". It returns node and relationship collection.

#### Why it exists?
I just need embedded Graph Database Solution that supports Cypher Language.
Graph DBMS is a perfect solution for some gamedev ai-related stuff (Procedural Behaviour Design, Perception and context awareness storage), but every solution needs a server. Imagine that in order to play your game you must install Neo4J's server in the first place, or every time your npc makes a decision it queries Azure Cloud to check how much he likes pizza. It's unreliable and forces you to host a game server for the entirety of a game's lifetime even though your game is singleplayer. Sounds stupid? Well, ask EA about Simcity 2013 - they thought it's a great idea! But really, Graph DBMS has other use cases. [Watch this video to know more about Graph DBMS](https://www.youtube.com/watch?v=GekQqFZm7mA)

#### DB Explorer
![DB Explorer Screenshot](/screenshots/Explorer.png)\
DB Explorer is a companion app currently used for interaction with database. It allows for CRUD operations on the database to test its' functionalities. 
The app is still in development.

#### Progress
Below you'll find a list of features that will be included in the 1st milestone release of SliccDB:
- [x] Basic Structures (Relations, Nodes)
- [x] Serialization (Reading and Writing)
- [x] Basic Debugger App (Ability to add, remove, edit nodes and relations)
- [x] Antlr-Generated OpenCypher Parser, Lexer and ListenerBase
- [x] CREATE Clause
- [x] MATCH Clause
- [ ] WHERE Clause
- [x] RETURN Clause
- [ ] MERGE Clause

#### Can I help?
Of course! Any help with the Cypher Interpreter will be appreciated. Currently I use [Raiload Diagrams](https://s3.amazonaws.com/artifacts.opencypher.org/M16/railroad/Cypher.html) from official OpenCypher website to look for lexems and then for every pattern I walk the tree and extract what is needed. This architecture while simple, is not optimal and it would be far better if I were to build some kind of a command object inside every block and based on that operated on the database. 
