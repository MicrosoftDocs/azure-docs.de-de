---
title: Azure Functions-Bereitstellungsslots
description: Erfahren Sie, wie Sie Bereitstellungsslots mit Azure Functions erstellen und verwenden.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.openlocfilehash: 87d7d4676c604ca7219b7580eb3ce585282a7f11
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327239"
---
# <a name="azure-functions-deployment-slots"></a>Azure Functions-Bereitstellungsslots

Azure Functions-Bereitstellungsslots ermöglichen Ihrer Funktions-App das Ausführen verschiedener Instanzen, die als „Slots“ bezeichnet werden. Slots sind verschiedene Umgebungen, die über einen öffentlich verfügbaren Endpunkt verfügbar gemacht werden. Eine App-Instanz wird immer dem Produktionsslot zugeordnet, und Sie können Instanzen, die einem Slot zugewiesen sind, bei Bedarf austauschen. Funktions-Apps, die im App Service-Plan ausgeführt werden, verfügen möglicherweise über mehrere Slots, während im Verbrauchsplan nur ein Slot zulässig ist.

Im Folgenden wird dargestellt, wie sich der Austausch von Slots auf Funktionen auswirkt:

- Die Umleitung des Datenverkehrs erfolgt nahtlos, und es gehen keine Anforderungen aufgrund des Tauschs verloren.
- Wenn eine Funktion während eines Austauschs ausgeführt wird, wird die Ausführung fortgesetzt, und die nächsten Trigger werden an die getauscht App-Instanz weitergeleitet.

> [!NOTE]
> Slots sind für den Linux-Verbrauch-Plan derzeit nicht verfügbar.

## <a name="why-use-slots"></a>Gründe für die Verwendung von Slots

Es gibt eine Reihe von Vorteilen durch die Verwendung von Bereitstellungsslots. In den folgenden Szenarien werden gängige Verwendungsmöglichkeiten für Slots beschrieben:

- **Verschiedene Umgebungen für unterschiedliche Zwecke**: Die Verwendung verschiedener Slots bietet Ihnen die Möglichkeit, App-Instanzen vor dem Austauschen in den Produktions- oder einen Stagingslot zu unterscheiden.
- **Vorwärmen**: Wenn Sie in einem Slot anstatt direkt in der Produktionsumgebung bereitstellen, können Sie die App vor dem Livebetrieb vorwärmen. Außerdem reduziert die Verwendung von Slots die Latenzzeit für HTTP-ausgelöste Workloads. Instanzen werden vor der Bereitstellung vorgewärmt, wodurch sich der Kaltstart bei neu bereitgestellten Funktionen verkürzt.
- **Einfache Fallbacks**: Nach einem Austausch mit der Produktion enthält der Slot mit der vorherigen Staging-App die vorherige Produktions-App. Wenn die in den Produktionsslot überführten Änderungen nicht Ihren Erwartungen entsprechen, können Sie den Austausch sofort wieder umkehren, um Ihre „letzte als funktionierend bekannte Instanz“ wiederherzustellen.

## <a name="swap-operations"></a>Austauschvorgänge

Während eines Austauschs wird ein Slot als Quelle und der andere als Ziel betrachtet. Der Quellslot enthält die Instanz der Anwendung, die auf den Zielslot angewendet wird. Die folgenden Schritte stellen sicher, dass es beim Zielslot während eines Austauschs zu keinen Ausfallzeiten kommt:

1. **Einstellungen anwenden:** Einstellungen aus dem Zielslot werden auf alle Instanzen des Quellslot angewendet. Beispielsweise werden die Produktionseinstellungen auf die Staginginstanz angewendet. Die angewendeten Einstellungen umfassen die folgenden Kategorien:
    - [Slotspezifische](#manage-settings) App-Einstellungen und Verbindungszeichenfolgen (sofern zutreffend)
    - Einstellungen für [Continuous Deployment](../app-service/deploy-continuous-deployment.md) (sofern aktiviert)
    - Einstellungen für die [App Service-Authentifizierung](../app-service/overview-authentication-authorization.md) (sofern aktiviert)

1. **Warten auf Neustarts und Verfügbarkeit:** Der Austausch wartet, bis jede Instanz im Quellslot ihren Neustart abgeschlossen hat und für Anforderungen verfügbar ist. Sollte eine Instanz nicht erfolgreich neu gestartet werden, werden sämtliche Änderungen am Quellslot zurückgesetzt, und der Austauschvorgang wird beendet.

1. **Routing aktualisieren:** Nach erfolgreicher Vorwärmung aller Instanzen im Quellslot schließen die beiden Slots den Austausch ab, indem die Routingregeln ausgetauscht werden. Nach diesem Schritt befindet sich die App, die zuvor im Quellslot vorbereitet wurde, im Zielslot (also beispielsweise im Produktionsslot).

1. **Vorgang wiederholen:** Nachdem der Quellslot nun die App vor dem Austausch enthält, die sich zuvor im Zielslot befand, führen Sie den gleichen Vorgang erneut aus, indem alle Einstellungen angewendet und die Instanzen für den Quellslot neu gestartet werden.

Berücksichtigen Sie dabei Folgendes:

- In jeder Phase des Austauschvorgangs erfolgt die Initialisierung der ausgetauschten Apps im Quellslot. Der Zielslot bleibt während der gesamten Vorbereitung des Quellslots online – unabhängig davon, ob der Austausch erfolgreich ist oder nicht.

- Wenn Sie einen Stagingslot gegen den Produktionsslot austauschen möchten, muss der Produktionsslot *immer* der Zielslot sein. So ist sichergestellt, dass Ihre Produktions-App durch den Austauschvorgang nicht beeinträchtigt wird.

- Einstellungen im Zusammenhang mit Ereignisquellen und -bindungen müssen als [Bereitstellungssloteinstellungen](#manage-settings) konfiguriert werden, *bevor Sie einen Austausch starten*. Wenn Sie vorzeitig als „sticky“ (persistent) markiert werden, wird sichergestellt, dass Ereignisse und Ausgaben an die richtige Instanz weitergeleitet werden.

## <a name="manage-settings"></a>Verwalten von Einstellungen

Einige Konfigurationseinstellungen sind slotspezifisch. In den nachstehenden Listen finden Sie die Einstellungen, die sich beim Austauschen von Slots ändern und die unverändert bleiben.

**Slotspezifische Einstellungen:**

* Veröffentlichungsendpunkte
* Benutzerdefinierte Domänennamen
* Nicht öffentliche Zertifikate und TLS/SSL-Einstellungen
* Skalierungseinstellungen
* WebJobs-Planer
* IP-Einschränkungen
* Always On
* Diagnoseeinstellungen
* Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS)

**Nicht slotspezifische Einstellungen:**

* Allgemeine Einstellungen (z. B. Framework-Version, 32/64-Bit-Angabe, WebSockets)
* App-Einstellungen (können so konfiguriert werden, dass sie beim Slot verbleiben)
* Verbindungszeichenfolgen (können so konfiguriert werden, dass sie beim Slot verbleiben)
* Handlerzuordnungen
* Öffentliche Zertifikate
* WebJobs-Inhalte
* Hybridverbindungen *
* Virtual Network-Integration*
* Dienstendpunkte*
* Azure Content Delivery Network*

Für mit einem Sternchen (*) gekennzeichnete Features ist eine Rückgängigmachung des Austauschs geplant. 

> [!NOTE]
> Bestimmte App-Einstellungen, die für nicht ausgetauschte Einstellungen gelten, werden ebenfalls nicht ausgetauscht. Da die Diagnoseprotokolleinstellungen beispielsweise nicht ausgetauscht werden, werden verwandte App-Einstellungen wie `WEBSITE_HTTPLOGGING_RETENTION_DAYS` und `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` ebenfalls nicht ausgetauscht, auch wenn Sie nicht als Sloteinstellungen angezeigt werden.
>

### <a name="create-a-deployment-setting"></a>Erstellen einer Bereitstellungseinstellung

Sie können Einstellungen als Bereitstellungseinstellung markieren, wodurch Sie „sticky“ werden. Eine persistente (sticky) Einstellung wird nicht zusammen mit der App-Instanz ausgetauscht.

Wenn Sie eine Bereitstellungseinstellung in einem Slot erstellen, achten Sie darauf, dass Sie dieselbe Einstellung mit einem eindeutigen Wert in allen anderen Slots erstellen, die an einem Austausch beteiligt sind. Auf diese Weise bleiben die Einstellungsnamen zwischen Slots konsistent, während sich der Wert einer Einstellung nicht ändert. Diese Namenskonsistenz stellt sicher, dass Ihr Code nicht versucht, auf eine Einstellung zuzugreifen, die in einem Slot definiert ist, in einem anderen aber nicht.

Führen Sie die folgenden Schritte aus, um eine Bereitstellungseinstellung zu erstellen:

1. Navigieren Sie in der Funktions-App zu **Bereitstellungsslots**, und wählen Sie dann den Slotnamen aus.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Suchen Sie im Azure-Portal nach Slots." border="true":::

1. Wählen Sie **Konfiguration** aus, und wählen Sie den Namen der Einstellung aus, die für den aktuellen Slot persistent sein soll.

    :::image type="content" source="./media/functions-deployment-slots/functions-configure-deployment-slot.png" alt-text="Konfigurieren der Anwendungseinstellung für einen Slot im Azure-Portal." border="true":::

1. Wählen Sie **Bereitstellungssloteinstellung** aus, und wählen Sie dann **OK** aus.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-setting.png" alt-text="Konfigurieren der Bereitstellungssloteinstellung." border="true":::

1. Sobald der Abschnitt mit den Einstellungen nicht mehr angezeigt wird, wählen Sie **Speichern** aus, um die Änderungen beizubehalten.

    :::image type="content" source="./media/functions-deployment-slots/functions-save-deployment-slot-setting.png" alt-text="Speichern der Bereitstellungssloteinstellung." border="true":::

## <a name="deployment"></a>Bereitstellung

Slots sind leer, wenn Sie einen Slot erstellen. Sie können jede der [unterstützten Bereitstellungstechnologien](./functions-deployment-technologies.md) verwenden, um Ihre Anwendung in einem Slot bereitzustellen.

## <a name="scaling"></a>Skalierung

Alle Slots werden auf dieselbe Anzahl von Workern wie der Produktionsslot skaliert.

- Bei Verbrauch-Plänen wird der Slot analog der App skaliert.
- Bei App Service-Plänen wird die App auf eine feste Anzahl von Workern skaliert. Slots werden auf derselben Anzahl von Workern wie der App-Plan ausgeführt.

## <a name="add-a-slot"></a>Hinzufügen eines Slots

Sie können einen Slot über die [CLI](/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create) oder über das Portal hinzufügen. In den folgenden Schritten wird veranschaulicht, wie Sie einen neuen Slot im Portal erstellen:

1. Navigieren Sie zu Ihrer Funktions-App.

1. Wählen Sie **Bereitstellungsslots** aus, und wählen Sie dann **+ Slot hinzufügen** aus.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add.png" alt-text="Hinzufügen eines Azure Functions-Bereitstellungsslots." border="true":::

1. Geben Sie den Namen des Slots ein, und wählen Sie dann **Hinzufügen** aus.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add-name.png" alt-text="Benennen des Azure Functions-Bereitstellungsslots." border="true":::

## <a name="swap-slots"></a>Austauschen von Slots

Sie können Slot über die [CLI](/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap) oder über das Portal austauschen. In den folgenden Schritten wird veranschaulicht, wie Sie Slots im Portal austauschen:

1. Navigieren Sie zur Funktionen-App.
1. Wählen Sie **Bereitstellungssloteinstellung** aus, und wählen Sie dann **Austauschen** aus.

    :::image type="content" source="./media/functions-deployment-slots/functions-swap-deployment-slot.png" alt-text="Screenshot der Seite „Bereitstellungsslot“, auf der die Aktion „Slot hinzufügen“ ausgewählt ist" border="true":::

1. Überprüfen Sie die Konfigurationseinstellungen für den Austausch, und wählen Sie dann **Austauschen** aus.
    
    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png" alt-text="Austauschen des Bereitstellungsslots." border="true":::

Der Vorgang kann einen Augenblick dauern, während der Austauschvorgang ausgeführt wird.

## <a name="roll-back-a-swap"></a>Ausführen eines Rollbacks für einen Austausch

Wenn ein Austausch zu einem Fehler führt oder Sie einen Austausch einfach „rückgängig“ machen möchten, können Sie ein Rollback auf den ursprünglichen Zustand ausführen. Um zum Zustand vor dem Austausch zurückzukehren, führen Sie einen weiteren Austausch aus, um den Austausch umzukehren.

## <a name="remove-a-slot"></a>Entfernen eines Slots

Sie können einen Slot über die [CLI](/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete) oder über das Portal entfernen. In den folgenden Schritten wird veranschaulicht, wie Sie einen Slot im Portal entfernen:

1. Navigieren Sie in der Funktions-App zu **Bereitstellungsslots**, und wählen Sie dann den Slotnamen aus.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Suchen Sie im Azure-Portal nach Slots." border="true":::

1. Klicken Sie auf **Löschen**.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot.png" alt-text="Screenshot der Seite „Übersicht“, auf dem die Aktion „Löschen“ ausgewählt ist" border="true":::

1. Geben Sie den Namen des Bereitstellungsslots ein, den Sie löschen möchten, und wählen Sie dann **Löschen** aus.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot-details.png" alt-text="Löschen des Bereitstellungsslots im Azure-Portal." border="true":::

1. Schließen Sie den Bestätigungsbereich für den Löschvorgang.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-deleted.png" alt-text="Bestätigung zum Löschen des Bereitstellungsslots." border="true":::

## <a name="automate-slot-management"></a>Automatisieren der Slotverwaltung

Mithilfe der [Azure CLI](/cli/azure/functionapp/deployment/slot?view=azure-cli-latest) können Sie die folgenden Aktionen für einen Slot automatisieren:

- [erstellen](/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [delete](/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- [list](/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-list)
- [swap](/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [auto-swap](/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>Ändern des App Service-Plans

Bei einer Funktions-App, die in einem App Service-Plan ausgeführt wird, können Sie den zugrunde liegenden App Service-Plan für einen Slot zu ändern.

> [!NOTE]
> Unter einem Verbrauch-Plan können Sie den App Service-Plan eines Slots nicht ändern.

Führen Sie die folgenden Schritte aus, um den App Service-Plan eines Slots zu ändern:

1. Navigieren Sie in der Funktions-App zu **Bereitstellungsslots**, und wählen Sie dann den Slotnamen aus.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Suchen Sie im Azure-Portal nach Slots." border="true":::

1. Wählen Sie unter **App Service-Plan**  die Option **App Service-Plan ändern** aus.

1. Wählen Sie den Plan aus, auf den Sie ein Upgrade durchführen möchten, oder erstellen Sie einen neuen Plan.

    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-apply.png" alt-text="Ändern des App Service-Plans im Azure-Portal." border="true":::

1. Klicken Sie auf **OK**.

## <a name="limitations"></a>Einschränkungen

Azure Functions-Bereitstellungsslots unterliegen folgenden Einschränkungen:

- Die Anzahl der für einen App verfügbaren Slots hängt vom Plan ab. Im Verbrauch-Plan ist nur ein Bereitstellungsslot zulässig. Zusätzliche Slots sind für Apps verfügbar, die unter dem App Service-Plan ausgeführt werden.
- Durch den Austausch eines Slots werden Schlüssel für Apps zurückgesetzt, die über eine App-Einstellung für `AzureWebJobsSecretStorageType` von `files` verfügen.
- Slots sind für den Linux-Verbrauchsplan nicht verfügbar.

## <a name="support-levels"></a>Supportstufen

Es gibt zwei Ebenen der Unterstützung für Bereitstellungsslots:

- **Allgemeine Verfügbarkeit (General Availability, GA)** : Vollständige Unterstützung und Freigabe für die Verwendung in Produktionsumgebungen.
- **Vorschau**: Noch nicht unterstützt, die Erreichung der allgemeinen Verfügbarkeit wird aber für die Zukunft erwartet.

| Betriebssystem/Hostingplan           | Unterstützungsebene     |
| ------------------------- | -------------------- |
| Windows: Verbrauch       | Allgemeine Verfügbarkeit |
| Windows Premium           | Allgemeine Verfügbarkeit  |
| Windows: Dediziert         | Allgemeine Verfügbarkeit |
| Linux: Verbrauch         | Nicht unterstützt          |
| Linux Premium             | Allgemeine Verfügbarkeit  |
| Linux: Dediziert           | Allgemeine Verfügbarkeit |

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellungstechnologien in Azure Functions](./functions-deployment-technologies.md)
