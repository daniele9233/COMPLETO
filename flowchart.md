graph TB
    subgraph "FASE 1: PREPARAZIONE INFRASTRUTTURA"
        A[site.yml] -->|Play 1| B[create_disk<br/>Su: all hosts]
        A -->|Play 2| C[update_hosts_file<br/>Su: all + local]
    end
    
    subgraph "FASE 2: INSTALLAZIONE RANCHER"
        A -->|Play 3| D[master1_install<br/>Su: masters]
        D --> E[master1_config]
        E --> F[master1_kubectl]
        F --> G[master1_helm_cert_manager_install]
    end
    
    subgraph "FASE 3: CREAZIONE CLUSTER K8S"
        A -->|Play 4| H[master1_create_cluster<br/>Su: masters]
        H -->|API Rancher| I[Crea cluster 'iacdevops']
        I --> J[Registra nodi manager]
        I --> K[Registra nodi worker]
    end
    
    subgraph "FASE 4: CONFIGURAZIONE KUBECTL"
        A -->|Pausa 180s| L[kubectl_setup<br/>Su: new_managers]
        L --> M[Fix DNS cattle-system]
    end
    
    subgraph "FASE 5: STORAGE NFS"
        A -->|Play 7| N[nfs<br/>Su: nfs_server]
        N --> O[Export /share/k8s]
        N --> P[Export /share/backup]
        A -->|Play 8.1| Q[nfs_client_setup<br/>Su: tutti i nodi cluster]
        A -->|Play 8.2| R[nfs_provisioner<br/>Su: new_managers]
        R --> S[StorageClass: nfs-k8s]
        R --> T[StorageClass: nfs-backup]
    end
    
    subgraph "FASE 6: DEPLOY APPLICAZIONI"
        A -->|Play 9| U[install_pgadmin<br/>Su: new_managers]
        A -->|Play 11| V[install_prometheus<br/>Su: new_managers]
        V --> W[Deploy Grafana]
        V --> X[Deploy AlertManager]
    end
    
    subgraph "FASE 7: REVERSE PROXY"
        A -->|Play 10| Y[nginx_install<br/>Su: nginx_servers]
        Y --> Z[Proxy HTTPS per Rancher]
        Y --> AA[Proxy per servizi K8s]
    end