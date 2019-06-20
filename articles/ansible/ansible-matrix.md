---
title: Matrix der Ansible-Module und -Versionen für Azure | Microsoft-Dokumentation
description: Matrix der Ansible-Module und -Versionen für Azure
keywords: Ansible, Rollen, Matrix, Version, Azure, DevOps
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: b3396b2f4639cc7298b77810dbaafadd308d6b24
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65230980"
---
# <a name="ansible-module-and-version-matrix"></a>Matrix der Ansible-Module und -Versionen

Ansible umfasst eine Suite von Modulen für die Verwendung bei der Bereitstellung und Konfiguration von Azure-Ressourcen. Diese Ressourcen umfassen virtuelle Computer, Skalierungsgruppen, Netzwerkdienste und Containerdienste. In diesem Artikel werden die verschiedenen Ansible-Module für Azure aufgeführt sowie die Ansible-Versionen, zu deren Lieferumfang sie gehören.

## <a name="ansible-modules-for-azure"></a>Ansible-Module für Azure

Die folgenden Module können direkt auf Remotehosts oder über Playbooks ausgeführt werden.

Diese Module sind in dem offiziellen Release von Ansible oder aus den folgenden Microsoft-Playbookrollen erhältlich.

| Ansible-Module für Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2.8 | Ansible-Rolle | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------| 
| **Compute**                    |           |                          |                          |                            |           |           |
| azure_rm_availabilityset                    | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_availabilityset_facts              | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_deployment                         | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_deployment_facts                   | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_functionapp                        | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_functionapp_facts                  | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_image                              | -            | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_image_facts                        | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_resource                           | -            | -                           | Ja          | Ja          | Ja          | Ja          |
| azure_rm_resource_facts                     | -            | -                           | Ja          | Ja          | Ja          | Ja          |
| azure_rm_resourcegroup                      | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_resourcegroup_facts                | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualmachine                     | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualmachine_facts               | -            | -                           | -            | Ja          | Ja          | Ja          |
| azure_rm_virtualmachineextension           | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualmachineextension_facts      | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_virtualmachineimage_facts          | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualmachinescaleset            | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualmachinescaleset_facts      | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_virtualmachinescalesetextension_facts | -            | -                        | -            | -            | Ja          | Ja          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_virtualmachinescalesetinstance_facts | -            | -                         | -            | -            | Ja          | Ja          |
| **Netzwerk**                              |              |                             |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Ja          | Ja          | Ja          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | -          | Ja          |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | -          | Ja          |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | -          | Ja          |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | -          | Ja          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_applicationsecuritygroup_facts     | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Ja          | Ja          |
| azure_rm_cdnendpoint_facts                  | -            | -                         | -          | -            | Ja          | Ja          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Ja          | Ja          |
| azure_rm_cdnprofile_facts                   | -            | -                         | -          | -            | Ja          | Ja          |
| azure_rm_dnsrecordset                       | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_dnsrecordset_facts                 | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_dnszone                            | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_dnszone_facts                      | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_loadbalancer                       | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_loadbalancer_facts                 | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_networkinterface                   | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_networkinterface_facts             | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_publicipaddress                    | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_publicipaddress_facts              | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_route                              | -            | -                           | -            | Ja          | Ja          | Ja          |
| azure_rm_routetable                         | -            | -                           | -            | Ja          | Ja          | Ja          |
| azure_rm_routetable_facts                   | -            | -                           | -            | Ja          | Ja          | Ja          |
| azure_rm_securitygroup                      | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_subnet                             | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_subnet_facts                       | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Ja          | Ja          | Ja          |
| azure_rm_trafficmanagerendpoint_facts       | -            | -                         | -          | Ja          | Ja          | Ja          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Ja          | Ja          | Ja          |
| azure_rm_trafficmanagerprofile_facts        | -            | -                         | -          | Ja          | Ja          | Ja          |
| azure_rm_virtualnetwork                     | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualnetwork_facts               | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Ja          | Ja          |
| **Speicher**                    |           |                          |                          |                            |           |           |
| azure_rm_manageddisk                        | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_manageddisk_facts                  | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_storageaccount                     | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_storageaccount_facts               | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_storageblob                        | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| **Web**                    |           |                          |                          |                             |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Ja          | Ja          | Ja          |
| azure_rm_appserviceplan_facts               | -            | -                         | -          | Ja          | Ja          | Ja          |
| azure_rm_webapp                             | -            | -                         | -          | Ja          | Ja          | Ja          |
| azure_rm_webapp_facts                       | -            | -                         | -          | Ja          | Ja          | Ja          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Ja          | Ja          |
| **Container**                    |           |                          |                          |                            |           |           |
| azure_rm_acs                                | Ja          | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_aks                                | -            | -                           | Ja          | Ja          | Ja          | Ja          |
| azure_rm_aks_facts                          | -            | -                           | Ja          | Ja          | Ja          | Ja          |
| azure_rm_aksversion_facts                   | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_containerinstance                  | -            | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_containerinstance_facts            | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_containerregistry                  | -            | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_containerregistry_facts            | -            | -                           | -            | Ja          | Ja          | Ja          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | Ja          | Ja          |
| **Datenbanken**                    |           |                          |                          |                             |           |           |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_cosmosdbaccount_facts              | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_mariadbconfiguration_facts         | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_mariadbdatabase_facts              | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_mariadbfirewallrule_facts          | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_mariadbserver_facts                | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_mysqldatabase                      | -            | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Ja          | Ja          | Ja          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_mysqlserver                        | -            | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Ja          | Ja          | Ja          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_postgresqldatabase                 | -            | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Ja          | Ja          | Ja          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_postgresqlserver                   | -            | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Ja          | Ja          | Ja          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_rediscache_facts                   | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_sqldatabase                        | -            | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Ja          | Ja          | Ja          |
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_sqlserver                          | -            | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_sqlserver_facts                    | -            | Ja                         | Ja          | Ja          | Ja          | Ja          |
| **Analyse**                    |           |                          |                          |                             |           |           |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Ja          | Ja          |
| **Integration**                    |           |                          |                          |                             |           |           |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_servicebus_facts                   | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Ja          | Ja          |
| **Sicherheit**                    |           |                          |                          |                             |           |           |
| azure_rm_keyvault                           | -            | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -          | Ja          | Ja          |
| azure_rm_keyvaultkey                        | -            | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_keyvaultsecret                     | -            | Ja                         | Ja          | Ja          | Ja          | Ja          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_roleassignment_facts               | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_roledefinition_facts               | -            | -                           | -            | -            | Ja          | Ja          |
| **DevOps**               |           |                          |                          |                             |           |           |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_devtestlab_facts                   | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_devtestlabarmtemplate_facts        | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_devtestlabartifact_facts           | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_devtestlabartifactsource_facts     | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_devtestlabvirtualmachine_facts | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_devtestlabvirtualnetwork_facts     | -            | -                           | -            | -            | Ja          | Ja          |
| **Azure Monitor**          |           |                          |                          |                             |           |           |
| azure_rm_autoscale                  | -            | -                         | -          | Ja          | Ja          | Ja          |
| azure_rm_autoscale_facts            | -            | -                         | -          | Ja          | Ja          | Ja          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Ja          | Ja          |
| azure_rm_loganalyticsworkspace_facts        | -            | -                           | -            | -            | Ja          | Ja          |

## <a name="introduction-to-playbook-role-for-azure"></a>Einführung in die Playbookrolle für Azure

Die [Playbookrolle „azure_preview_module“](https://galaxy.ansible.com/Azure/azure_preview_modules/) enthält alle neuesten Azure-Module. Die Updates und Fehlerbehebungen erfolgen schneller als für das offizielle Ansible-Release. Wenn Sie Ansible zum Bereitstellen von Azure-Ressourcen verwenden, wird empfohlen, die Playbookrolle `azure_preview_module` zu installieren.

Die Playbookrolle `azure_preview_module` wird alle drei Wochen veröffentlicht.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Playbookrollen finden Sie unter [Erstellen wiederverwendbarer Playbooks](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 
