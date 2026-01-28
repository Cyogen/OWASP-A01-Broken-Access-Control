```mermaid
flowchart TB
    Start([Start])
    A[Alert / Detection Source]
    PD1{Identify Access Attempt}
    PD2{Authenticated?}
    PD3{Authorized for Resource?}
    DC1[Data Collection]
    CAT[Categorize Activity]
    TRI[Triage & Impact Analysis]
    FP{False Positive?}
    ESC[Escalate to Tier 2 / AppSec]
    STOP([Close / Document])

    Start --> A --> PD1 --> PD2
    PD2 -- No --> DC1 --> CAT --> TRI --> FP
    PD2 -- Yes --> PD3
    PD3 -- Yes --> STOP
    PD3 -- No --> DC1
    FP -- Yes --> STOP
    FP -- No --> ESC
