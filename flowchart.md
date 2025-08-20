flowchart LR
  subgraph Local
    A[ansible-control (localhost)]
  end

  subgraph Infrastructure
    M[masters]
    NM[new_managers]
    W[workers]
    NFS[nfs_server]
    NG[nginx_servers]
  end

  A -->|create_disk| M
  A -->|create_disk| NM
  A -->|create_disk| W
  A -->|update_hosts| M
  A -->|update_hosts| NM
  A -->|master1_install & helm| M
  A -->|master1_create_cluster (Rancher API)| A --> M
  A -->|kubectl_setup| NM
  A -->|nfs role| NFS
  A -->|nfs_client_setup| M & NM & W
  A -->|nfs_provisioner (helm)| NM
  A -->|install_prometheus| NM
  A -->|install_pgadmin| NM
  A -->|nginx_install| NG

  NFS -->|exports| M & NM & W
  NG -->|reverse-proxy| M[NODES:Rancher] & NM
