```mermaid
graph TB
    Start([API Key Lifecycle Start])
    
    subgraph Registration["1. REGISTRATION & KEY GENERATION"]
        A[User/Client Registers] --> B[System Validates Registration]
        B --> C{Valid Registration?}
        C -->|No| D[Return Error]
        C -->|Yes| E[Generate Unique API Key]
        E --> F[Hash API Key]
        F --> G[Store Hashed Key in Database]
        G --> H[Associate Key with User Account]
        H --> I[Set Key Metadata]
        I --> J[Creation Date, Expiry, Permissions, Rate Limits]
        J --> K[Return API Key to User]
        K --> L[User Stores Key Securely]
    end
    
    subgraph Authentication["2. API REQUEST AUTHENTICATION"]
        M[Client Makes API Request] --> N[Include API Key in Request]
        N --> O{Key Location?}
        O -->|Header| P[X-API-Key or Authorization Header]
        O -->|Query Param| Q[?api_key=xxx]
        O -->|Body| R[JSON Body Field]
        P --> S[Server Receives Request]
        Q --> S
        R --> S
        S --> T{API Key Present?}
        T -->|No| U[Return 401 Unauthorized]
        T -->|Yes| V[Extract API Key]
        V --> W[Hash Received Key]
        W --> X{Key Exists in DB?}
        X -->|No| Y[Return 403 Forbidden]
        X -->|Yes| Z[Retrieve Key Metadata]
    end
    
    subgraph Validation["3. KEY VALIDATION & AUTHORIZATION"]
        Z --> AA{Key Expired?}
        AA -->|Yes| AB[Return 401 Key Expired]
        AA -->|No| AC{Key Revoked?}
        AC -->|Yes| AD[Return 403 Key Revoked]
        AC -->|No| AE{Rate Limit Exceeded?}
        AE -->|Yes| AF[Return 429 Too Many Requests]
        AE -->|No| AG{Has Required Permissions?}
        AG -->|No| AH[Return 403 Insufficient Permissions]
        AG -->|Yes| AI[Update Last Used Timestamp]
        AI --> AJ[Increment Usage Counter]
        AJ --> AK[Log Request]
        AK --> AL[Process API Request]
        AL --> AM[Return 200 Success with Data]
    end
    
    subgraph Monitoring["4. MONITORING & MAINTENANCE"]
        AN[Background Monitoring Service]
        AN --> AO[Check Key Usage Patterns]
        AO --> AP{Suspicious Activity?}
        AP -->|Yes| AQ[Trigger Security Alert]
        AQ --> AR[Notify Administrator]
        AR --> AS[Potentially Auto-Revoke Key]
        AP -->|No| AT[Continue Monitoring]
        
        AU[Check Key Expiration] --> AV{Near Expiry?}
        AV -->|Yes| AW[Send Renewal Notification]
        AV -->|No| AX[Continue Monitoring]
        
        AY[Generate Usage Reports] --> AZ[Track API Calls, Errors, Performance]
    end
    
    subgraph Rotation["5. KEY ROTATION"]
        BA[User Requests New Key] --> BB[Generate New API Key]
        BB --> BC[Hash New Key]
        BC --> BD[Store New Key in Database]
        BD --> BE[Mark Old Key for Deprecation]
        BE --> BF[Set Grace Period]
        BF --> BG[User Updates Applications with New Key]
        BG --> BH{Grace Period Ended?}
        BH -->|No| BI[Both Keys Active]
        BH -->|Yes| BJ[Deactivate Old Key]
    end
    
    subgraph Revocation["6. KEY REVOCATION"]
        CA[Revocation Trigger] --> CB{Trigger Source?}
        CB -->|User Request| CC[User Initiates Revocation]
        CB -->|Security Alert| CD[System Auto-Revokes]
        CB -->|Admin Action| CE[Administrator Revokes]
        CC --> CF[Mark Key as Revoked in DB]
        CD --> CF
        CE --> CF
        CF --> CG[Set Revocation Timestamp]
        CG --> CH[Log Revocation Event]
        CH --> CI[Notify User]
        CI --> CJ[All Future Requests Denied]
    end
    
    subgraph Expiration["7. KEY EXPIRATION"]
        DA[Scheduled Expiration Check] --> DB{Key Expired?}
        DB -->|Yes| DC[Mark Key as Expired]
        DC --> DD[Log Expiration Event]
        DD --> DE[Send Expiration Notice]
        DE --> DF[User Must Request New Key]
        DB -->|No| DG[Key Remains Active]
    end
    
    subgraph Deletion["8. KEY DELETION"]
        EA[Account Closure or Final Cleanup] --> EB[Mark Key for Deletion]
        EB --> EC[Archive Key History]
        EC --> ED[Remove Key from Active Database]
        ED --> EE[Retain Audit Logs]
        EE --> EF[Complete Deletion After Retention Period]
    end
    
    Start --> Registration
    L --> Authentication
    AM --> Monitoring
    AT --> Rotation
    AX --> Rotation
    BI --> Authentication
    BJ --> Authentication
    CJ --> Expiration
    DG --> Authentication
    DF --> Registration
    EF --> End([Lifecycle Complete])
    
    style Registration fill:#e1f5ff
    style Authentication fill:#fff4e1
    style Validation fill:#e8f5e9
    style Monitoring fill:#f3e5f5
    style Rotation fill:#fff9c4
    style Revocation fill:#ffebee
    style Expiration fill:#fce4ec
    style Deletion fill:#eceff1