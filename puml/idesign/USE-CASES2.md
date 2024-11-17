# Core Use-cases

## Activity Diagrams

### Add Tradesman / Contractor

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//usecases.puml

title Add Tradesman / Contractor

start
  :Apply for membership;
  :Verify application;
  if () then (Member exists)
    Error()
  else (Valid)
    :Process payment;
    :Approve;
  endif
stop

@enduml
```

### Request Tradesman

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//usecases.puml

title Request Tradesman

start
  :Request tradesman;
  :Verify request;
  if () then (Not allowed)
    Error()
  else (OK)
    :Match tradesman;
  endif
stop

@enduml
```

### Match Tradesman

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//usecases.puml

title Match Tradesman

start
  :Request match;
  :Verify request;
  if () then (Not allowed)
    Error()
  else (OK)
    :Analyze project needs;
    :Search candidates;
    :Assign best fit;
  endif
stop

@enduml
```

### Assign Tradesman

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//usecases.puml

title Assign Tradesman

start
  :Request assignment;
  :Verify request;
  if () then (Not allowed)
    label error_space
    label error_space
    label error_space
    label error_space
    label error
    Error()
  else (OK)
    :Verify availability;
    if () then (Not available)
      label sp_lab4
      goto error
    else (OK)
      :Assign to project;
    endif
  endif
stop

@enduml
```

### Terminate Tradesman

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//usecases.puml

title Terminate Tradesman

start
  :Request termination;
  :Verify request;
  if () then (Not allowed)
    Error()
  else (OK)
    :Terminate from active projects;
    :Make tradesman available;
  endif
stop

@enduml
```

### Pay Tradesman

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!include ThemePath//usecases-icons.puml
!include ThemePath//usecases.puml

title Pay Tradesman

start
  fork
    :Find scheduled payments;
  fork again
    IconTimer()
  end fork {Scheduled Time >= Time}

  :Pay tradesman;
stop

@enduml
```

### Create Project

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//usecases.puml

title Create Project

start
  :Request create project;
  :Verify request;
  if () then (Not allowed)
    Error()
  else (OK)
    :Add required trades, skills,\nbill rates, location, duration, etc.;
    :Activate project;
  endif
stop

@enduml
```

### Close Project

```plantuml
@startuml

!define ThemePath https://azuker.github.io/sw-design/puml/theme
!includeurl ThemePath//usecases.puml

title Close Project

start
  :Request close project;
  :Verify request;
  if () then (Not allowed)
    Error()
  else (OK)
    :Release tradesmen;
    :Close project;
  endif
stop

@enduml
```
