# Azure API Management Networking

### External API Management and External APIs

```mermaid
graph LR;
    A((Internet))--Gateway<br/>Portal-->B(APIM)
    B--Service Tags-->C[App Services<br/>Azure Functions]
    B-->D[AppGW];
    D-->F[VM];
    B-->E[Vendor API];
    style A fill:#007FFF,stroke:#333,stroke-width:1px,color:#fff;    
    classDef someclass fill:#4DFF4D,stroke:#333,stroke-width:1px,color:black;
    class B,D,E,F someclass;
    classDef someclass1 fill:#f96,color:black;
    class C someclass1;
```

Azure Services:
- API Management in External Mode
- Depending on how API are exposed:
  - Azure App Services
  - Azure VMs
  - Etc.

Security:
- No WAF (Recommended)
- No DDOS (Recommended)
- Communication from APIM to App Services and Functions could leverage Service Tags


### Application Gateway, External API Management and External APIs

```mermaid
graph LR;
    A((Internet))-->Z[AppG<br/>WAF];
    Z--Gateway<br/>Portal<br/>Service Tags-->B(APIM);
    B--Service Tags-->C[App Services<br/>Azure Functions]
    B-->D[AppGw];
    D-->F[VM];
    B-->E[Vendor API];
    style A fill:#007FFF,stroke:#333,stroke-width:1px,color:#fff;    
    classDef someclass fill:#4DFF4D,stroke:#333,stroke-width:1px,color:black;
    class Z,D,E,F someclass;
    classDef someclass1 fill:#f96,color:black;
    class C,B someclass1;
```

### Internal with internal and external services

### Internal with AKS internal
