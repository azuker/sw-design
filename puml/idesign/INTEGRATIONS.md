# Integrations

## Examples

### Basic

- Can included end-to-end flows across managers and subsystems to depict broader flows
- Often, focusing on managers alone could be enough
    - Less clutter, less coupling, etc.
    - Might even include only a subsystem

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//idesign.puml

title E2E Flows

Client("c1", "WebApi")
Resource("r1", "Alert Discovery")

Client("c2", "WebApi")
Util("u2", "Message Bus")
Manager("m1", "Ingestion")
Engine("e1", "Expiry")
Util("u1", "Message Bus")
Resource("r2", "Alert Discovery")

Client("c3", "WebApi")
Manager("m3", "Ingestion")
Util("u3", "Message Bus")
Resource("r3", "Alert Discovery")

c3 --> m3: Remove Observables
u2 --> m1: Register Observables (trig: enrichment)
c1 --> r1: create alert
c2 --> m1: Register Observables
r1 -> m1: Register Observables\nreturn ids, AD updates links
m1 --> e1: Refresh Enrichments
u1 <. e1: evt:Refresh Required
m1 ..> u1: evt: Observables Registered\nevt: Observables Enriched
r2 <- u1: AD updates links, score, visited
m3 ...> u3: evt: Observables Removed
r3 <- u3: AD updates links, score, visited

@enduml
```
