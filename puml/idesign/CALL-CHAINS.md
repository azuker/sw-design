# Call Chains

## Examples

### Basic

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//idesign.puml

title Discover Source Packages

Client("c1", "WebApi")
Manager("m1", "Accounting Source Discovery")
Accessor("a1", "Rules")
Accessor("a2", "Packages")

c1 --> m1
m1 --> a1
m1 --> a2

@enduml
```

With more blocks + sync/async:

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//idesign.puml

title Discover Source Packages

Client("c1", "WebApi")
Client("c2", "Command")
Manager("m1", "Accounting Source Discovery")
Manager("m2", "Notification")
Engine("e1", "Validation")
Accessor("a1", "Rules")
Accessor("a2", "Packages")
Db("r1", "Metadata")
Resource("r2", "Libs")
Util("u1", "Security")

c1 --> m1
m1 .> m2
m1 --> e1
m1 ---> a1
m1 ---> a2
a1 --> r1
a2 --> r2

@enduml
```

#### Design Validation

You should validate the architecture with swim-lanes or sequence diagrams to prove its validity by materializing the dynamic view of the system.

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//sequence.puml

title Register Campaign
skinparam backgroundColor #FFFFFE

Client("c1", "WebApi")
Manager("m1", "Delivery Manager")
Engine("e1", "Template")
Accessor("a1", "Rules")
Util("u1", "Message Bus")

autonumber
autoactivate on

c1 -> m1: Register campaign
    m1 -> e1: Link template
        e1 -> a1: Configure rules
            return metadata
        return template
    m1 --> u1: Campaign changed

@enduml
```

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//swimlane.puml

title Register Campaign

Manager("m1", "Delivery Manager")
Engine("e1", "Template Engine")
Accessor("a1", "Rules Accessor")

|m1|
start
    :Add metadata;
    |e1|
    :Link to a template;
    |a1|
    :Configure rules;
    |m1|
    :Configure schedule;
    #violet:Campaign Changed>
stop
@enduml
```

### Advanced Notes

- **IMPORTANTLY,** the basic can often be enough and generally better
    - Further instructions you see here around adding more information which isn't high-level call-chain flow is optional
        - E.g., order, question marks, arrow texts, arrow technical terms, conditional ordinals, etc.
    - Often, such detailed diagrams can create clutter and impair readability and understandability
        - Plus, can become "too specific", meaning every little change might require updating the diagrams
        - More design, less architecture
    - Consider alternatives
        - Seperate the more detailed diagram, although there are duplications this way
        - Can incorporate it as a separate design process as well, more of a detail design
        - Provide additional details through other means, such as sequence and swim lane diagrams
    - If you add the specifications then perhaps it can minimize the need for such a detailed diagram (see spec further below)
    - Depends on the hand-off point as well

### Advanced

- Call-chains can instruct technical terms
    - Diagram title - use-case operation name on the manager (the function name)
    - Arrow text - operation name
    - When text is only descriptive - use all lowercase
    - When text reflects the operation name - UsePascalCaseWithNoSpaces

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//idesign.puml

title Copy File To Archive

Client("c1", "Notification")
Manager("m1", "Accounting Source Discovery")
Accessor("a1", "External Packages")
Accessor("a2", "Packages")

c1 --> m1: CopyFileToArchive
m1 --> a1: ReadPackageContents
m1 --> a2: UploadPackage

@enduml
```

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//idesign.puml

title Preprocess Transaction Group

Client("c1", "Notification")
Manager("m1", "Accounting Ingestion")
Accessor("a1", "Transactions")
Engine("e1", "Validation")

c1 --> m1
m1 --> e1: 2. validate
m1 ---> a1: 1. read transactions


@enduml
```

- Can use subsystem prefix to reflect subsystems (`subsystem::Name`)
    - Can do so on use case operation name or in components.

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//idesign.puml

title methodology::IngestAiEvent

Client("c1", "WebApi")
Util("c2", "Message Bus")
Manager("m1", "Ingestion")
Engine("e1", "Normalize")
Accessor("a1", "ProcessingRules")
Resource("r1", "Model Training Ext. System")
Db("r2", "Rules Storage")

c1 --> m1
c2 --> m1
m1 --> e1
m1 ---> a1
a1 --> r2
a1 --> r1

@enduml
```

- Can reflect Bus semantics - events/commands/rpc
    - Use prefix to reflect Bus semantics (`evt:` / `cmd:` / `delayed:`)
- In case of commands - can include the target (should be known)
    - The command is written on the arrow that connects to the target
    - The arrow text can instruct the use-case operation name
- In case of events - can leave out targets, many subscribers can exist
    - If you know of a specific subscriber component then you can include it
    - The event is written on the arrow that connects to the message bus
    - The arrow text can instruct the event payload/topic name
- You are not supposed to break down the call-chains of the targets
    - Exception to the rule: triggering oneself - you can choose to do it

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//idesign.puml

title accounting::Discover Accounting Sources

Client("c1", "Timer")
Manager("m1", "Accounting Source Discovery")
Manager("m2", "Accounting Source Discovery")
Manager("m3", "exchange::Response")
Accessor("a1", "External Packages")
Accessor("a2", "Packages")
Util("u1", "Message Bus")
Util("u2", "Message Bus")

c1 --> m1
m1 .> u1
u2 <. m1: 5. evt:SourceDiscovered
m1 --> a1: 1. check for new incoming files
m1 --> a2: 2. read already processed file metadata
u1 -> m2: 3. cmd:ProcessIncomingFile
u1 -> m2: 4. cmd:CopyFileToArchive
m3 <- u2

@enduml
```

- Can exclude message bus blocks for brevity sake, a dotted line implies the use of message bus

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//idesign.puml

title Discover Accounting Sources

Client("c1", "Timer")
Manager("m1", "Accounting Source Discovery")
Manager("m2", "Accounting Source Discovery")
Manager("m3", "exchange::Response")
Accessor("a1", "External Packages")
Accessor("a2", "Packages")

c1 --> m1
m1 --> a1: 1. check for new incoming files
m1 --> a2: 2. read already processed file metadata
m1 .> m2: 3. cmd:ProcessIncomingFile
m1 .> m2: 4. cmd:CopyFileToArchive
m3 <. m1: 5. evt:SourceDiscovered

@enduml
```

- Can include multiple names if the design isn't settled on one
    - Do reach a decision when needed though, and update the design accordingly
- Can include a question mark (`?`) to indicate optional flows or components
    - If you are uncertain if such is needed at the moment
    - Do reach a decision when needed though, and update the design accordingly

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//idesign.puml

title Process Incoming File

Client("c1", "Notification")
Manager("m1", "Accounting Source Discovery")
Manager("m2", "Accounting Ingestion")
Manager("m3", "exchange::Response")
Accessor("a1", "External Packages")
Engine("e1", "Formatter/Parser")
Engine("e2", "Persist?")
Accessor("a2", "Accounting Transactions")
Util("u1", "Message Bus")
Util("u2", "Message Bus")

c1 --> m1
m1 --> e1: 2. validate and parse parse
m1 ---> a1: 1. read incoming file
m1 --> e2: 3. persit
e2 --> a2: bulk insert + create tx group
m1 .> u1
u1 -> m2: cmd:PreprocessTransactionGroup
u2 <. m1
m3 <- u2: evt:SourceProcessStatusChanged

@enduml
```

### Multi-Step

- Can represent in a single diagram
- Can use `<ordinal>.<letter>` to represent mutually exclusive flows (one or the other)
- Can use arrow to self if it executes the same exact use-case

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//idesign.puml

title Sanitize

Util(c1, "Message Bus")
Manager(m1, "Sanitation")
Manager(m2, "Sanitation")
Manager(m3, "Sanitation")
Accessor(a2, "Sanitizer")
Accessor(a1, "Files")
Accessor(a3, "Sanitizer")
Accessor(a4, "Containers")
Accessor(a5, "Containers")
Util(u1, "Message Bus")
Util(u2, "Message Bus")

c1 ---> m1: 1. Sanitize
m1 --> a2: 2. ScanAsync
m1 --> a1: 3. UpdateStatus
m1 .> m2: 4. cmd:delayed:TrackSanitation
m2 .> m2: 4.3b. cmd:delayed:TrackSanitation
m3 <. m1: 5. cmd:delayed:TerminateSanitation \n(timeout)
m2 --> a3: 4.1. CheckStatus
m2 --> a4: 4.2. UpdateStatus
m3 --> a5: 5.1. UpdateStatus \n(if not completed)
u1 <.. m2: 4.3a. evt:FileStatusChanged
u2 <.. m3: ?5.2. evt:FileStatusChanged

@enduml
```

- Can break it down

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//idesign.puml

title Sanitize

Util(c1, "Message Bus")
Manager(m1, "Sanitation")
Manager(m2, "Sanitation")
Manager(m3, "Sanitation")
Accessor(a2, "Sanitizer")
Accessor(a1, "Files")

c1 --> m1: 1. Sanitize
m1 --> a2: 2. ScanAsync
m1 --> a1: 3. UpdateStatus
m1 .> m2: 4. cmd:delayed:TrackSanitation
m3 <. m1: 5. cmd:delayed:TerminateSanitation \n(timeout)

@enduml
```

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//idesign.puml

title TerminateSanitation

Util(c1, "Message Bus")
Manager(m1, "Sanitation")
Accessor(a1, "Containers")
Util(u1, "Message Bus")

c1 --> m1: 1. Sanitize
m1 --> a1: 2. UpdateStatus \n(if not completed)
m1 .> u1: ?3. evt:FileStatusChanged

@enduml
```

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//idesign.puml

title TrackSanitation

Util(c1, "Message Bus")
Manager(m1, "Sanitation")
Accessor(a1, "Sanitizer")
Accessor(a2, "Containers")
Util(u1, "Message Bus") 

c1 --> m1: 1. Sanitize
m1 .> m1: 4b. cmd:delayed:TrackSanitation
u1 <. m1: 4a. evt:FileStatusChanged
m1 --> a1: 2. CheckStatus
m1 --> a2: 3. UpdateStatus

@enduml
```

### Use-case Specifications

- Triggers
    - The operations that trigger this use-case
- Functionality
    - The logical doings of this specific use-case operation
    - The things this operation is repsonsible for until its completion at which it returns
    - If it triggers asynchornous use-cases or effects - that is not included
- Effects
    - Asynchronous consequences
    - Commands, events, delayed messaging, etc.
- Can be technical terms (`[subsystem::]PascalCaseName`)
- Can be descriptive text

> **IMPORTANT:** Like before, this can be considered an advanced measure.
> - The specification itself can be considered as an additional design step
> - The specification itself can be considered as design validation, similar to the swimlanes method
> - Regarding all further instructions: optional, like the advanced things before. Use with caution.

---

**Basic:**

<details>
  <summary>Spec</summary>

  - Triggers
    - FE clients and domain systems create new graph entities
    - Device connected interceptor (IoT-hub integration)
    - Device twin interceptor (IoT-hub integration)
  - Functionality
    - Resolves how to reach the desired state
    - Applies data operations (merge-like)
  - Effects
    - Resources changed event
</details>

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//idesign.puml

title Deploy Resources

Client("c1", "WebApi")
Util("c2", "MessageBus")
Manager("m1", "ResourcesManager")
Accessor("a1", "GraphAccessor")

c1 --> m1
c2 --> m1
m1 --> a1

@enduml
```

---

<details>
  <summary>Spec</summary>

  - Triggers
    - Device bootstrap sequence
  - Functionality
    - Returns device default configuration
  - Effects
    - pm::ResourceManager.DeployResources
    - Maintain device information in the IoT platform (state/config/twin/etc.)
        - Or: `pm::DeviceTwinManager.MaintainIotDevice`
</details>

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//idesign.puml

title ec::Register Device

Client("c1", "WebApi")
Manager("m1", "ec::DevicesManager")
Manager("m2", "pm::ResourcesManager")
Manager("m3", "pm::DeviceTwinManager")
Accessor("a1", "ec::DeviceConfigAccessor")

c1 --> m1
m2 <. m1: cmd:DeployResources
m1 .> m3: cmd:MaintainIotDevice
m1 --> a1

@enduml
```

---

<details>
  <summary>Spec</summary>

  - Triggers
    - FE clients and domain systems create new graph entities
    - Device connected interceptor (IoT-hub integration)
    - Device twin interceptor (IoT-hub integration)
  - Functionality
    - Resolves how to reach the desired state
    - Applies data operations (merge-like)
  - Effects
    - evt:ResourcesChanged
</details>

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//idesign.puml

title pm::Deploy Resources

Client("c1", "WebApi")
Util("c2", "MessageBus")
Manager("m1", "pm::ResourcesManager")
Accessor("a1", "pm::GraphAccessor")

c1 --> m1
c2 --> m1
m1 --> a1

@enduml
```

#### Design Validation - More Examples

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//idesign.puml

title Use-case

|#dee4e8|p1|
|#c4c9cd|p2|
|#daf0fe|Backend|

|Backend|
start
    :something;
    if (open) then (yes)
        :generate open data;
        split
            #3f3:Mail to p1\nMAIL ID: **3010**|
            |p1|
            :Receive Mail **3010**;
            detach
        split again
            |Backend|
            #3f3:Mail to p2\nMAIL ID: **3006**|
        end split
        |p2|
        :Receive Mail **3006**;
        detach
    endif
    |Backend|
    #violet:something else>
stop
@enduml
```
