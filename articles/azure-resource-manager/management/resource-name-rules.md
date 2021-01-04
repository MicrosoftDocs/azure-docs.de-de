---
title: Einschränkungen bei Ressourcennamen
description: Zeigt die Benennungsregeln und -einschränkungen für Azure-Ressourcen
ms.topic: conceptual
ms.date: 12/04/2020
ms.openlocfilehash: a6aac03277a11cf671f5a618bc85ff987cfdc2dd
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608315"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Benennungsregeln und -einschränkungen für Azure-Ressourcen

Dieser Artikel bietet einen Überblick über die Benennungsregeln und -einschränkungen für Azure-Ressourcen. Empfehlungen zum Benennen von Ressourcen finden Sie unter [Empfohlene Namens- und Kennzeichnungskonventionen](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

In diesem Artikel werden Ressourcen nach dem Ressourcenanbieter-Namespace aufgelistet. Eine Übersicht über die Zuordnung von Ressourcenanbietern und Azure-Diensten finden Sie unter [Ressourcenanbieter für Azure-Dienste](azure-services-resource-providers.md).

Bei Ressourcennamen wird nicht zwischen Groß- und Kleinschreibung unterschieden, es sei denn, dies ist in der Spalte für gültige Zeichen ausdrücklich angegeben.

In den folgenden Tabellen bezieht sich der Begriff alphanumerisch auf:

* **a** bis **z** (Kleinbuchstaben)
* **A** bis **Z** (Großbuchstaben)
* **0** bis **9** (Zahlen)

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | servers | Ressourcengruppe | 3-63 | Kleinbuchstaben und Zahlen.<br><br>Beginnen Sie mit einem Kleinbuchstaben. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | Dienst | Global | 1-50 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen Sie mit einem Buchstaben, und enden Sie mit einem alphanumerischen Zeichen. |
> | service/apis | Dienst | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/apis/issues | api | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/apis/issues/attachments | Problem | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/apis/issues/comments | Problem | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/apis/operations | api | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/apis/operations/tags | operation | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/apis/releases | api | 1-80 | Alphanumerische Zeichen, Unterstriche und Bindestriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen oder Unterstrich. |
> | service/apis/schemas | api | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/apis/tagDescriptions | api | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/apis/tags | api | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/api-version-sets | Dienst | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/authorizationServers | Dienst | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/backends | Dienst | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/certificates | Dienst | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/diagnostics | Dienst | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/groups | Dienst | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/groups/users | group | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/identityProviders | Dienst | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/loggers | Dienst | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/notifications | Dienst | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/notifications/recipientEmails | Benachrichtigung | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/openidConnectProviders | Dienst | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/policies | Dienst | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/products | Dienst | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/products/apis | product | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/products/groups | product | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/products/tags | product | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/properties | Dienst | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/subscriptions | Dienst | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/tags | Dienst | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/templates | Dienst | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |
> | service/users | Dienst | 1–256 | Verwendung nicht möglich:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | configurationStores | Ressourcengruppe | 5 - 50 | Alphanumerische Zeichen, Unterstriche und Bindestriche. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | locks | Zuweisungsbereich | 1-90 | Alphanumerische Zeichen, Punkte, Unterstriche, Bindestriche und Klammern.<br><br>Darf nicht mit einem Punkt enden. |
> | policyAssignments | Zuweisungsbereich | 1–128 Anzeigename<br><br>1-64 Ressourcenname | Der Anzeigename kann beliebige Zeichen enthalten.<br><br>Der Ressourcenname darf nicht `%` enthalten und nicht mit einem Punkt oder Leerzeichen enden. |
> | policyDefinitions | Definitionsbereich | 1–128 Anzeigename<br><br>1-64 Ressourcenname | Der Anzeigename kann beliebige Zeichen enthalten.<br><br>Der Ressourcenname darf nicht `%` enthalten und nicht mit einem Punkt oder Leerzeichen enden. |
> | policySetDefinitions | Definitionsbereich | 1–128 Anzeigename<br><br>1-64 Ressourcenname<br><br>1-24 Ressourcenname im Verwaltungsgruppenbereich | Der Anzeigename kann beliebige Zeichen enthalten.<br><br>Der Ressourcenname darf nicht `%` enthalten und nicht mit einem Punkt oder Leerzeichen enden.  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | automationAccounts | Ressourcengruppe | 6-50 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen Sie mit einem Buchstaben, und enden Sie mit einem alphanumerischen Zeichen. |
> | automationAccounts/certificates | Automation-Konto | 1–128 | Verwendung nicht möglich:<br> `<>*%&:\?.+/` <br><br>Darf nicht mit einem Leerzeichen enden.  |
> | automationAccounts/connections | Automation-Konto | 1–128 | Verwendung nicht möglich:<br> `<>*%&:\?.+/` <br><br>Darf nicht mit einem Leerzeichen enden. |
> | automationAccounts/credentials | Automation-Konto | 1–128 | Verwendung nicht möglich:<br> `<>*%&:\?.+/` <br><br>Darf nicht mit einem Leerzeichen enden. |
> | automationAccounts/runbooks | Automation-Konto | 1 - 63 | Alphanumerische Zeichen, Unterstriche und Bindestriche.<br><br>Beginnen Sie mit einem Buchstaben.  |
> | automationAccounts/schedules | Automation-Konto | 1–128 | Verwendung nicht möglich:<br> `<>*%&:\?.+/` <br><br>Darf nicht mit einem Leerzeichen enden. |
> | automationAccounts/variables | Automation-Konto | 1–128 | Verwendung nicht möglich:<br> `<>*%&:\?.+/` <br><br>Darf nicht mit einem Leerzeichen enden. |
> | automationAccounts/watchers | Automation-Konto | 1 - 63 |  Alphanumerische Zeichen, Unterstriche und Bindestriche.<br><br>Beginnen Sie mit einem Buchstaben. |
> | automationAccounts/webhooks | Automation-Konto | 1–128 | Verwendung nicht möglich:<br> `<>*%&:\?.+/` <br><br>Darf nicht mit einem Leerzeichen enden. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | batchAccounts | Region | 3-24 | Kleinbuchstaben und Zahlen. |
> | batchAccounts/applications | Batch-Konto | 1-64 | Alphanumerische Zeichen, Unterstriche und Bindestriche. |
> | batchAccounts/certificates | Batch-Konto | 5–45 | Alphanumerische Zeichen, Unterstriche und Bindestriche. |
> | batchAccounts/pools | Batch-Konto | 1-64 | Alphanumerische Zeichen, Unterstriche und Bindestriche. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | blockchainMembers | Global | 2–20 | Kleinbuchstaben und Zahlen.<br><br>Beginnen Sie mit einem Kleinbuchstaben. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | botServices | Global | 2 - 64 |  Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. |
> | botServices/channels | Bot Service | 2 - 64 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. |
> | botServices/Connections | Bot Service | 2 - 64 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. |
> | enterpriseChannels | Ressourcengruppe | 2 - 64 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | Redis | Global | 1 - 63 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. Aufeinanderfolgende Bindestriche sind nicht zulässig. |
> | Redis/firewallRules | Redis | 1–256 | Alphanumerische Zeichen |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | profiles | Ressourcengruppe | 1–260 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |
> | profiles/endpoints | Global | 1-50 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | certificateOrders | Ressourcengruppe | 3–30 | Alphanumerische Zeichen. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | accounts | Ressourcengruppe | 2 - 64 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | availabilitySets | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | diskEncryptionSets | Ressourcengruppe | 1-80 | Alphanumerische Zeichen und Unterstriche. |
> | disks | Ressourcengruppe | 1-80 | Alphanumerische Zeichen und Unterstriche. |
> | galleries | Ressourcengruppe | 1-80 | Alphanumerische Zeichen und Punkte.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |
> | galleries/applications | Katalog | 1-80 | Alphanumerische Zeichen, Bindestriche und Punkte.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |
> | galleries/applications/versions | application | 32-bit integer | Zahlen und Punkte. |
> | galleries/images | Katalog | 1-80 | Alphanumerische Zeichen, Bindestriche und Punkte.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |
> | galleries/images/versions | image | 32-bit integer | Zahlen und Punkte. |
> | images | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | snapshots | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | virtualMachines | Ressourcengruppe | 1–15 (Windows)<br>1–64 (Linux)<br><br>Siehe Hinweis weiter unten. | Leerzeichen oder die folgenden Zeichen dürfen nicht verwendet werden:<br> `\/"'[]:|<>+=;,?*@&_`<br><br>Virtuelle Windows-Computer dürfen keine Punkte enthalten oder mit Bindestrichen enden.<br><br>Linux-VMs dürfen nicht mit einem Punkt oder Bindestrich enden. |
> | virtualMachineScaleSets | Ressourcengruppe | 1–15 (Windows)<br>1–64 (Linux)<br><br>Siehe Hinweis weiter unten. | Leerzeichen oder die folgenden Zeichen dürfen nicht verwendet werden:<br> `\/"'[]:|<>+=;,?*@&`<br><br>Darf nicht mit einem Unterstrich beginnen. Darf nicht mit einem Punkt oder Bindestrich enden. |

> [!NOTE]
> Virtuelle Azure-Computer weisen zwei getrennte Namen auf: einen Ressourcennamen und einen Hostnamen. Wenn Sie im Portal einen virtuellen Computer erstellen, wird für beide Namen der gleiche Wert verwendet. Die Einschränkungen in der obigen Tabelle gelten für den Hostnamen. Der eigentliche Ressourcenname kann bis zu 64 Zeichen lang sein.

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | containerGroups | Ressourcengruppe | 1 - 63 | Kleinbuchstaben, Zahlen und Bindestriche.<br><br>Darf nicht mit einem Bindestrich beginnen oder enden. Aufeinanderfolgende Bindestriche sind nicht zulässig. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | registries | Global | 5 - 50 | Alphanumerische Zeichen. |
> | registries/buildTasks | Registrierung | 5 - 50 | Alphanumerische Zeichen. |
> | registries/buildTasks/steps | Buildtask | 5 - 50 | Alphanumerische Zeichen. |
> | registries/replications | Registrierung | 5 - 50 | Alphanumerische Zeichen. |
> | registries/scopeMaps | Registrierung | 5 - 50 | Alphanumerische Zeichen, Bindestriche und Unterstriche. |
> | registries/tasks | Registrierung | 5 - 50 | Alphanumerische Zeichen, Bindestriche und Unterstriche. |
> | registries/tokens | Registrierung | 5 - 50 | Alphanumerische Zeichen, Bindestriche und Unterstriche. |
> | registries/webhooks | Registrierung | 5 - 50 | Alphanumerische Zeichen. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | managedClusters | Ressourcengruppe | 1 - 63 | Alphanumerische Zeichen, Unterstriche und Bindestriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |
> | openShiftManagedClusters | Ressourcengruppe | 1–30 | Alphanumerische Zeichen. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | hubs | Ressourcengruppe | 1-64 | Alphanumerische Zeichen.<br><br>Beginnen Sie mit einem Buchstaben.  |
> | hubs/authorizationPolicies | Hub | 1-50 | Alphanumerische Zeichen, Unterstriche und Punkte.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |
> | hubs/connectors | Hub | 1–128 | Alphanumerische Zeichen und Unterstriche.<br><br>Beginnen Sie mit einem Buchstaben. |
> | hubs/connectors/mappings | Connector | 1–128 | Alphanumerische Zeichen und Unterstriche.<br><br>Beginnen Sie mit einem Buchstaben. |
> | hubs/interactions | Hub | 1–128 | Alphanumerische Zeichen und Unterstriche.<br><br>Beginnen Sie mit einem Buchstaben. |
> | hubs/kpi | Hub | 1–512 | Alphanumerische Zeichen und Unterstriche.<br><br>Beginnen Sie mit einem Buchstaben. |
> | hubs/links | Hub | 1–512 | Alphanumerische Zeichen und Unterstriche.<br><br>Beginnen Sie mit einem Buchstaben. |
> | hubs/predictions | Hub | 1–512 | Alphanumerische Zeichen und Unterstriche.<br><br>Beginnen Sie mit einem Buchstaben. |
> | hubs/profiles | Hub | 1–128 | Alphanumerische Zeichen und Unterstriche.<br><br>Beginnen Sie mit einem Buchstaben. |
> | hubs/relationshipLinks | Hub | 1–512 | Alphanumerische Zeichen und Unterstriche.<br><br>Beginnen Sie mit einem Buchstaben. |
> | hubs/relationships | Hub | 1–512 | Alphanumerische Zeichen und Unterstriche.<br><br>Beginnen Sie mit einem Buchstaben. |
> | hubs/roleAssignments | Hub | 1–128 | Alphanumerische Zeichen und Unterstriche.<br><br>Beginnen Sie mit einem Buchstaben. |
> | hubs/views | Hub | 1–512 | Alphanumerische Zeichen und Unterstriche.<br><br>Beginnen Sie mit einem Buchstaben. |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | associations | Ressourcengruppe | 1–180 | Verwendung nicht möglich:<br>`%&\\?/`<br><br>Darf nicht mit einem Punkt oder Leerzeichen enden. |
> | resourceProviders | Ressourcengruppe | 3–64 | Verwendung nicht möglich:<br>`%&\\?/`<br><br>Darf nicht mit einem Punkt oder Leerzeichen enden. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | jobs | Ressourcengruppe | 3-24 | Alphanumerische Zeichen, Bindestriche, Unterstriche und Punkte. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | workspaces | Ressourcengruppe | 3–30 | Alphanumerische Zeichen, Unterstriche und Bindestriche |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | factories | Global | 3-63 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |
> | factories/dataflows | Fabrik | 1–260 | Verwendung nicht möglich:<br>`<>*#.%&:\\+?/`<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. |
> | factories/datasets | Fabrik | 1–260 | Verwendung nicht möglich:<br>`<>*#.%&:\\+?/`<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. |
> | factories/integrationRuntimes | Fabrik | 3-63 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |
> | factories/linkedservices | Fabrik | 1–260 | Verwendung nicht möglich:<br>`<>*#.%&:\\+?/`<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. |
> | factories/pipelines | Fabrik | 1–260 | Verwendung nicht möglich:<br>`<>*#.%&:\\+?/`<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. |
> | factories/triggers | Fabrik | 1–260 | Verwendung nicht möglich:<br>`<>*#.%&:\\+?/`<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. |
> | factories/triggers/rerunTriggers | Trigger (trigger) | 1–260 | Verwendung nicht möglich:<br>`<>*#.%&:\\+?/`<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | accounts | Global | 3-24 | Kleinbuchstaben und Zahlen. |
> | accounts/computePolicies | account | 3–60 | Alphanumerische Zeichen, Bindestriche und Unterstriche. |
> | accounts/dataLakeStoreAccounts | account | 3-24 | Kleinbuchstaben und Zahlen. |
> | accounts/firewallRules | account | 3-50 | Alphanumerische Zeichen, Bindestriche und Unterstriche. |
> | accounts/storageAccounts | account | 3–60 | Alphanumerische Zeichen, Bindestriche und Unterstriche. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | accounts | Global | 3-24 | Kleinbuchstaben und Zahlen. |
> | accounts/firewallRules | account | 3-50 | Alphanumerische Zeichen, Bindestriche und Unterstriche. |
> | accounts/virtualNetworkRules | account | 3-50 | Alphanumerische Zeichen, Bindestriche und Unterstriche. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | services | Ressourcengruppe | 2–62 | Alphanumerische Zeichen, Bindestriche, Punkte und Unterstriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. |
> | services/projects | Dienst | 2–57 | Alphanumerische Zeichen, Bindestriche, Punkte und Unterstriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | servers | Global | 3-63 | Kleinbuchstaben, Bindestriche und Zahlen.<br><br>Darf nicht mit einem Bindestrich beginnen oder enden. |
> | servers/databases | servers | 1 - 63 | Alphanumerische Zeichen und Bindestriche. |
> | servers/firewallRules | servers | 1–128 | Alphanumerische Zeichen, Bindestriche und Unterstriche. |
> | servers/virtualNetworkRules | servers | 1–128 | Alphanumerische Zeichen und Bindestriche. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | servers | Global | 3-63 | Kleinbuchstaben, Bindestriche und Zahlen.<br><br>Darf nicht mit einem Bindestrich beginnen oder enden. |
> | servers/databases | servers | 1 - 63 | Alphanumerische Zeichen und Bindestriche. |
> | servers/firewallRules | servers | 1–128 | Alphanumerische Zeichen, Bindestriche und Unterstriche. |
> | servers/virtualNetworkRules | servers | 1–128 | Alphanumerische Zeichen und Bindestriche. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | servers | Global | 3-63 | Kleinbuchstaben, Bindestriche und Zahlen.<br><br>Darf nicht mit einem Bindestrich beginnen oder enden. |
> | servers/databases | servers | 1 - 63 | Alphanumerische Zeichen und Bindestriche. |
> | servers/firewallRules | servers | 1–128 | Alphanumerische Zeichen, Bindestriche und Unterstriche. |
> | servers/virtualNetworkRules | servers | 1–128 | Alphanumerische Zeichen und Bindestriche. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | IotHubs | Global | 3-50 | Alphanumerische Zeichen und Bindestriche.<br><br>Darf nicht mit einem Bindestrich enden. |
> | IotHubs/certificates | IoT Hub | 1-64 | Alphanumerische Zeichen, Bindestriche, Punkte und Unterstriche. |
> | IotHubs/eventHubEndpoints/ConsumerGroups | eventHubEndpoints | 1-50 | Alphanumerische Zeichen, Bindestriche, Punkte und Unterstriche. |
> | provisioningServices | Ressourcengruppe | 3–64 | Alphanumerische Zeichen und Bindestriche.<br><br>Enden Sie mit einem alphanumerischen Zeichen. |
> | provisioningServices/certificates | provisioningServices | 1-64 | Alphanumerische Zeichen, Bindestriche, Punkte und Unterstriche. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | labs | Ressourcengruppe | 1-50 | Alphanumerische Zeichen, Unterstriche und Bindestriche. |
> | labs/customimages | Lab | 1-80 | Alphanumerische Zeichen, Unterstriche, Bindestriche und Klammern. |
> | labs/formulas | Lab | 1-80 | Alphanumerische Zeichen, Unterstriche, Bindestriche und Klammern. |
> | labs/virtualmachines | Lab | 1–15 (Windows)<br>1–64 (Linux) | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. Darf nicht nur aus Zahlen bestehen. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | databaseAccounts | Global | 3-44 | Kleinbuchstaben, Zahlen und Bindestriche.<br><br>Beginnen Sie mit einem Kleinbuchstaben oder einer Zahl. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | domains | Ressourcengruppe | 3-50 | Alphanumerische Zeichen und Bindestriche. |
> | domains/topics | Domäne | 3-50 | Alphanumerische Zeichen und Bindestriche. |
> | eventSubscriptions | Ressourcengruppe | 3–64 | Alphanumerische Zeichen und Bindestriche. |
> | topics | Ressourcengruppe | 3-50 | Alphanumerische Zeichen und Bindestriche. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | clusters | Ressourcengruppe | 6-50 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen Sie mit einem Buchstaben. Enden Sie mit einem Buchstaben oder einer Zahl. |
> | Namespaces | Global | 6-50 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen Sie mit einem Buchstaben. Enden Sie mit einem Buchstaben oder einer Zahl. |
> | namespaces/AuthorizationRules | Namespace | 1-50 | Alphanumerische Zeichen, Punkte, Bindestriche und Unterstriche.<br><br>Beginnen und enden Sie mit einem Buchstaben oder einer Zahl. |
> | namespaces/disasterRecoveryConfigs | Namespace | 1-50 | Alphanumerische Zeichen, Punkte, Bindestriche und Unterstriche.<br><br>Beginnen und enden Sie mit einem Buchstaben oder einer Zahl. |
> | namespaces/eventhubs | Namespace | 1-50 | Alphanumerische Zeichen, Punkte, Bindestriche und Unterstriche.<br><br>Beginnen und enden Sie mit einem Buchstaben oder einer Zahl. |
> | namespaces/eventhubs/authorizationRules | Event Hub | 1-50 | Alphanumerische Zeichen, Punkte, Bindestriche und Unterstriche.<br><br>Beginnen und enden Sie mit einem Buchstaben oder einer Zahl. |
> | namespaces/eventhubs/consumergroups | Event Hub | 1-50 | Alphanumerische Zeichen, Punkte, Bindestriche und Unterstriche.<br><br>Beginnen und enden Sie mit einem Buchstaben oder einer Zahl. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | clusters | Global | 3–59 | Alphanumerische Zeichen und Bindestriche<br><br>Beginnen und enden Sie mit einem Buchstaben oder einer Zahl. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | jobs | Ressourcengruppe | 2 - 64 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen Sie mit einem Buchstaben. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | actionGroups | Ressourcengruppe | 1–260 | Verwendung nicht möglich:<br>`/&%\?` <br><br>Darf nicht mit einem Leerzeichen oder Punkt enden.  |
> | components | Ressourcengruppe | 1–260 | Verwendung nicht möglich:<br>`%&\?/` <br><br>Darf nicht mit einem Leerzeichen oder Punkt enden.  |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | IoTApps | Global | 2–63 | Kleinbuchstaben, Zahlen und Bindestriche.<br><br>Beginnen Sie mit einem Kleinbuchstaben oder einer Zahl. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | vaults | Global | 3-24 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen Sie mit einem Buchstaben. Enden Sie mit einem Buchstaben oder einer Ziffer. Darf keine aufeinanderfolgenden Bindestriche enthalten. |
> | vaults/secrets | Tresor | 1–127 | Alphanumerische Zeichen und Bindestriche. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | clusters | Global | 4–22 | Kleinbuchstaben und Zahlen.<br><br>Beginnen Sie mit einem Buchstaben. |
> | /clusters/databases | cluster | 1–260 | Alphanumerische Zeichen, Bindestriche, Leerzeichen und Punkte. |
> | /clusters/databases/dataConnections | database | 1–40 | Alphanumerische Zeichen, Bindestriche, Leerzeichen und Punkte. |
> | /clusters/databases/eventhubconnections | database | 1–40 | Alphanumerische Zeichen, Bindestriche, Leerzeichen und Punkte. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | integrationAccounts | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Bindestriche, Unterstriche, Punkte und Klammern. |
> | integrationAccounts/assemblies | Integrationskonto | 1-80 | Alphanumerische Zeichen, Bindestriche, Unterstriche, Punkte und Klammern. |
> | integrationAccounts/batchConfigurations | Integrationskonto | 1–20 | Alphanumerische Zeichen. |
> | integrationAccounts/certificates | Integrationskonto | 1-80 | Alphanumerische Zeichen, Bindestriche, Unterstriche, Punkte und Klammern. |
> | integrationAccounts/maps | Integrationskonto | 1-80 | Alphanumerische Zeichen, Bindestriche, Unterstriche, Punkte und Klammern. |
> | integrationAccounts/partners | Integrationskonto | 1-80 | Alphanumerische Zeichen, Bindestriche, Unterstriche, Punkte und Klammern. |
> | integrationAccounts/rosettanetprocessconfigurations | Integrationskonto | 1-80 | Alphanumerische Zeichen, Bindestriche, Unterstriche, Punkte und Klammern. |
> | integrationAccounts/schemas | Integrationskonto | 1-80 | Alphanumerische Zeichen, Bindestriche, Unterstriche, Punkte und Klammern. |
> | integrationAccounts/sessions | Integrationskonto | 1-80 | Alphanumerische Zeichen, Bindestriche, Unterstriche, Punkte und Klammern. |
> | integrationServiceEnvironments | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Bindestriche, Punkte und Unterstriche. |
> | integrationServiceEnvironments/managedApis | Integrationsdienstumgebung | 1-80 | Alphanumerische Zeichen, Bindestriche, Punkte und Unterstriche. |
> | workflows | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Bindestriche, Unterstriche, Punkte und Klammern. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | commitmentPlans | Ressourcengruppe | 1–260 | Verwendung nicht möglich:<br>`<>*%&:?+/\\`<br><br>Darf nicht mit einem Leerzeichen enden. |
> | webServices | Ressourcengruppe | 1–260 | Verwendung nicht möglich:<br>`<>*%&:?+/\\`<br><br>Darf nicht mit einem Leerzeichen enden. |
> | workspaces | Ressourcengruppe | 1–260 | Verwendung nicht möglich:<br>`<>*%&:?+/\\`<br><br>Darf nicht mit einem Leerzeichen enden. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | workspaces | Ressourcengruppe | 3–33 | Alphanumerische Zeichen und Bindestriche. |
> | workspaces/computes | Arbeitsbereich | 2–16 | Alphanumerische Zeichen und Bindestriche. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | userAssignedIdentities | Ressourcengruppe | 3–128 | Alphanumerische Zeichen, Bindestriche und Unterstriche<br><br>Beginnen Sie mit einem Buchstaben oder einer Zahl. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | accounts | Ressourcengruppe | 1–98 (Ressourcengruppenname und Kontoname) | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | mediaservices | Ressourcengruppe | 3-24 | Kleinbuchstaben und Zahlen. |
> | mediaservices/liveEvents | Mediendienst | 1–32 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. |
> | mediaservices/liveEvents/liveOutputs | Liveereignis | 1–256 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. |
> | mediaservices/streamingEndpoints | Mediendienst | 1-24 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | applicationGateways | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | applicationSecurityGroups | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | azureFirewalls | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | bastionHosts | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | connections | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | dnsZones | Ressourcengruppe | 1–63 Zeichen<br><br>2–34 Bezeichnungen<br><br>Jede Bezeichnung ist ein Satz von Zeichen, die durch einen Punkt voneinander getrennt sind. Beispielsweise weist **contoso.com** 2 Bezeichnungen auf. | Jede Bezeichnung kann alphanumerische Zeichen, Unterstriche und Bindestriche enthalten.<br><br>Jede Bezeichnung ist durch einen Punkt getrennt. |
> | expressRouteCircuits | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | firewallPolicies | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | firewallPolicies/ruleGroups | Firewallrichtlinie | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | frontDoors | Global | 5–64 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |
> | frontdoorWebApplicationFirewallPolicies | Ressourcengruppe | 1–128 | Alphanumerische Zeichen.<br><br>Beginnen Sie mit einem Buchstaben. |
> | loadBalancers | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | loadBalancers/inboundNatRules | Lastenausgleich | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | localNetworkGateways | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | networkInterfaces | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | networkSecurityGroups | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | networkSecurityGroups/securityRules | Netzwerksicherheitsgruppe | 1-80 |  Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | networkWatchers | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | privateDnsZones | Ressourcengruppe | 1–63 Zeichen<br><br>2–34 Bezeichnungen<br><br>Jede Bezeichnung ist ein Satz von Zeichen, die durch einen Punkt voneinander getrennt sind. Beispielsweise weist **contoso.com** 2 Bezeichnungen auf. | Jede Bezeichnung kann alphanumerische Zeichen, Unterstriche und Bindestriche enthalten.<br><br>Jede Bezeichnung ist durch einen Punkt getrennt. |
> | privateDnsZones/virtualNetworkLinks | Private DNS-Zone | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | publicIPAddresses | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | publicIPPrefixes | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | routeFilters | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | routeFilters/routeFilterRules | Routenfilter | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | routeTables | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | routeTables/routes | Routingtabelle | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | serviceEndpointPolicies | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | trafficmanagerprofiles | Global | 1 - 63 | Alphanumerische Zeichen, Bindestriche und Punkte.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |
> | virtualNetworkGateways | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | virtualNetworks | Ressourcengruppe | 2 - 64 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | virtualnetworks/subnets | Virtuelles Netzwerk | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | virtualNetworks/virtualNetworkPeerings | Virtuelles Netzwerk | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | virtualWans | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | vpnGateways | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | vpnGateways/vpnConnections | VPN-Gateway | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |
> | vpnSites | Ressourcengruppe | 1-80 | Alphanumerische Zeichen, Unterstriche, Punkte und Bindestriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. Enden Sie mit einem alphanumerischen Zeichen oder einem Unterstrich. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | Namespaces | Global | 6-50 | Alphanumerische Zeichen und Bindestriche<br><br>Beginnen Sie mit einem Buchstaben. Enden Sie mit einem alphanumerischen Zeichen. |
> | namespaces/AuthorizationRules | Namespace | 1–256 | Alphanumerische Zeichen, Punkte, Bindestriche und Unterstriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. |
> | namespaces/notificationHubs | Namespace | 1–260 | Alphanumerische Zeichen, Punkte, Bindestriche und Unterstriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. |
> | namespaces/notificationHubs/AuthorizationRules | Benachrichtigungshub | 1–256 | Alphanumerische Zeichen, Punkte, Bindestriche und Unterstriche.<br><br>Beginnen Sie mit einem alphanumerischen Zeichen. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | clusters | Ressourcengruppe | 4–63 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |
> | workspaces | Ressourcengruppe | 4–63 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | solutions | Arbeitsbereich | – | Bei von Microsoft erstellten Lösungen muss der Name folgendes Muster aufweisen:<br>`SolutionType(WorkspaceName)`<br><br>Bei von Drittanbietern erstellten Lösungen muss der Name folgendes Muster aufweisen:<br>`SolutionType[WorkspaceName]`<br><br>Ein gültiger Name ist beispielsweise:<br>`AntiMalware(contoso-IT)`<br><br>Beim Lösungstyp wird die Groß-/Kleinschreibung beachtet. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | dashboards | Ressourcengruppe | 3 - 160 | Alphanumerische Zeichen und Bindestriche.<br><br>Fügen Sie zur Verwendung von eingeschränkten Zeichen das Tag **hidden-title** mit dem gewünschten Dashboardnamen hinzu. Im Portal wird dieser Name angezeigt, wenn das Dashboard eingeblendet wird. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | workspaceCollections | region | 3-63 | Alphanumerische Zeichen und Bindestriche.<br><br>Darf nicht mit einem Bindestrich beginnen. Darf keine aufeinanderfolgenden Bindestriche enthalten. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | capacities | region | 3-63 | Kleinbuchstaben oder Zahlen.<br><br>Beginnen Sie mit einem Kleinbuchstaben. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | vaults | Ressourcengruppe | 2–50 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen Sie mit einem Buchstaben. |
> | vaults/backupPolicies | Tresor | 3–150 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen Sie mit einem Buchstaben. Darf nicht mit einem Bindestrich enden. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | Namespaces | Global | 6-50 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen Sie mit einem Buchstaben. Enden Sie mit einem Buchstaben oder einer Zahl. |
> | namespaces/AuthorizationRules | Namespace | 1-50 |  Alphanumerische Zeichen, Punkte, Bindestriche und Unterstriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |
> | namespaces/HybridConnections | Namespace | 1–260 | Alphanumerische Zeichen, Punkte, Bindestriche, Unterstriche und Schrägstriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |
> | namespaces/HybridConnections/authorizationRules | Hybridverbindung | 1-50 | Alphanumerische Zeichen, Punkte, Bindestriche und Unterstriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |
> | namespaces/WcfRelays | Namespace | 1–260 | Alphanumerische Zeichen, Punkte, Bindestriche, Unterstriche und Schrägstriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |
> | namespaces/WcfRelays/authorizationRules | WCF-Relay | 1-50 | Alphanumerische Zeichen, Punkte, Bindestriche und Unterstriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | deployments | Ressourcengruppe | 1-64 | Alphanumerische Zeichen, Unterstriche, Klammern, Bindestriche und Punkte. |
> | resourcegroups | Abonnement | 1-90 | Alphanumerische Zeichen, Unterstriche, Klammern, Bindestriche, Punkte und Unicode-Zeichen entsprechend der [Dokumentation zu regulären Ausdrücken](/rest/api/resources/resourcegroups/createorupdate).<br><br>Darf nicht mit einem Punkt enden. |
> | tagNames | resource | 1–512 | Verwendung nicht möglich:<br>`<>%&\?/` |
> | tagNames/tagValues | Tagname | 1–256 | Alle Zeichen. |
> | templateSpecs | Ressourcengruppe | 1-90 | Alphanumerische Zeichen, Unterstriche, Klammern, Bindestriche und Punkte. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | Namespaces | Global | 6-50 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen Sie mit einem Buchstaben. Enden Sie mit einem Buchstaben oder einer Zahl.<br><br>Weitere Informationen finden Sie unter [Erstellen des Namespaces](/rest/api/servicebus/create-namespace). |
> | namespaces/AuthorizationRules | Namespace | 1-50 | Alphanumerische Zeichen, Punkte, Bindestriche und Unterstriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |
> | namespaces/disasterRecoveryConfigs | Global | 6-50 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen Sie mit einem Buchstaben. Enden Sie mit einem alphanumerischen Zeichen. |
> | namespaces/migrationConfigurations | Namespace |  | Sollte immer **$default** sein. |
> | namespaces/queues | Namespace | 1–260 | Alphanumerische Zeichen, Punkte, Bindestriche, Unterstriche und Schrägstriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |
> | namespaces/queues/authorizationRules | queue | 1-50 | Alphanumerische Zeichen, Punkte, Bindestriche und Unterstriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |
> | namespaces/topics | Namespace | 1–260 | Alphanumerische Zeichen, Punkte, Bindestriche, Unterstriche und Schrägstriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |
> | namespaces/topics/authorizationRules | topic | 1-50 | Alphanumerische Zeichen, Punkte, Bindestriche und Unterstriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |
> | namespaces/topics/subscriptions | topic | 1-50 | Alphanumerische Zeichen, Punkte, Bindestriche und Unterstriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |
> | namespaces/topics/subscriptions/rules | Abonnement | 1-50 | Alphanumerische Zeichen, Punkte, Bindestriche und Unterstriche.<br><br>Beginnen und enden Sie mit einem alphanumerischen Zeichen. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | clusters | region | 4–23 | Kleinbuchstaben, Zahlen und Bindestriche.<br><br>Beginnen Sie mit einem Kleinbuchstaben. Enden Sie mit einem Kleinbuchstaben oder einer Zahl. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | signalR | Global | 3-63 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen Sie mit einem Buchstaben. Enden Sie mit einem Buchstaben oder einer Zahl.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | managedInstances | Global | 1 - 63 | Kleinbuchstaben, Zahlen und Bindestriche.<br><br>Darf nicht mit einem Bindestrich beginnen oder enden. |
> | servers | Global | 1 - 63 | Kleinbuchstaben, Zahlen und Bindestriche.<br><br>Darf nicht mit einem Bindestrich beginnen oder enden. |
> | servers/administrators | server |  | Muss `ActiveDirectory`lauten. |
> | servers/databases | server | 1–128 | Verwendung nicht möglich:<br>`<>*%&:\/?`<br><br>Darf nicht mit einem Punkt oder Leerzeichen enden. |
> | servers/databases/syncGroups | database | 1–150 | Alphanumerische Zeichen, Bindestriche und Unterstriche. |
> | servers/elasticPools | server | 1–128 | Verwendung nicht möglich:<br>`<>*%&:\/?`<br><br>Darf nicht mit einem Punkt oder Leerzeichen enden. |
> | servers/failoverGroups | Global | 1 - 63 | Kleinbuchstaben, Zahlen und Bindestriche.<br><br>Darf nicht mit einem Bindestrich beginnen oder enden. |
> | servers/firewallRules | server | 1–128 | Verwendung nicht möglich:<br>`<>*%&:;\/?`<br><br>Darf nicht mit einem Punkt enden. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | storageAccounts | Global | 3-24 | Kleinbuchstaben und Zahlen. |
> | storageAccounts/blobServices | Speicherkonto |  | Muss `default`lauten. |
> | storageAccounts/blobServices/containers | Speicherkonto | 3-63 | Kleinbuchstaben, Zahlen und Bindestriche.<br><br>Beginnen Sie mit einem Kleinbuchstaben oder einer Zahl. Darf keine aufeinanderfolgenden Bindestriche enthalten. |
> | storageAccounts/fileServices | Speicherkonto |  | Muss `default`lauten. |
> | storageAccounts/fileServices/shares | Speicherkonto | 3-63 | Kleinbuchstaben, Zahlen und Bindestriche.<br><br>Darf nicht mit einem Bindestrich beginnen oder enden. Darf keine aufeinanderfolgenden Bindestriche enthalten. |
> | storageAccounts/managementPolicies | Speicherkonto |  | Muss `default`lauten. |
> | Blob | Container | 1-1024 | Alle URL-Zeichen, Groß-/Kleinschreibung beachten |
> | queue | Speicherkonto | 3-63 | Kleinbuchstaben, Zahlen und Bindestriche.<br><br>Darf nicht mit einem Bindestrich beginnen oder enden. Darf keine aufeinanderfolgenden Bindestriche enthalten. |
> | table | Speicherkonto | 3-63 | Alphanumerische Zeichen.<br><br>Beginnen Sie mit einem Buchstaben. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | storageSyncServices | Ressourcengruppe | 1–260 | Alphanumerische Zeichen, Leerzeichen, Punkte, Bindestriche und Unterstriche.<br><br>Darf nicht mit einem Punkt oder Leerzeichen enden. |
> | storageSyncServices/syncGroups | Speichersynchronisierungsdienst | 1–260 | Alphanumerische Zeichen, Leerzeichen, Punkte, Bindestriche und Unterstriche.<br><br>Darf nicht mit einem Punkt oder Leerzeichen enden. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | managers | Ressourcengruppe | 2–50 | Alphanumerische Zeichen und Bindestriche.<br><br>Beginnen Sie mit einem Buchstaben. Enden Sie mit einem alphanumerischen Zeichen. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | streamingjobs | Ressourcengruppe | 3-63 | Alphanumerische Zeichen, Bindestriche und Unterstriche. |
> | streamingjobs/functions | Streamingauftrag | 3-63 | Alphanumerische Zeichen, Bindestriche und Unterstriche. |
> | streamingjobs/inputs | Streamingauftrag | 3-63 | Alphanumerische Zeichen, Bindestriche und Unterstriche. |
> | streamingjobs/outputs | Streamingauftrag | 3-63 | Alphanumerische Zeichen, Bindestriche und Unterstriche. |
> | streamingjobs/transformations | Streamingauftrag | 3-63 | Alphanumerische Zeichen, Bindestriche und Unterstriche. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | environments | Ressourcengruppe | 1-90 | Verwendung nicht möglich:<br>`'<>%&:\?/#` |
> | environments/accessPolicies | Environment | 1-90 | Verwendung nicht möglich:<br> `'<>%&:\?/#` |
> | environments/eventSources | Environment | 1-90 | Verwendung nicht möglich:<br>`'<>%&:\?/#` |
> | environments/referenceDataSets | Environment | 3-63 | Alphanumerische Zeichen |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Entität | `Scope` | Länge | Gültige Zeichen |
> | --- | --- | --- | --- |
> | certificates | Ressourcengruppe | 1–260 | Verwendung nicht möglich:<br>`/` <br><br>Darf nicht mit einem Leerzeichen oder Punkt enden.  | 
> | serverfarms | Ressourcengruppe | 1–40 | Alphanumerische Zeichen und Bindestriche. |
> | sites | Global | 2-60 | Alphanumerische Zeichen und Bindestriche.<br><br>Darf nicht mit einem Bindestrich beginnen oder enden. |
> | sites/slots | site | 2–59 | Alphanumerische Zeichen und Bindestriche. |

> [!NOTE]
> Azure Functions weist dieselben Benennungsregeln und -einschränkungen auf wie Microsoft.Web/sites.

## <a name="next-steps"></a>Nächste Schritte

Empfehlungen zum Benennen von Ressourcen finden Sie unter [Bereitschaft: Empfohlene Namens- und Kennzeichnungskonventionen](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).
