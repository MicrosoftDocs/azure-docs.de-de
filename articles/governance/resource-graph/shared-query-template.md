---
title: 'Schnellstart: Erstellen einer freigegebenen Abfrage mit Vorlagen'
description: In dieser Schnellstartanleitung verwenden Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage), um eine freigegebene Resource Graph-Abfrage zu erstellen, mit der virtuelle Computer nach Betriebssystem gezählt werden.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 8d631ffcb14af93f10e578097470efc6156287d5
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594315"
---
# <a name="quickstart-create-a-shared-query-by-using-an-arm-template"></a>Schnellstart: Erstellen einer freigegebenen Abfrage unter Verwendung einer Resource Manager-Vorlage

Resource Graph-Abfragen können als _private Abfrage_ oder _freigegebene Abfrage_ gespeichert werden. Eine private Abfrage wird im Portalprofil eines Benutzers gespeichert und ist für andere Benutzer nicht sichtbar. Eine freigegebene Abfrage ist ein Resource Manager-Objekt, das über Berechtigungen und rollenbasierten Zugriff für andere Benutzer freigegeben werden kann. Eine freigegebene Abfrage ermöglicht eine allgemeine und konsistente Ausführung der Ressourcenermittlung. In dieser Schnellstartanleitung wird eine Azure Resource Manager-Vorlage (ARM-Vorlage) zum Erstellen einer freigegebenen Abfrage verwendet.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Bereitstellen der ARM-Vorlage zum Erstellen einer freigegebenen Abfrage für Azure" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

In dieser Schnellstartanleitung erstellen Sie eine freigegebene Abfrage namens _VMs nach Betriebssystem zählen_. Informationen zum Ausprobieren dieser Abfrage im SDK oder im Portal mit dem Resource Graph-Explorer finden Sie unter [Zählen von virtuellen Computern nach Betriebssystemtyp](./samples/starter.md#count-os).

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/).

:::code language="json" source="~/quickstart-templates/resourcegraph-sharedquery-countos/azuredeploy.json":::

In der Vorlage ist die folgende Ressource definiert:

- [Microsoft.ResourceGraph/queries](/azure/templates/microsoft.resourcegraph/queries)

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

> [!NOTE]
> Der Azure Resource Graph-Dienst ist kostenlos. Weitere Informationen finden Sie unter [Was ist Azure Resource Graph?](./overview.md).

1. Wählen Sie die folgende Abbildung aus, um sich am Azure-Portal anzumelden und die Vorlage zu öffnen:

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Bereitstellen der ARM-Vorlage zum Erstellen einer freigegebenen Abfrage für Azure" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

1. Wählen Sie die folgenden Werte aus, bzw. geben Sie sie ein:

   | Name | Wert |
   |------|-------|
   | Subscription | Wählen Sie Ihr Azure-Abonnement. |
   | Resource group | Wählen Sie **Neu erstellen**, geben Sie einen Namen an, und wählen Sie dann **OK**. |
   | Standort | Wählen Sie eine Region aus. Beispiel: **USA, Mitte**. |
   | Abfragename | Behalten Sie den Standardwert bei: **VMs nach Betriebssystem zählen**. |
   | Abfragecode | Behalten Sie den Standardwert bei: `Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)`. |
   | Abfragebeschreibung | Behalten Sie den Standardwert bei: **Diese freigegebene Abfrage zählt alle VM-Ressourcen und fasst sie nach Betriebssystemtyp zusammen.** |
   | Ich stimme den oben genannten Geschäftsbedingungen zu | (Auswählen) |

1. Wählen Sie die Option **Kaufen**.

Einige zusätzliche Ressourcen:

- Weitere Beispielvorlagen finden Sie unter [Azure-Schnellstartvorlage](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Navigieren Sie zur [Azure-Vorlagenreferenz](/azure/templates/microsoft.resourcegraph/allversions), um die Vorlagenreferenz anzuzeigen.
- Informationen zur Entwicklung von ARM-Vorlagen finden Sie in der [Azure Resource Manager-Dokumentation](../../azure-resource-manager/management/overview.md).
- Informationen zur Bereitstellung auf Abonnementebene finden Sie unter [Erstellen von Ressourcengruppen und Ressourcen auf Abonnementebene](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

Führen Sie die folgenden Schritte aus, um die neue freigegebene Abfrage auszuführen:

1. Suchen Sie über die Suchleiste des Portals nach **Resource Graph-Abfragen**, und wählen Sie die Option aus.

1. Wählen Sie die freigegebene Abfrage mit dem Namen **VMs nach Betriebssystem zählen** und dann auf der Seite **Übersicht** die Registerkarte **Ergebnisse** aus.

Sie können die freigegebene Abfrage auch über den Resource Graph-Explorer öffnen:

1. Suchen Sie über die Suchleiste des Portals nach **Resource Graph-Explorer**, und wählen Sie die Option aus.

1. Wählen Sie die Schaltfläche **Abfrage öffnen** aus.

1. Ändern Sie **Typ** in _Freigegebene Abfragen_. Wird die Abfrage **VMs nach Betriebssystem zählen** in der Liste nicht angezeigt, verwenden Sie das Filterfeld zum Eingrenzen der Ergebnisse. Wenn die freigegebene Abfrage **VMs nach Betriebssystem zählen** angezeigt wird, wählen Sie den Namen aus.

1. Wählen Sie nach dem Laden der Abfrage die Schaltfläche **Abfrage ausführen** aus. Die Ergebnisse werden auf der Registerkarte **Ergebnisse** angezeigt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Um die erstellte freigegebene Abfrage zu entfernen, gehen Sie wie folgt vor:

1. Suchen Sie über die Suchleiste des Portals nach **Resource Graph-Abfragen**, und wählen Sie die Option aus.

1. Aktivieren Sie das Kontrollkästchen neben der freigegebenen Abfrage **VMs nach Betriebssystem zählen**.

1. Wählen Sie oben auf der Seite die Schaltfläche **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine freigegebene Resource Graph-Abfrage erstellt.

Weitere Informationen zu freigegebenen Abfragen finden Sie in folgendem Tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Erstellen und Freigeben einer Azure Resource Graph-Abfrage im Azure-Portal](./tutorials/create-share-query.md)