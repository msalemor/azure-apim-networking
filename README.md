# Azure API Management Networking and Security

## Overview

## API Management Networking Modes

- Default: Not deployed to VNET, exposes public gateway and portal, and can acccess external APIs only
- External: Deployed to VNET, exposes public gateway and portal, and can access internal and external APIs
- Internal: Deployed to VNET, expoese private gateway and portal, and can access internal and external APIs

## API Management configuration in order of improved security

### API Management Components

```mermaid
graph LR;
  APPS(Apps<br/>Devices)<-->GT;
  DEVS(App<br/>Developers)<-->PT;
  APP(API Providers)-->MG;
  GT<-->APIS(APIs);
  subgraph SG1["APIM"]
    MG(Management);
    MG-->GT;
    PT(Portal)<-->MG;
    GT(Gateway);
  end;
  classDef internet fill:#007FFF,color:white;
  classDef unsafe fill:#ff3333,color:white;
  classDef semisafe fill:darkorange,color:black;
  classDef safe fill:purple,color:white;
  classDef text fill:black,color:white;
  class APPS,DEVS internet;
  class GT,PT,MG safe;
  class APIS semisafe;
  class APP text;
```

### Default API Management Deployment and External APIs

```mermaid
graph LR;
  A((Internet))-->B(APIM);
  B--Service Tags-->C[App Services<br/>or<br/>Azure Functions]
  B-->D[AppGW];
  D-->F[VM];
  B--Ingress-->E[AKS/ACA];
  B-->G[Vendor API]
  classDef internet fill:#007FFF,color:white;
  classDef unsafe fill:#ff3333,color:white;
  classDef semisafe fill:darkorange,color:black;
  classDef safe fill:darkgreen,color:white;
  class A internet;
  class C,D,F,E safe;  
  class B,G semisafe;
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
- Traffic from API to APIs flows over the Internet


### External API Management Deployment and External and Internal APIs

```mermaid
graph LR;
  A((Internet))-->B;
  subgraph "APIM Subnet"
    B(APIM)
  end;
  B--Service Tags-->C(App Services<br/>Azure Functions);
  B-->D[ILB];
  D-->F[VMSS];
  B--Internal<br/>Ingress-->E[AKS/ACA];
  B-->G[Vendor API];
  C-->H[Azure SQL];
  F-->H;
  E-->H;
  classDef internet fill:#007FFF,color:white;
  classDef unsafe fill:#ff3333,color:white;
  classDef semisafe fill:darkorange,color:black;
  classDef safe fill:darkgreen,color:white;
  class A internet;
  class B,C,D,F,E safe;
  class G semisafe;
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
- Traffic from internet to Azure backed APIs flows all internally over the Azure backbone

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
    B-->G[ASE];
    B-->H[Vendor API]
    style A fill:#007FFF,stroke:#333,stroke-width:1px,color:#fff;    
    classDef unsafe fill:#4DFF4D,stroke:#333,stroke-width:1px,color:black;
    class X unsafe;
    classDef safe fill:red,color:black;
    class C,Z,B,D,F,E,M,G safe;
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
- Internal or external access to the APIs over the AppGw
- Communication to APIs can be over private IPs to internal services or public endpoints
- Traffic from internet to Azure backed APIs flows all internally over the Azure backbone

### Reference

- [API Management Networking](https://learn.microsoft.com/en-us/azure/api-management/virtual-network-concepts?tabs=stv2)
