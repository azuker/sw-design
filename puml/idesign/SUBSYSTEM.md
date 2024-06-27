# Subsystems

## Examples

### Basic

```plantuml
@startuml

title File Management Subsystem

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//subsystem.puml

Subsystem() {
    TierClients() {
        Client(c1, "WebAPI")
        Client(c2, "Timer")
        Util(c3, "Message Bus")

        Link(c1, c2)
        Link(c2, c3)
    }

    TierManagers() {
        Manager(m1, "Collection")
        Manager(m2, "Stream")
        Manager(m3, "Sanitation")
        Manager(m4, "Housekeeping")

        Link(m1, m2)
        Link(m2, m3)
        Link(m3, m4)
    }

    TierEngines() {
        Engine(e1, "Analyze")
        Engine(e2, "Produce")
        
        Link(e1, e2)
    }

    TierAccessors() {
        Accessor(a1, "Files")
        Accessor(a2, "Blobs")
        Accessor(a3, "Sanitizer")
        
        Link(a1, a2)
        Link(a2, a3)
    }

    TierResources() {
        Db(r1, "Containers")
        Resource(r2, "Blob Storage")
        Resource(r3, "Sanitizer")

        Link(r1, r2)
        Link(r2, r3)
    }
}

TierUtils() {
    Util(u1, "Message Bus")
    Util(u2, "Files")
    Util(u3, "WebPush")

    LinkUtil(u1, u2)
    LinkUtil(u2, u3)
}

@enduml
```
