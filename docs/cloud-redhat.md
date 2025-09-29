# cloud redhat

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# Principais Pontos de Atenção para Criação de Cloud On-Premise Red Hat para Aplicações Telco

Com base na pesquisa abrangente realizada, identifiquei os principais pontos de atenção críticos para implementar uma cloud on-premise Red Hat destinada a aplicações de telecomunicações que suportará tanto VNFs quanto CNFs.

## Arquitetura de Plataforma Híbrida

O ponto mais fundamental é a **arquitetura híbrida VNF/CNF**. Sua infraestrutura deve suportar simultaneamente:[^1_1][^1_2][^1_3]

- **Red Hat OpenShift Container Platform** para CNFs (Cloud-Native Network Functions)
- **Red Hat OpenStack Services on OpenShift** para VNFs tradicionais
- **Red Hat OpenShift Data Foundation/Ceph Storage** para storage distribuído

Esta abordagem permite migração gradual de VNFs para CNFs sem interromper serviços existentes.[^1_4][^1_5]

## Requisitos de Hardware Críticos

### Dimensionamento de Nós de Controle

Para alta disponibilidade telco, os nós de controle OpenShift requerem:[^1_6][^1_7]

- **Mínimo 3 nós** para quorum e tolerância a falhas
- **32 cores/64 threads** por nó
- **256 GB RAM** por nó
- **500 GB SSD** para root disk + storage adicional
- **Interfaces de 25 Gbps** (mínimo 10 Gbps)


### Nós Worker Especializados

Para workloads telco de alta performance:[^1_8][^1_1]

- **CPUs**: Intel 3rd Gen Xeon (IceLake) ou AMD EPYC Zen 4
- **64+ GB RAM** com suporte a huge pages
- **NVMe/SSD storage** para baixa latência
- **SR-IOV NICs** certificadas para DPDK


## Networking de Alto Desempenho

### Requisitos Obrigatórios

A rede é crítica para aplicações telco:[^1_9][^1_1]

- **IPv6 obrigatório** com dual-stack IPv4/IPv6
- **SR-IOV e DPDK** para processamento de pacotes de baixa latência
- **Segmentação de rede** separada para OAM, sinalização e storage
- **Bonding ativo-ativo (LACP)** para redundância


### Performance de Rede

- **MACVLAN/IPVLAN** para redes secundárias
- **MetalLB** para load balancing
- **Service mesh** para comunicação entre CNFs


## Storage Distribuído e Resiliente

### Red Hat Ceph Storage

Para workloads telco críticos:[^1_10][^1_11]

- **Mínimo 5 nós** com replicação tripla
- **5 GB RAM por OSD** para performance
- **SSDs exclusivamente** para BlueStore
- **Rede cluster separada** para tráfego inter-OSD


## Segurança e Compliance Telco

### Frameworks de Compliance

Aplicações telco exigem aderência rigorosa:[^1_12][^1_13][^1_14]

- **NIST SP 800-171** para informações controladas
- **NIST SP 800-190** para segurança de containers
- **FIPS 140-2** para criptografia validada
- **Common Criteria** para avaliações de segurança


### Hardening Obrigatório

- **SELinux enforcing mode** sempre habilitado
- **RBAC granular** para controle de acesso
- **Network policies** para microsegmentação
- **Image scanning** contínuo para vulnerabilidades


## Observabilidade Telco-Grade

### Stack de Monitoramento

Para operações telco 24/7:[^1_15][^1_16][^1_17]

- **Prometheus/Grafana** para métricas e dashboards
- **Alertmanager** com integração a sistemas de ticketing
- **Logging centralizado** com retenção conforme compliance
- **Distributed tracing** para troubleshooting de CNFs


### Métricas Críticas

- **Latência de rede por-segundo** e agregada
- **Throughput per-user** e total
- **SLA monitoring** com availability KPIs
- **Custom metrics** para auto-scaling baseado em tráfego telco


## Alta Disponibilidade e Disaster Recovery

### Estratégia Multi-Site

Para continuidade de serviços críticos:[^1_18][^1_19][^1_20]

- **Metro-DR** com replicação síncrona para baixo RPO
- **Regional-DR** com replicação assíncrona
- **OpenShift APIs for Data Protection (OADP)** para backup
- **Testes regulares de failover** para validação


### Distribuição Geográfica

- **Multi-zone deployment** obrigatório
- **Pod anti-affinity** para evitar single points of failure
- **PodDisruptionBudgets** para controle de manutenções


## Integração com Sistemas Legacy

### OSS/BSS Integration

Crítico para operadoras:[^1_21][^1_22][^1_23]

- **APIs RESTful** para integração com sistemas existentes
- **Apache Kafka** para streaming de eventos em tempo real
- **Service mesh** para conectividade híbrida
- **Suporte a protocolos legados** (SS7, Diameter, SIP)


## Performance e Otimização Telco

### CPU Management Avançado

Para workloads críticos:[^1_1][^1_24]

- **CPU Manager** com cores dedicados
- **Topology Manager** com NUMA awareness
- **Performance Profiles** para tuning de kernel
- **IRQ affinity** para isolamento de interrupções


### Memory Management

- **Huge pages** configurado para aplicações DPDK
- **NUMA pinning** para workloads sensíveis à latência
- **QoS classes** (Guaranteed/Burstable/BestEffort)


## Considerações de Implementação

### Certificação e Validação

- **Red Hat CNF Certification** para fornecedores
- **Hardware certification** para compatibilidade garantida
- **Performance benchmarking** para validação de SLAs


### Custos e Licenciamento

- **Planejamento de licenças** Red Hat por socket/cluster
- **Support tiers** adequados para ambiente telco
- **Professional services** para implementação crítica


## Estratégia de Migração

### Abordagem Gradual

- **Strangler pattern** para substituição incremental de VNFs
- **Blue-green deployments** para minimizar downtime
- **A/B testing** para validação de performance

Esta implementação requer planejamento meticuloso e expertise especializada, mas oferece uma base sólida para modernização telco com redução de OPEX, maior agilidade operacional e capacidade de inovação acelerada para atender às demandas de 5G e edge computing.
<span style="display:none">[^1_25][^1_26][^1_27][^1_28][^1_29][^1_30][^1_31][^1_32][^1_33][^1_34][^1_35][^1_36][^1_37][^1_38][^1_39][^1_40][^1_41][^1_42][^1_43][^1_44][^1_45][^1_46][^1_47][^1_48][^1_49][^1_50][^1_51][^1_52][^1_53][^1_54][^1_55][^1_56][^1_57][^1_58][^1_59][^1_60][^1_61][^1_62][^1_63][^1_64][^1_65][^1_66][^1_67][^1_68][^1_69][^1_70][^1_71][^1_72][^1_73]</span>

<div style="text-align: center">⁂</div>

[^1_1]: https://docs.redhat.com/en/documentation/openshift_container_platform/4.18/html/scalability_and_performance/telco-core-ref-design-specs

[^1_2]: https://www.redhat.com/en/topics/cloud-computing/what-is-telco-cloud

[^1_3]: https://www.redhat.com/en/resources/openstack-value-telecommunication-service-providers-brief

[^1_4]: https://www.redhat.com/en/resources/cnf-certification-telcos-intel-and-red-hat

[^1_5]: https://www.redhat.com/en/blog/running-cloud-native-network-functions-confidence-red-hat-openshift

[^1_6]: https://docs.redhat.com/en/documentation/red_hat_openstack_services_on_openshift/18.0/html/planning_your_deployment/assembly_infrastructure-and-system-requirements

[^1_7]: https://docs.redhat.com/en/documentation/red_hat_openstack_platform/17.1/html/deploying_red_hat_openstack_platform_at_scale/assembly-recommended-specifications-for-your-large-openstack-deployment_recommendations-large-deployments

[^1_8]: https://docs.redhat.com/en/documentation/openshift_container_platform/4.18/html/installing_on_openstack/installing-openstack-nfv-preparing

[^1_9]: https://infohub.delltechnologies.com/en-au/l/design-and-optimize-a-5g-telco-cloud/high-performance-data-plane-networks/

[^1_10]: https://docs.redhat.com/en/documentation/red_hat_openstack_platform/16.2/html/recommendations_for_large_deployments/assembly-recommended-specifications-for-your-large-openstack-deployment_recommendations-large-deployments

[^1_11]: https://docs.redhat.com/en/documentation/red_hat_ceph_storage/4/html/installation_guide/requirements-for-installing-rhcs

[^1_12]: https://access.redhat.com/compliance/nist-sp-800-171

[^1_13]: https://www.redhat.com/en/resources/guide-nist-compliance-container-environments-detail

[^1_14]: https://access.redhat.com/compliance/

[^1_15]: https://www.redhat.com/pt-br/blog/getting-telco-observability-right-red-hat

[^1_16]: https://pronteff.com/red-hat-observability-guide-for-openshift-4-17/

[^1_17]: https://www.redhat.com/en/technologies/cloud-computing/openshift/observability

[^1_18]: https://infohub.delltechnologies.com/en-au/l/dell-telecom-infrastructure-blocks-for-red-hat-4-0-architecture-guide/disaster-recovery-194/

[^1_19]: https://cloud.ibm.com/docs/openshift?topic=openshift-iks-ha-dr

[^1_20]: https://www.redhat.com/en/blog/disaster-recovery-enablement-openstack

[^1_21]: https://www.kai-waehner.de/blog/2025/09/09/telecom-oss-modernization-with-data-streaming-from-legacy-burden-to-cloud-native-agility/

[^1_22]: https://www.telecoms.com/oss-bss-cx/microservices-based-cloud-native-modernization-of-oss-bss-with-open-source

[^1_23]: https://www.redhat.com/en/blog/5g-oss-bss-systems

[^1_24]: https://docs.okd.io/4.16/virt/vm_networking/virt-using-dpdk-with-sriov.html

[^1_25]: https://www.redhat.com/en/resources/openstack-platform-datasheet

[^1_26]: https://www.redhat.com/en/blog/top-features-telco-cloud

[^1_27]: https://community.f5.com/kb/technicalarticles/introducing-f5-big-ip-next-cnf-solutions-for-red-hat-openshift/310629

[^1_28]: https://newsroom.orange.com/red-hat-and-orange-collaborate-to-accelerate-telco-cloud-transformation-and-services-softwarization/

[^1_29]: https://docs.redhat.com/en/documentation/red_hat_openstack_platform/17.1/html-single/installing_and_managing_red_hat_openstack_platform_with_director/index

[^1_30]: https://www.redhat.com/en/topics/cloud-native-apps/vnf-and-cnf-whats-the-difference

[^1_31]: https://www.youtube.com/watch?v=CxZraZcyrCo

[^1_32]: https://support.hpe.com/hpesc/public/docDisplay?docId=a00111253en_us\&page=GUID-A31BE143-3E4F-4DE6-8829-879FE42D8EEC.html\&docLocale=en_US

[^1_33]: https://pronteff.com/manage-5g-core-cnf-lifecycles-on-red-hat-openshift/

[^1_34]: https://www.telecomtv.com/content/cloud-native/how-red-hat-and-amdocs-are-advancing-the-telco-cloud-51829/

[^1_35]: https://docs.redhat.com/pt-br/documentation/red_hat_openstack_platform/9/html-single/architecture_guide/index

[^1_36]: https://www.f5.com/company/events/webinars/deploying-a-horizontal-telco-cloud-for-5g

[^1_37]: https://www.redhat.com/en/blog/service-upgrades-telco-5g-cloud-native-core-caas-infrastructure-no-service-disruption

[^1_38]: https://support.hpe.com/hpesc/public/docDisplay?docId=a00113063en_us\&page=GUID-C54E6583-21D8-4771-B080-0BC93D1F7DAE.html\&docLocale=en_US

[^1_39]: https://www.nokia.com/core-networks/nokia-on-red-hat-openshift/

[^1_40]: https://www.redhat.com/en/resources/ceph-storage-datasheet

[^1_41]: https://cloudify.network/oc_sriov.html

[^1_42]: http://www.hyperscalers.com.au/RedHat-Ceph-Hyperscale-Storage

[^1_43]: https://www.delltechnologies.com/asset/zh-hk/solutions/service-provider-solutions/technical-support/telecom-multi-cloud-foundation-with-telecom-infrastructure-blocks-for-red-hat-spec-sheet.pdf

[^1_44]: https://www.redhat.com/en/services/training/cloud-storage-red-hat-ceph-storage-cl260

[^1_45]: https://docs.redhat.com/documentation/pt/red_hat_openstack_platform/17.0/pdf/recommendations_for_large_deployments/Red_Hat_OpenStack_Platform-17.0-Recommendations_for_Large_Deployments-en-US.pdf

[^1_46]: https://docs.redhat.com/en/documentation/red_hat_ceph_storage/8/html/hardware_guide/recommended-minimum-hardware-requirements-for-the-red-hat-ceph-storage-dashboard_hw

[^1_47]: https://docs.redhat.com/en/documentation/red_hat_openstack_platform/16.0/html/network_functions_virtualization_planning_and_configuration_guide/ch-hardware-requirements

[^1_48]: https://docs.okd.io/4.18/networking/hardware_networks/configuring-sriov-device.html

[^1_49]: https://www.linkedin.com/pulse/introduction-redhat-ceph-storage-mostafa-saber

[^1_50]: https://support.hpe.com/hpesc/public/docDisplay?docId=sd00001995en_us\&page=GUID-B3D30BAF-91FB-465B-BBBB-20407CFAA4E3.html

[^1_51]: https://access.redhat.com/articles/red_hat_compliance_NCSC_cloud_services

[^1_52]: https://metricshub.com/success-stories/how-metricshub-helped-a-major-telco-achieve-unified-observability-at-scale-in-just-5-months/

[^1_53]: https://www.nist.gov/system/files/documents/itl/cloud/NIST_SP-500-291_Version-2_2013_June18_FINAL.pdf

[^1_54]: https://infohub.delltechnologies.com/p/dell-telecom-infrastructure-blocks-for-red-hat-now-with-greater-flexibility/

[^1_55]: https://docs.okd.io/4.17/edge_computing/day_2_core_cnf_clusters/observability/telco-observability.html

[^1_56]: https://www.redhat.com/en/resources/unify-telecommunications-ops-with-rhel-overview

[^1_57]: https://www.redhat.com/en/blog/managing-nist-800-53-controls-in-a-multicluster-openshift-environment-part-1

[^1_58]: https://www.ibm.com/products/instana/supported-technologies/openshift-monitoring

[^1_59]: https://nvlpubs.nist.gov/nistpubs/ir/2022/Nist.IR.8320.pdf

[^1_60]: https://docs.okd.io/4.19/observability/network_observability/understanding-network-observability-operator.html

[^1_61]: https://trilio.io/products/red-hat-openstack-platform-backup-and-recovery/

[^1_62]: https://cloud.google.com/compute/docs/containers/openshift-ha

[^1_63]: https://trilio.io/products/openstack-backup-and-recovery/

[^1_64]: https://docs.redhat.com/en/documentation/openshift_container_platform/3.11/html/cluster_administration/admin-guide-high-availability

[^1_65]: https://superuser.openinfra.org/articles/tutorial-rear-openstack-deployment/

[^1_66]: https://www.youtube.com/watch?v=_YT3mB-jyzQ

[^1_67]: https://www.youtube.com/watch?v=_QNwS9hCjgw

[^1_68]: https://www.vinchin.com/disaster-recovery/openstack-disaster-recovery.html

[^1_69]: https://clouddocs.f5.com/containers/latest/userguide/multicluster/

[^1_70]: https://www.redhat.com/pt-br/blog/telco-autonomous-networks-choosing-the-right-cloud-and-framework

[^1_71]: https://docs.okd.io/4.19/virt/backup_restore/virt-disaster-recovery.html

[^1_72]: https://www.reddit.com/r/kubernetes/comments/171kzc2/moving_from_a_traditional_legacy_ha_vm_setup_to/

[^1_73]: https://ppl-ai-code-interpreter-files.s3.amazonaws.com/web/direct-files/0ebc84da52dcf069bf2cc864c0e0b411/d832fbbe-5362-4269-9de2-2350792f7073/04bec8bd.md

