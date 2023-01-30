# Azure API Management Networking

## API Management configuration in order of improved security

### Default API Management Deployment and External APIs

```mermaid
graph LR;
    A((Internet))--Gateway<br/>Portal-->B(APIM)
    B--Service Tags-->C[App Services<br/>Azure Functions]
    B-->D[AppGW];
    D-->F[VM];
    B--Ingress-->E[AKS/ACA];
    B-->G[Vendor API]
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


### External API Management Deployment and External and Internal APIs

```mermaid
graph LR;
    A((Internet))--Gateway<br/>Portal-->B(APIM)
    B--Service Tags-->C[App Services<br/>Azure Functions]
    B-->D[ILB];
    D-->F[VMSS];
    B--Internal<br/>Ingress-->E[AKS/ACA];
    B-->G[Vendor API]
    style A fill:#007FFF,stroke:#333,stroke-width:1px,color:#fff;    
    classDef unsafe fill:#4DFF4D,stroke:#333,stroke-width:1px,color:black;
    class X unsafe;
    classDef safe fill:red,color:black;
    class C,Z,B,D,F,E,M safe;
```

Azure Services:
- VNET
- AppGW
- API Management in External Mode
- Depending on how API are exposed:
  - Azure App Services
  - Azure VMs  
  - Private Endpoints
  - Etc.

Security:
- No WAF
- No DDOS
- APIM deployed to VNET subnet obtains private IP
- Communication to APIs can be over private IPs to internal services or public endpoints

### Application Gateway, Internal API Management, Internal and External APIs

```mermaid
graph LR;
    A((Internet))--DDOS-->X((Public<br/>IP));
    X-->Z;
    Z[AppG<br/>WAF];
    Z--Gateway<br/>Portal-->B(APIM);
    B-->M[Private<br/>Endpoint];
    M-->C[App Services<br/>Azure Functions]
    B-->D[ILB];
    D-->F[VMSS];
    B--Internal<br/>Ingress-->E[AKS/ACA];
    B-->G[Vendor API]
    style A fill:#007FFF,stroke:#333,stroke-width:1px,color:#fff;    
    classDef unsafe fill:#4DFF4D,stroke:#333,stroke-width:1px,color:black;
    class X unsafe;
    classDef safe fill:red,color:black;
    class C,Z,B,D,F,E,M safe;
```

Azure Services:
- Public IP
- VNET
- AppGW
- API Management in External Mode
- Depending on how API are exposed:
  - Azure App Services
  - Azure VMs  
  - Private Endpoints
  - Etc.

Security:
- DDOS on Public IP
- WAF on AppGW and can do SSL offloading or end-to-end encryption
- APIM deployed to VNET subnet obtains private IP
- Communication to APIs can be over private IPs to internal services or public endpoints

### Reference

- [API Management Networking](https://learn.microsoft.com/en-us/azure/api-management/virtual-network-concepts?tabs=stv2)
