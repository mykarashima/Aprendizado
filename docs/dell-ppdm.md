# DELL-PPDM

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# Principais Pontos de Atenção para Utilização do Dell PowerProtect Data Manager (PPDM)

O Dell PowerProtect Data Manager (PPDM) é uma plataforma de proteção de dados moderna e definida por software que oferece recursos avançados para backup de máquinas virtuais e proteção de clusters Kubernetes. Para garantir uma implementação bem-sucedida e operação otimizada, é fundamental compreender os principais pontos de atenção específicos para cada tipo de carga de trabalho.

![Principais pontos de atenção para utilização do Dell PowerProtect Data Manager (PPDM) para backup de VMs e clusters Kubernetes](https://ppl-ai-code-interpreter-files.s3.amazonaws.com/web/direct-files/9f5acd5cb58098d610d50369287ea728/58b44904-23fc-4b35-bae2-1abde12aa9d8/4271f79c.png)

Principais pontos de atenção para utilização do Dell PowerProtect Data Manager (PPDM) para backup de VMs e clusters Kubernetes

## Requisitos e Considerações do Sistema

### Especificações Mínimas de Hardware

O PowerProtect Data Manager possui requisitos específicos de recursos que devem ser cuidadosamente planejados. Para a implementação via OVA no VMware vSphere, são necessários **10 cores de CPU**, **24 GB de RAM** e configuração de discos específica incluindo 100 GB para o disco principal, 500 GB para o segundo disco, e discos adicionais menores para componentes específicos.[^1_1][^1_2][^1_3]

### Compatibilidade de Plataforma

O ambiente deve atender aos requisitos mínimos de **VMware vCenter 6.5 ou superior** e **VMware ESXi 6.5 ou superior**. Para backup com reconhecimento de aplicações, são necessárias versões mais recentes, incluindo VMware Tools 10.1 ou superior. É importante notar que o PPDM suporta apenas endereços **IPv4**, não oferecendo suporte nativo para IPv6.[^1_1][^1_2][^1_4]

### Planejamento de Capacidade

A capacidade de armazenamento pode ser dimensionada desde **12 TB até 256 TB em incrementos de 12 TB**. O sistema oferece **desduplicação inline** e **compressão assistida por hardware**, proporcionando taxas de desduplicação garantidas de até 55:1. Para ambientes maiores, é possível expandir a capacidade através de shelves de expansão adicionais.[^1_5][^1_6][^1_7]

## Backup de Máquinas Virtuais

### VM Direct Engine e Limitações

O PPDM utiliza o conceito de **VM Direct Engine** para movimentação de dados durante operações de backup. Cada VM Direct Engine externo pode gerenciar **máximo de 25 sessões simultâneas** de backup e recuperação, enquanto o engine embarcado suporta apenas **quatro sessões**. Para ambientes de produção, é altamente recomendável implementar engines externos devido às limitações de capacidade do engine embarcado.[^1_2][^1_8]

### Transparent Snapshots (TSDM)

Uma das funcionalidades mais avançadas é o **Transparent Snapshot Data Mover (TSDM)**, que oferece backup de imagens de VM com **impacto quase zero** no desempenho das máquinas virtuais. Esta tecnologia utiliza as **APIs vSphere para filtros de I/O (VAIO)** e é implementada através de um VIB (VMware Installation Bundle) certificado pela VMware. O TSDM é automaticamente instalado nos hosts ESXi quando políticas de proteção são criadas, mas pode ser gerenciado manualmente se necessário.[^1_9][^1_10][^1_11][^1_12]

### Privilégios e Permissões

Para operações de backup de VM, é necessário configurar uma **conta de usuário vCenter dedicada** no nível raiz do vCenter com privilégios específicos. É importante evitar o uso da conta de administrador do vCenter e criar uma conta dedicada exclusivamente para uso com o PPDM. A conta deve ter privilégios para operações de descoberta, iniciação do modo de transporte Hot Add, e operações de restauração incluindo Instant Access.[^1_2][^1_13]

### Limitações de Backup

O PPDM suporta apenas **backup em nível de imagem e nível de disco**, não permitindo backup de pastas individuais dentro das máquinas virtuais. Existe uma limitação importante relacionada a VMs criadas a partir de snapshots, onde discos com nomenclatura "-000001.vmdk" podem causar falhas no TSDM, forçando o uso do método NBD tradicional.[^1_13][^1_14][^1_9]

## Proteção de Clusters Kubernetes

### Requisitos de Storage

Para proteção de clusters Kubernetes, o PPDM possui requisitos específicos de storage. **Apenas storage baseado em CSI (Container Storage Interface) é suportado** para operações de backup e recuperação. Adicionalmente, apenas PVCs com **VolumeMode Filesystem são suportados**. Embora a descoberta inclua volumes de storage não-CSI, as operações de backup e recuperação são limitadas exclusivamente a storage CSI.[^1_15][^1_16][^1_4][^1_17]

### Service Account e Privilégios

A configuração de clusters Kubernetes requer um **service account com privilégios específicos**, incluindo capacidades para Get/Create/Update/List de CustomResourceDefinitions, criação de ClusterRoleBinding para role 'cluster-admin', e acesso completo ao namespace 'powerprotect'. Por padrão, a conta admin-user no namespace kube-system contém todos os privilégios necessários, mas é possível criar contas personalizadas com privilégios mínimos utilizando arquivos YAML fornecidos pelo PPDM.[^1_16][^1_4]

### Componentes de Proteção

O PPDM implementa dois namespaces principais no cluster durante a descoberta: **velero-ppdm** (contém pod Velero para backup de metadados) e **powerprotect** (contém PowerProtect controller para snapshots e backup de PVCs). O sistema utiliza o **VMware Velero** integrado para backup e restauração de metadados, enquanto o **PowerProtect controller** gerencia snapshots e backup de volumes persistentes.[^1_15][^1_18]

### Backup Consistente de Aplicações

Para bancos de dados em pods Kubernetes, o PPDM oferece **backup agentless e consistente com aplicações** para MySQL, MongoDB, PostgreSQL e Cassandra. Esta funcionalidade utiliza **templates de aplicação** implementados através de arquivos YAML customizáveis que fazem a ponte entre ambientes específicos de banco de dados e a arquitetura de backup Kubernetes.[^1_19][^1_20][^1_15][^1_21]

## Limites de Escalabilidade

### Limites por Componente

O PPDM possui limites testados e recomendados para diversos componentes. É possível conectar até **12 vCenters**, **40 VM Direct Engines externos**, proteger até **10.000 máquinas virtuais**, integrar com **10 appliances Data Domain**, e configurar até **5 nós de search engine**. Estes números representam limites testados e devem ser considerados como melhores práticas para dimensionamento do ambiente.[^1_8]

### Distribuição de Recursos

A distribuição de VM Direct Engines deve considerar que o limite por vCenter é de **25 engines**, enquanto o limite global é de 40. Por exemplo, utilizando o máximo de 12 vCenters, seria possível distribuir uma média de três VM Direct Engines por vCenter. Esta distribuição deve ser cuidadosamente planejada para evitar gargalos de performance.[^1_8]

## Otimização de Performance

### Storage Direct Protection

Uma funcionalidade avançada é o **Storage Direct Protection**, que oferece backup e recuperação rápidos diretamente do storage Dell, resultando em **RPOs mais curtos**. Esta tecnologia proporciona **proteção de dados eficiente e segura** para ofertas mais amplas do Dell Storage, incluindo PowerMax e PowerStore.[^1_22]

### Volume Group Snapshotting

Para ambientes Kubernetes utilizando PowerFlex CSI driver, o PPDM automaticamente utiliza a **extensão volume group snapshot** quando PVCs compartilham o mesmo label de volume-group. Esta funcionalidade permite snapshot de todos os PVCs pertencentes ao mesmo grupo de volume simultaneamente, ao invés de criar snapshots individuais.[^1_23]

### Desduplicação e Compressão

O sistema implementa **desduplicação inline aumentada**, onde blocos de dados comuns entre máquinas virtuais são escritos apenas uma vez no local de backup. Adicionalmente, oferece **compressão assistida por hardware** através de placas QAT (Intel Quick Assist Technology), resultando em economia significativa de espaço de armazenamento e melhoria na velocidade de backup.[^1_24][^1_5]

## Requisitos de Rede e Conectividade

### Configuração de Portas

O PPDM requer configuração específica de portas para comunicação adequada. As principais portas incluem **8443 TCP** para servidor REST e comunicação com agentes VM Direct, **7000 TCP** para comunicação bidirecional entre agentes e appliance PPDM, e **443 HTTPS** para comunicação com vCenter. Para clusters Kubernetes, a porta **30095 TCP** é utilizada para comunicação com o PowerProtect Controller.[^1_2]

### Sincronização de Tempo e DNS

É **altamente recomendado** configurar servidores NTP para sincronização do PPDM. O sistema também requer configuração adequada de DNS, incluindo **lookups forward e reverse**. É importante evitar o uso de endereços IP na subnet 172.24.0.192/26, que está reservada para a rede Docker privada.[^1_2][^1_25]

### Considerações de Firewall

O PPDM utiliza **firewall Linux SLES 12** para proteção e limitação de acesso externo ao sistema. As regras de firewall são configuradas automaticamente para portas utilizadas em comunicação inbound e outbound. Para comunicação adequada, é necessário garantir que todas as portas necessárias estejam abertas entre o PPDM e outros componentes da infraestrutura.[^1_2]

## Segurança e Governança

### Certificados e Criptografia

O sistema utiliza **certificados TLS auto-assinados** por padrão, mas é recomendável substituir por certificados de autoridades confiáveis em ambientes de produção. Para clusters Kubernetes, está disponível **criptografia de dados em trânsito**, que pode ser habilitada nas configurações de segurança da UI do PPDM.[^1_15][^1_13][^1_26]

### Role-Based Access Control (RBAC)

O PPDM implementa **controle de acesso baseado em roles (RBAC)** robusto, permitindo definição granular de privilégios para diferentes usuários e grupos. O sistema suporta integração com **LDAP e Active Directory** para autenticação centralizada, e oferece múltiplas roles predefinidas com privilégios específicos para diferentes funções.[^1_27][^1_28][^1_29][^1_7]

### Retention Locks e Proteção contra Ransomware

Uma funcionalidade crítica de segurança é o **retention lock**, que pode ser aplicado a backups de todos os tipos de assets. O sistema também inclui **detecção de anomalias** utilizando algoritmos avançados de machine learning para identificar atividades suspeitas durante backups, ajudando a proteger contra ransomware e outras ameaças.[^1_22][^1_2]

## Melhores Práticas Operacionais

### Implementação de VM Direct Engines

É **fortemente recomendado** implementar VM Direct Engines externos devido às limitações de capacidade do engine embarcado. Cada engine externo deve atender aos requisitos de **4 vCPU * 2 GHz**, **8 GB RAM** e configuração específica de discos. O planejamento deve considerar que engines externos podem gerenciar até 25 sessões simultâneas.[^1_2]

### Backup do Sistema PPDM

O **serviço de proteção do sistema PowerProtect Data Manager** deve ser configurado para proteger dados persistentes críticos, incluindo bancos de dados Lockbox e Elasticsearch. Os backups do sistema são considerados "completos" mesmo sendo criados de forma incremental, e devem ser armazenados em uma unidade de storage DD privada dedicada.[^1_2][^1_30]

### Monitoramento e Manutenção

É essencial implementar **monitoramento contínuo** do ambiente através do SupportAssist, que oferece monitoramento proativo, criação automática de casos de suporte, e verificações automáticas de saúde. O sistema deve ser configurado para envio de **notificações por email** para alertas críticos e status de backup.[^1_25][^1_23]

### Testes de Recuperação

Implementar **testes regulares de recuperação** é fundamental para garantir a integridade dos backups. O PPDM oferece funcionalidades de **test drills automatizados** que podem verificar a integridade dos backups e realizar restaurações completas de teste em VMs isoladas. Estes testes devem ser agendados regularmente para validar a capacidade de recuperação do ambiente.[^1_24]

## Considerações de Upgrade e Migração

### Structured Custom Resource Definitions

A partir da versão 19.9, o PPDM utiliza **Structured Custom Resource Definitions (CRDs)** com Kubernetes para aumentar a segurança do sistema. Ao atualizar de versões anteriores, a **recuperação self-service do último backup** feito antes da atualização não é possível, sendo necessário utilizar a interface do PPDM para restauração.[^1_15][^1_13]

### Compatibilidade de Versões

Para utilização do Transparent Snapshots, é necessário **VMware ESXi 7.0 U3 ou superior**. O sistema mantém compatibilidade com versões anteriores para a maioria das funcionalidades, mas recursos avançados podem requerer versões específicas de componentes.[^1_1][^1_31][^1_12]

### Planejamento de Capacidade Futura

Ao planejar a implementação, considerar crescimento futuro é essencial. O sistema pode ser expandido de **12 TB até 256 TB** através de licenças adicionais de capacidade em incrementos de 12 TB. Para ambientes com crescimento rápido, é recomendável implementar o **Cloud Tier** que pode estender a capacidade utilizando storage de objetos de baixo custo.[^1_32][^1_5][^1_7][^1_33]

A implementação bem-sucedida do Dell PowerProtect Data Manager requer planejamento cuidadoso de todos estes aspectos, desde requisitos básicos de sistema até considerações avançadas de segurança e escalabilidade. O monitoramento contínuo e a aderência às melhores práticas operacionais são fundamentais para manter um ambiente de proteção de dados robusto e eficiente.
<span style="display:none">[^1_34][^1_35][^1_36][^1_37][^1_38][^1_39][^1_40][^1_41][^1_42][^1_43][^1_44][^1_45][^1_46][^1_47][^1_48][^1_49][^1_50][^1_51][^1_52][^1_53][^1_54][^1_55][^1_56][^1_57][^1_58][^1_59][^1_60][^1_61][^1_62][^1_63][^1_64][^1_65][^1_66][^1_67][^1_68]</span>

<div style="text-align: center">⁂</div>

[^1_1]: https://infohub.delltechnologies.com/l/dell-powerprotect-data-manager-deployment-best-practices-1/powerprotect-data-manager-overview-4/

[^1_2]: https://www.delltechnologies.com/asset/en-us/products/storage/industry-market/h18564-powerprotect-data-manager-deployment-best-practice-wp.pdf

[^1_3]: https://www.dell.com/support/manuals/en-us/enterprise-copy-data-management/pp-dm_19.12_deployment_guide/powerprotect-data-manager-resource-requirements-in-a-vmware-environment?guid=guid-82d1b26e-4603-41fd-b88c-a25c0b1e5902\&lang=en-us

[^1_4]: https://www.dell.com/support/manuals/en-us/enterprise-copy-data-management/pp-dm_19.9_kubernetes_ug/recommendations-and-considerations-when-using-a-kubernetes-cluster?guid=guid-806649b2-23ee-4040-afb0-2fdd8bf7cb78\&lang=en-us

[^1_5]: https://infohub.delltechnologies.com/l/dell-powerprotect-data-manager-appliance-configuration-best-practices/powerprotect-data-manager-appliance-hardware-composition-2/

[^1_6]: https://www.dell.com/support/contents/en-us/videos/videoplayer/how-to-expand-powerprotect-data-manager-appliance-storage/6361509582112

[^1_7]: https://www.storagereview.com/review/dell-powerprotect-data-manager-appliance-redefines-simple-for-data-protection

[^1_8]: https://infohub.delltechnologies.com/l/dell-powerprotect-data-manager-deployment-best-practices-1/scalability-limits-for-powerprotect-data-manager-2/

[^1_9]: https://www.dell.com/support/kbdoc/pt-br/000282152/ppdm-os-backups-de-vm-configurados-para-usar-o-modo-de-transferência-tsdm-usam-nbd-em-vez-disso

[^1_10]: https://www.dell.com/support/contents/en-aw/videos/videoplayer/transparent-snapshots-with-powerprotect-data-manager-overview/6361507413112

[^1_11]: https://www.dell.com/support/contents/pt-br/videos/videoplayer/como-usar-o-transparent-snapshot-data-mover-tsdm-para-o-powerprotect-data-manager-199/6282596233001

[^1_12]: https://infohub.delltechnologies.com/en-us/l/powerprotect-data-manager-vmware-virtual-machine-protection-using-transparent-snapshots/transparent-snapshots-architecture-7/

[^1_13]: https://www.dell.com/support/manuals/en-us/enterprise-copy-data-management/pp-dm_19.10_virtual_machines_ug/vm-direct-engine-limitations-and-unsupported-features?guid=guid-924a9b67-e639-4905-af86-bfb177fb349f\&lang=en-us

[^1_14]: https://www.dell.com/support/manuals/pt-br/enterprise-copy-data-management/pp-dm_ag/limita%C3%A7%C3%B5es-e-recursos-n%C3%A3o-compat%C3%ADveis-do-vm-direct-engine?guid=guid-924a9b67-e639-4905-af86-bfb177fb349f\&lang=pt-br

[^1_15]: https://www.delltechnologies.com/asset/en-us/products/storage/industry-market/h18563-dell-powerprotect-data-manager-protecting-kubernetes-workloads-wp.pdf

[^1_16]: https://www.dell.com/support/manuals/en-us/enterprise-copy-data-management/pp-dm_19.19_hyperv_vm_ug/best-practices-and-additional-considerations-for-hypervisor-environments?guid=guid-a388c34a-f78c-4c9a-b2f2-e19f2e8f75a1\&lang=en-us

[^1_17]: https://www.dell.com/support/manuals/en-us/enterprise-copy-data-management/pp-dm_19.9_kubernetes_ug/prerequisites-to-kubernetes-cluster-discovery?guid=guid-8197023d-4899-4faf-9f0a-95fc3a7a6858\&lang=en-us

[^1_18]: https://www.dell.com/support/contents/en-uk/videos/videoplayer/dell-emc-powerprotect-overview/6328414465112

[^1_19]: https://infohub.delltechnologies.com/l/dell-powerprotect-data-manager-protecting-kubernetes-workloads/application-consistent-database-backups-in-kubernetes-6/

[^1_20]: https://www.youtube.com/watch?v=ZJay2jhVb7s

[^1_21]: https://www.dell.com/support/kbdoc/pt-br/000310372/powerprotect-os-ativos-de-vm-não-protegidos-são-contados-como-ativos-desprotegidos

[^1_22]: https://www.delltechnologies.com/asset/pt-br/products/data-protection/technical-support/h17691-dellemc-powerprotect-software-ds.pdf

[^1_23]: https://www.delltechnologies.com/asset/pt-br/products/data-protection/industry-market/esg-showcase-modernizing-vm-backups-at-scale-without-compromise-with-dell-technologies.pdf

[^1_24]: https://support.hornetsecurity.com/hc/en-us/articles/19688237179921-Best-Practices-for-setting-up-VM-Backup

[^1_25]: https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dellemc.ppdm_ddve_0_0_1?tab=overview

[^1_26]: https://www.dell.com/support/contents/en-id/videos/videoplayer/k8’s-application-consistency-with-powerprotect-data-manager/6361508995112

[^1_27]: https://www.dell.com/support/contents/pt-br/videos/videoplayer/como-fazer-backup-e-restaurar-máquinas-virtuais-no-powerprotect-data-manager-ppdm/6348000448112

[^1_28]: https://www.pdq.com/blog/virtual-machine-performance-tips/

[^1_29]: https://www.dell.com/support/manuals/pt-br/enterprise-copy-data-management/pp-dm_deployment_guide/pref%C3%A1cio?guid=guid-4101cd29-b8f1-47ed-9b56-92f0c7f1db50\&lang=pt-br

[^1_30]: https://forfirm.com/optimizing-virtual-machine-management-best-practices-for-efficient-virtual-environment/

[^1_31]: https://www.dell.com/support/contents/en-ai/videos/videoplayer/how-to-backup-restore-virtual-machines-in-powerprotect-data-manager-ppdm/6348000448112

[^1_32]: https://www.storagereview.com/pt/review/dell-powerprotect-data-manager-appliance-redefines-simple-for-data-protection

[^1_33]: https://infohub.delltechnologies.com/zh-cn/l/dell-powerprotect-data-manager-appliance-configuration-best-practices/introduction-5029/

[^1_34]: https://www.youtube.com/watch?v=XtmoD4e2_gM

[^1_35]: https://xopero.com/blog/en/virtual-machine-backup-a-step-by-step-guide-to-data-protection/

[^1_36]: https://www.dell.com/en-us/shop/storage-servers-and-networking-for-business/sf/powerprotect-data-manager

[^1_37]: https://www.wwt.com/article/kubernetes-backups-with-dell-emc-power-protect

[^1_38]: https://www.dell.com/support/product-details/pt-br/product/enterprise-copy-data-management/overview

[^1_39]: https://infohub.delltechnologies.com/t/powerprotect-data-manager-virtual-machine-backup-and-recovery-1/

[^1_40]: https://www.dell.com/support/kbdoc/pt-br/000184431/powerprotect-como-proteger

[^1_41]: https://www.dell.com/support/contents/pt-br/videos/videoplayer/como-proteger-um-cluster-convidado-do-tanzu-kubernetes-com-o-powerprotect-data-manager-197/1697183664732876962

[^1_42]: https://www.vmware.com/docs/vsphere-esxi-vcenter-server-80-performance-best-practices

[^1_43]: https://learn.microsoft.com/en-us/azure/azure-sql/virtual-machines/windows/performance-guidelines-best-practices-storage?view=azuresql

[^1_44]: https://www.dell.com/support/kbdoc/en-us/000323604/powerprotect-data-manager-upgrade-best-practices-and-recommendations

[^1_45]: https://www.dell.com/support/contents/pt-br/videos/videoplayer/visão-geral-de-snapshots-transparentes-com-powerprotect-data-manager/6361507413112

[^1_46]: https://docs.redhat.com/pt-br/documentation/red_hat_enterprise_linux/8/html/monitoring_and_managing_system_status_and_performance/optimizing-virtual-machine-performance-in-rhel-8_monitoring-and-managing-system-status-and-performance

[^1_47]: https://learn.microsoft.com/pt-br/troubleshoot/azure/virtual-machines/windows/performance-diagnostics

[^1_48]: https://learn.microsoft.com/pt-br/troubleshoot/azure/virtual-machines/windows/troubleshoot-high-cpu-issues-azure-windows-vm

[^1_49]: https://www.dell.com/support/manuals/pt-br/enterprise-copy-data-management/pp-dm_ag/pr%C3%A1ticas-recomendadas-e-considera%C3%A7%C3%B5es-adicionais-para-o-vm-direct-engine?guid=guid-d8a8f304-6769-4d2c-9a00-24d596ded602\&lang=pt-br

[^1_50]: https://binario.cloud/blog/problemas-de-performance-de-maquinas-virtuais-saiba-qual-e-o-processador-adequado-para-suas-solucoes/

[^1_51]: https://transparencia.rj.def.br/uploads/arquivos/licitacao/b8521d1dcbc94ba3b75d1a1b7535a579.pdf

[^1_52]: https://www.youtube.com/watch?v=zi78hGu28MI

[^1_53]: https://www.dell.com/support/kbdoc/pt-br/000211964/powerprotect-data-manager-replication-data-for-tsdm-capacity-optimized-backup-occupies-more-space-on-the-target-data-domain

[^1_54]: https://www.scribd.com/document/812580440/PowerProtect-Data-Manager-Network-Attached-Storage-User-Guide

[^1_55]: https://www.dell.com/support/kbdoc/pt-br/000192052/powerprotect-erro-sys0050-o-desempenho-do-sistema-está-lento

[^1_56]: https://console.cloud.google.com/marketplace/product/dellemc-ddve-public/powerprotect-data-manager

[^1_57]: https://www.dell.com/support/kbdoc/pt-br/000224668/ppdm-virtual-machines-not-responding-or-crash-during-vsphere-lwd-based-task-requested-by-ppdm

[^1_58]: https://www.dell.com/support/manuals/en-us/power-protect-dm5500/pp-dm5500_5.15_ig/dm5500-storage-capacity?guid=guid-bdd84360-aeea-453a-a249-fc67c294df0b\&lang=en-us

[^1_59]: https://support.continuitysoftware.com/hc/en-us/articles/14558915565724-Dell-PowerProtect-Data-Manager-PPDM-Scan-Requirements

[^1_60]: https://infohub.delltechnologies.com/fr-fr/l/dell-powerprotect-data-manager-dynamic-nas-protection-1/sizing-recommendations-12/

[^1_61]: https://www.dell.com/support/manuals/en-us/idp-other/dd_p_ddmc_user_guide/measuring-physical-capacity?guid=guid-180935f5-971f-4a2a-8005-fd306f93e6d2\&lang=en-us

[^1_62]: https://www.delltechnologies.com/asset/en-us/products/data-protection/technical-support/docu95705.pdf

[^1_63]: https://www.dell.com/support/manuals/en-us/enterprise-copy-data-management/pp-dm_19.9_ag/software-and-hardware-requirements?guid=guid-5a0b9da3-8bb8-4b0a-99cc-a6154347e4f6\&lang=en-us

[^1_64]: https://www.dell.com/support/contents/en-jo/videos/videoplayer/configure-and-deploy-the-reporting-engine-in-powerprotect-data-manager/6345415314112

[^1_65]: https://www.cliffsnotes.com/study-notes/22335674

[^1_66]: https://www.dell.com/support/manuals/en-us/enterprise-copy-data-management/pp-dm_19.9_ag/changing-storage-targets-and-storage-units?guid=guid-d819d8c1-683b-4e4f-8232-86413557d816\&lang=en-us

[^1_67]: https://www.dell.com/support/contents/pt-br/videos/videoplayer/como-desbloquear-a-conta-do-sistema-operacional-do-usuário-administrador-do-powerprotect-data-manager-usando-o-console-da-web/6348811762112

[^1_68]: https://www.dell.com/support/contents/en-uk/videos/videoplayer/how-to-configure-an-ntp-server-in-powerprotect-data-manager-ui-|-step-by-step-tutorial/6367164626112

