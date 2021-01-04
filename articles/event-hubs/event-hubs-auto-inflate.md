---
title: Automatisches Hochskalieren von Durchsatzeinheiten – Azure Event Hubs | Microsoft-Dokumentation
description: Aktivieren der automatischen Vergrößerung in einem Namespace zur automatischen Hochskalierung der Durchsatzeinheiten
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1be564472011622b71b3066495748dfdbe6cc791
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020805"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Automatisches Hochskalieren von Azure Event Hub-Durchsatzeinheiten
Azure Event Hubs ist eine extrem skalierbare Datenstreamingplattform. Daher nimmt die Event Hubs-Nutzung häufig zu, nachdem mit der Nutzung des Diensts begonnen wurde. Für solche Nutzung müssen die vordefinierten [Durchsatzeinheiten](event-hubs-scalability.md#throughput-units) zur Skalierung von Event Hubs und zur Verarbeitung größerer Übertragungsraten erweitert werden. Das Feature **Automatische Vergrößerung** von Event Hubs wird automatisch durch Erhöhung der Anzahl von Durchsatzeinheiten zentral hochskaliert, um den Nutzungsanforderungen gerecht zu werden. Zusätzliche Durchsatzeinheiten verhindern Drosselungsszenarien, bei denen Folgendes vorliegt:

* Die Datenerfassungsrate überschreitet die festgelegten Durchsatzeinheiten.
* Die Raten für ausgehende Datenanforderungen überschreiten die festgelegten Durchsatzeinheiten.

Der Event Hubs-Dienst erhöht den Durchsatz, wenn die Last den minimalen Schwellenwert überschreitet, ohne dass Anforderungen mit ServerBusy-Fehlern auftreten.

## <a name="how-auto-inflate-works"></a>Funktionsweise der automatischen Vergrößerung

Der Datenverkehr von Event Hubs wird von [Durchsatzeinheiten](event-hubs-scalability.md#throughput-units) gesteuert. Eine einzelne Durchsatzeinheit lässt eingehenden Datenverkehr von 1 MB pro Sekunde und die doppelte Menge an ausgehendem Datenverkehr zu. Event Hubs Standard kann mit 1 bis 20 Durchsatzeinheiten konfiguriert werden. Dank der automatischen Vergrößerung können Sie zunächst mit den mindestens erforderlichen Durchsatzeinheiten, die Sie auswählen, beginnen. Je nach der Zunahme des Datenverkehrs passt die Funktion die Durchsatzeinheiten automatisch bis zur maximal erforderlichen Anzahl an. Die automatische Vergrößerung hat folgende Vorteile:

- Effizienter Skalierungsmechanismus, der eine Hochskalierung entsprechend der Zunahme des Datenverkehrs ermöglicht
- Automatische Skalierung bis zur festgelegten Obergrenze ohne Probleme aufgrund einer Drosselung
- Mehr Kontrolle über die Skalierung, da Sie bestimmen, wann in welchem Umfang skaliert wird

## <a name="enable-auto-inflate-on-a-namespace"></a>Aktivieren der automatischen Vergrößerung in einem Namespace

Sie können automatische Vergrößerung in einem Event Hubs-Namespace des Standard-Tarifs mit einer der folgenden Methoden aktivieren oder deaktivieren:

- [Azure-Portal](https://portal.azure.com)
- Eine [Azure Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate)

> [!NOTE]
> Event Hubs-Namespaces des Tarifs Basic unterstützen keine automatische Vergrößerung.

### <a name="enable-auto-inflate-through-the-portal"></a>Aktivieren der automatischen Vergrößerung über das Portal


#### <a name="enable-at-the-time-of-creation"></a>Aktivieren zum Zeitpunkt der Erstellung 
Sie können die Funktion für die automatische Vergrößerung aktivieren, **wenn Sie einen Event Hubs-Namespace erstellen**:
 
![Aktivieren der automatischen Vergrößerung zum Zeitpunkt der Event Hub-Erstellung](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Wenn diese Option aktiviert ist, können Sie mit wenigen Durchsatzeinheiten beginnen und die Anzahl mit zunehmenden Nutzungsanforderungen anpassen. Die Obergrenze für die Vergrößerung hat keine direkte Auswirkungen auf den Preis. Dieser hängt von der Anzahl von genutzten Durchsatzeinheiten pro Stunde ab.

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>Aktivieren der automatischen Vergrößerung für einen vorhandenen Event Hub
Mithilfe der folgenden Anweisungen können Sie auch die Funktion der automatischen Vergrößerung aktivieren und deren Einstellungen ändern: 
 
1. Wählen Sie auf der Seite **Event Hubs-Namespace** die Option **Deaktiviert** unter **Auto-inflate throughput units**  (Durchsatzeinheiten automatisch vergrößern) aus.  

    ![Auswählen von Durchsatzeinheiten auf der Seite des Event Hubs-Namespace](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. Aktivieren Sie auf der Seite **Scale Settings** (Skalierungseinstellungen) das Kontrollkästchen **Aktivieren** (sofern die Autoskalierungsfunktion nicht aktiviert war).

    ![Auswählen von „Aktivieren“](./media/event-hubs-auto-inflate/scale-settings.png)
3. Geben Sie die **maximale** Anzahl von Durchsatzeinheiten ein, oder verwenden Sie die Scrollleiste, um den Wert festzulegen. 
4. (optional) Aktualisieren Sie die **minimale** Anzahl von Durchsatzeinheiten am oberen Rand dieser Seite. 


> [!NOTE]
> Wenn Sie die Konfiguration zur automatischen Vergrößerung anwenden, um die Durchsatzeinheiten zu erhöhen, sendet der Event Hubs-Dienst Diagnoseprotokolle, die Informationen dazu enthalten, warum und wann der Durchsatz gestiegen ist. Um die Diagnoseprotokollierung für einen Event Hub zu aktivieren, wählen Sie im Azure-Portal auf der „Event Hub“-Seite im linken Menü **Diagnoseeinstellungen** aus. Weitere Informationen finden Sie unter [Einrichten von Diagnoseprotokollen für einen Event Hub in Azure](event-hubs-diagnostic-logs.md). 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Aktivieren der automatischen Vergrößerung mithilfe einer Azure Resource Manager-Vorlage

Sie können die automatische Vergrößerung bei der Bereitstellung einer Azure Resource Manager-Vorlage aktivieren. Legen Sie beispielsweise die Eigenschaft `isAutoInflateEnabled` auf **TRUE** und `maximumThroughputUnits` auf 10 fest. Beispiel:

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

Die vollständige Vorlage finden Sie unter [Create Event Hubs namespace and enable inflate (Erstellen eines Event Hubs-Namespace und aktivieren der Vergrößerung)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) auf GitHub.


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Event Hubs finden Sie unter den folgenden Links:

* [Übersicht über Event Hubs](./event-hubs-about.md)
