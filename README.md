graph TD
    %% 定义样式
    classDef core fill:#ff9900,stroke:#333,stroke-width:4px,color:white;
    classDef agg fill:#3399ff,stroke:#333,stroke-width:2px,color:white;
    classDef acc fill:#99cc00,stroke:#333,stroke-width:2px;
    classDef srv fill:#ff6666,stroke:#333,stroke-width:2px,color:white;
    classDef fw fill:#cc0000,stroke:#333,stroke-width:2px,color:white;

    %% 互联网接入层
    subgraph Internet_Layer [外部网络]
        ISP1((ISP 1 专线))
        ISP2((ISP 2 专线))
    end

    %% 防火墙层
    subgraph Security_Layer [边界安全]
        FW[新一代防火墙 HA集群]:::fw
    end

    %% 核心层
    subgraph Core_Layer [核心交换层]
        CoreSW[核心交换机堆叠<br/>L3 Gateway<br/>10.x.x.1]:::core
    end

    %% 服务器汇聚区域 (重点优化)
    subgraph Server_Farm [数据中心/算力区]
        Agg_Server[数据中心汇聚交换机<br/>40G/100G 上行]:::agg
        
        subgraph Compute_Nodes [高性能计算]
            S_Visual[视觉算力集群<br/>18+物理机<br/>VLAN 101]:::srv
            S_Storage[存储服务器群<br/>iSCSI/NFS<br/>VLAN 102]:::srv
        end
        
        subgraph General_Server [通用业务]
            S_General[总公司业务/虚拟化<br/>5台宿主机<br/>VLAN 100]:::srv
        end
    end

    %% 办公汇聚区域
    subgraph Office_Area [办公网络接入区]
        Agg_Office[办公网汇聚交换机<br/>10G 上行]:::agg
        
        Acc_HQ[总公司接入<br/>VLAN 10]:::acc
        Acc_Visual[视觉分公司接入<br/>VLAN 20]:::acc
        Acc_Design[设计分公司接入<br/>VLAN 30]:::acc
        Acc_Wifi[无线AP群<br/>VLAN 50/200]:::acc
    end

    %% IoT/工厂汇聚区域
    subgraph IoT_Area [安防与IoT区域]
        Agg_IoT[IoT/安防汇聚交换机<br/>10G 上行]:::agg
        
        Acc_Factory[生产车间接入<br/>VLAN 40]:::acc
        Acc_CCTV[视频监控接入 POE<br/>VLAN 60]:::acc
        Acc_Dumb[门禁/打印机<br/>VLAN 61]:::acc
    end

    %% 连接关系
    ISP1 --- FW
    ISP2 --- FW
    FW ===|聚合链路| CoreSW
    
    CoreSW ===|40G/100G 光纤| Agg_Server
    CoreSW ===|10G 光纤| Agg_Office
    CoreSW ===|10G 光纤| Agg_IoT
    
    Agg_Server ===|10G/25G| S_Visual
    Agg_Server ===|10G/25G| S_Storage
    Agg_Server ---|10G| S_General
    
    Agg_Office ---|1G/2.5G| Acc_HQ
    Agg_Office ---|1G/2.5G| Acc_Visual
    Agg_Office ---|1G/2.5G| Acc_Design
    Agg_Office ---|1G PoE| Acc_Wifi
    
    Agg_IoT ---|1G Ind| Acc_Factory
    Agg_IoT ---|1G PoE| Acc_CCTV
    Agg_IoT ---|1G| Acc_Dumb

    %% 备注
    linkStyle default stroke-width:2px,fill:none,stroke:#333;
