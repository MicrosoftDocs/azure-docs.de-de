---
title: Upgrade eines Azure Service Fabric-Clusters | Microsoft Docs
description: Upgraden Sie den Service Fabric-Code und/oder die Konfiguration für die Ausführung eines Service Fabric-Clusters, und machen Sie sich unter anderem mit dem Festlegen des Clusteraktualisierungsmodus, dem Upgraden von Zertifikaten, dem Hinzufügen von Anwendungsports und dem Anwenden von Betriebssystempatches vertraut. Was können Sie erwarten, wenn die Upgrades durchgeführt werden?
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: aljo
ms.openlocfilehash: 234bff5049babf0c4b1d036b40201720b2736228
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60714724"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>Aktualisieren der Service Fabric-Version eines Clusters

Moderner Systeme müssen upgradefähig sein, um den langfristigen Erfolg Ihres Produkts zu gewährleisten. Ein Azure Service Fabric-Cluster ist eine Ressource, die Sie besitzen, die jedoch teilweise von Microsoft verwaltet wird. In diesem Artikel erfahren Sie, wie Sie die in Ihrem Azure-Cluster ausgeführte Service Fabric-Version upgraden.

Sie können Ihren Cluster so konfigurieren, dass er Fabric-Upgrades automatisch erhält, wenn diese von Microsoft veröffentlicht werden. Alternativ können Sie eine unterstützte Fabric-Version auswählen, die in Ihrem Cluster verwendet werden soll.

Hierzu legen Sie die Clusterkonfiguration „upgradeMode“ über das Portal oder mithilfe von Resource Manager fest – entweder zum Zeitpunkt der Clustererstellung oder später für einen aktiven Cluster. 

> [!NOTE]
> Achten Sie immer darauf, dass in Ihrem Cluster eine unterstützte Fabric-Version ausgeführt wird. Nach der Ankündigung einer neuen Service Fabric-Version beträgt die verbleibende Supportdauer der vorherigen Version noch mindestens 60 Tage. Neue Versionen werden im [Blog des Service Fabric-Teams](https://blogs.msdn.microsoft.com/azureservicefabric/)angekündigt. Ab dann kann die neue Version ausgewählt werden. 
> 
> 

14 Tage vor Ablauf der in Ihrem Cluster verwendeten Version wird ein Integritätsereignis generiert, das den Cluster in einen Warnzustand versetzt. Der Cluster bleibt so lange in dem Warnzustand, bis Sie ein Upgrade auf eine unterstützte Fabric-Version durchführen.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>Festlegen des Upgrademodus im Azure-Portal
Bei der Clustererstellung haben Sie die Wahl zwischen einem automatischen und einem manuellen Modus:

![Create_Manualmode][Create_Manualmode]

Einen aktiven Cluster können Sie über die Verwaltungsoberfläche in den automatischen oder manuellen Modus versetzen. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Upgraden auf eine neue Version in einem Cluster im manuellen Modus über das Portal
Für ein Upgrade auf eine neue Version müssen Sie lediglich die verfügbare Version im Dropdownmenü auswählen und auf „Speichern“ klicken. Das Fabric-Upgrade wird automatisch initiiert. Bei dem Upgrade werden die Clusterintegritätsrichtlinien (eine Kombination aus Knotenintegrität und Integrität aller im Cluster ausgeführten Anwendungen) berücksichtigt.

Wenn die Integritätsrichtlinien des Clusters nicht erfüllt sind, wird das Upgrade zurückgesetzt. Weitere Informationen zum Festlegen dieser benutzerdefinierten Integritätsrichtlinien finden Sie weiter unten in diesem Dokument. 

Beheben Sie die Probleme, die zu dem Rollback geführt haben, und initiieren Sie das Upgrade erneut, indem Sie die gleichen Schritte ausführen wie zuvor.

![Manage_Automaticmode][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>Festlegen des Upgrademodus mithilfe einer Resource Manager-Vorlage
Fügen Sie der Microsoft.ServiceFabric/Clusterressourcendefinition die Konfiguration „upgradeMode“ hinzu, legen Sie „clusterCodeVersion“ wie unten gezeigt auf eine der unterstützten Fabric-Versionen fest, und stellen Sie dann die Vorlage bereit. Gültige Werte für „upgradeMode“ sind „Manual“ und „Automatic“.

![ARMUpgradeMode][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Upgraden auf eine neue Version in einem Cluster im manuellen Modus mithilfe einer Resource Manager-Vorlage
Wenn sich der Cluster im manuellen Modus befindet und Sie ein Upgrade auf eine neue Version durchführen möchten, legen Sie „clusterCodeVersion“ auf eine unterstützte Version fest, und stellen Sie sie bereit. Das Fabric-Upgrade wird durch die Bereitstellung der Vorlage automatisch initiiert. Bei dem Upgrade werden die Clusterintegritätsrichtlinien (eine Kombination aus Knotenintegrität und Integrität aller im Cluster ausgeführten Anwendungen) berücksichtigt.

Wenn die Integritätsrichtlinien des Clusters nicht erfüllt sind, wird das Upgrade zurückgesetzt.  

Beheben Sie die Probleme, die zu dem Rollback geführt haben, und initiieren Sie das Upgrade erneut, indem Sie die gleichen Schritte ausführen wie zuvor.

## <a name="set-custom-health-polices-for-upgrades"></a>Festlegen benutzerdefinierter Integritätsrichtlinien für Upgrades
Sie können benutzerdefinierte Integritätsrichtlinien für Fabric-Upgrades angeben. Wenn Sie Ihren Cluster für automatische Fabric-Upgrades konfiguriert haben, werden diese Richtlinien auf die [erste Phase der automatischen Fabric-Upgrades](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades) angewendet.
Wenn sich der Cluster im manuellen Fabric-Upgrademodus befindet, werden diese Richtlinien immer dann angewendet, wenn Sie eine neue Version auswählen und so das Fabric-Upgrade in Ihrem Cluster initiieren. Wenn Sie die Richtlinien nicht überschreiben, werden die Standardwerte verwendet.

Sie können benutzerdefinierte Integritätsrichtlinien angeben oder die aktuellen Einstellungen auf dem Fabric-Upgradeblatt überprüfen, indem Sie die erweiterten Upgradeeinstellungen auswählen. Wie das geht, zeigt die folgende Abbildung: 

![Verwalten benutzerdefinierter Integritätsrichtlinien][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>Auflisten aller verfügbaren Versionen für alle Umgebungen eines bestimmten Abonnements
Führen Sie den folgenden Befehl aus, um eine Ausgabe wie die folgende zu erhalten.

„supportExpiryUtc“ gibt an, wann eine bestimmte Version abläuft oder abgelaufen ist. Die neueste Version besitzt anstelle eines gültigen Datums den Wert „9999-12-31T23:59:59.9999999“, was bedeutet, dass noch kein Ablaufdatum feststeht.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }
```

## <a name="next-steps"></a>Nächste Schritte
* Informieren Sie sich über die Vorgehensweise zum Anpassen einiger [Customize Service Fabric cluster settings and Fabric Upgrade policy](service-fabric-cluster-fabric-settings.md)
* Machen Sie sich mit der Vorgehensweise zum [Skalieren Ihres Clusters](service-fabric-cluster-scale-up-down.md)
* Machen Sie sich mit [Anwendungsupgrades](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
