# IDesign Method Diagram Examples with PlantUML

You can use the PlantUML template from: `https://azuker.github.io/sw-design/puml/theme/idesign.puml`.

## VS Code Setup

You can use VSCode to visualize the documentation and diagrams.
Install the following:

1. [Java](https://java.com/en/download/)
1. [Graphviz](http://www.graphviz.org/download/)
1. VSCode Extension: `Markdown Preview Enhanced`
1. VSCode Extension: `PlantUML`

To preview a `.md` markdown file, use the top right icon button to open the markdown enhanced preview.
Alternatively, the keyboard shortcut `Ctrl+K, V` should open it as well.

__Note__: you can configure the Markdown Preview Enhanced extension to display content in a theme of your preference (e.g. dark theme) 

## Legend

### Shapes, Annotations and Colors

- Clients use `Client` template block or `#lawngreen` color
- Managers use `Manager` template block or `#yellow` color
- Engines use `Engine` template block or `#orange` color
- Accessors use `Accessor` template block or `#lightgray` color
- Resources use `Resource` template block or `#aqua` color
- Database resources use `Db` template block or `#aqua` color
- Utilities use `Util` template block or `#violet` color
- Agents use `Agent` template block or `#moccasin` color
- Gateways use `Gateway` template block or `#goldenrod` color

```plantuml
@startuml

!includeurl https://azuker.github.io/sw-design/puml/theme/idesign.puml

title Colors and Shapes

Client(c1, "Client")
Manager(m1, "Manager")
Engine(e1, "Engine")
Accessor(a1, "Accessor")
Util(u1, "Utility")
Agent(g1, "Agent")
Gateway(g2, "Gateway")
Db(r1, "Resource\n(Database)")
Resource(r2, "Resource\n(Service)")

c1 --[hidden]> m1
m1 -[hidden]> e1
e1 -[hidden]> a1
m1 --[hidden]> u1
u1 --[hidden]> g1
g1 -[hidden]> g2
g1 --[hidden]> r1
r1 -[hidden]> r2

@enduml
```

### Call Notations

**Synchronous calls** have continuous (non-dashed) arrows, normally downwards. Additionally, this includes sequential calls that might still use asynchronous I/O:

```plantuml
@startuml
!includeurl https://azuker.github.io/sw-design/puml/theme/idesign.puml
title Downwards Call

Manager(m1, "Manager")
Accessor(a1, "Accessor")
Db(r1, "Resource\n(Database)")

m1 --> a1
a1 --> r1

@enduml
```

**Asynchronous calls** have dashed arrows, normally sideways:

```plantuml
@startuml
!includeurl https://azuker.github.io/sw-design/puml/theme/idesign.puml
title Sideways Call

Manager(m2, "Manager")
Manager(m3, "Manager")

Manager(m1, "Manager")
Util(u1, "Utility")

m1 .> u1
m1 --[hidden]> m2
m2 .> m3

@enduml
```
