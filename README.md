```mermaid
sequenceDiagram
    Client->>DNS:Request xxx.trafficmanager.net
    DNS->>Traffic Manager:Request xxx.trafficmanager.net
    loop Health Probe
        Traffic Manager-->>FW Endpoint 1: Probe port 587
        FW Endpoint 1-->>Server 1: port 587
        Traffic Manager-->>FW Endpoint 2: Probe port 587
        FW Endpoint 2-->>Server 2: port 587
    end
    Traffic Manager->>DNS:Reply CNAME server1.domain.com
    DNS->>Client: CNAME server1.domain.com
    Client->>DNS:Request A records for server1.domain.com
    DNS->>Client:Reply A Records for server1.domain.com (1.2.3.4)
    Client->>FW Endpoint 1:Connect SMTP
    FW Endpoint 1->>Server 1:Connect SMTP
    Server 1->>FW Endpoint 1:Reply SMTP - Banner: server1.domain.com
    FW Endpoint 1->>Client:Reply SMTP - Banner: server1.domain.com
    loop Check validity of remote host
        Client->>DNS: Request PTR 1.2.3.4
        DNS->>Client: Reply 4.3.2.1.in-addr.arpa (must be server1.domain.com)
        Client->>DNS: Request A record for Banner server1.domain.com
        DNS->>Client: Reply 1.2.3.4
    end
    critical Check validity of remote host
        Client->>DNS: Request PTR 1.2.3.4
        DNS->>Client: Reply 4.3.2.1.in-addr.arpa (must be server1.domain.com)
        Client->>DNS: Request A record for Banner server1.domain.com
        DNS->>Client: Reply 1.2.3.4
    option Failed validation
        Client-->>Client: Stop
    end
    Client->>FW Endpoint 1:Deliver E-mail
    FW Endpoint 1->>Server 1:Deliver E-mail
    

