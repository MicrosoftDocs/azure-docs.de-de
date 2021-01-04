---
title: Verwenden von verwalteten Identitäten zum Zugriff auf Event Hubs aus einem Azure Stream Analytics-Auftrag (Vorschau)
description: In diesem Artikel wird beschrieben, wie Sie verwaltete Identitäten verwenden, um Ihre Azure Stream Analytics-Aufträge für die Event Hubs-Eingabe und -Ausgabe zu authentifizieren.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: c65833e5bf581c6326bf9362c7e5fc00a985d301
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96354882"
---
# <a name="use-managed-identities-to-access-event-hubfrom-an-azure-stream-analytics-job-preview"></a>Verwenden von verwalteten Identitäten zum Zugriff auf Event Hubs aus einem Azure Stream Analytics-Auftrag (Vorschau)

Azure Stream Analytics unterstützt die Authentifizierung über verwaltete Identitäten sowohl für Eingaben als auch für Ausgaben von Event Hubs. Verwaltete Identitäten beseitigen die Einschränkungen benutzerbasierter Authentifizierungsmethoden, wie etwa die Notwendigkeit einer erneuten Authentifizierung aufgrund von Kennwortänderungen oder Benutzertoken, die alle 90 Tage ablaufen. Wenn Sie die Notwendigkeit einer manuellen Authentifizierung aufheben, können Ihre Stream Analytics-Bereitstellungen vollständig automatisiert werden.  

Bei einer verwalteten Identität handelt es sich um eine in Azure Active Directory registrierte verwaltete Anwendung, die einen bestimmten Stream Analytics-Auftrag repräsentiert. Diese verwaltete Anwendung wird zur Authentifizierung bei einer Zielressource verwendet, beispielsweise Event Hubs hinter einer Firewall oder in einem virtuellen Netzwerk. Weitere Informationen zum Umgehen von Firewalls finden Sie unter [Gewähren des Zugriffs auf Azure Event Hubs-Namespaces über private Endpunkte](../event-hubs/private-link-service.md#trusted-microsoft-services).

Dieser Artikel veranschaulicht, wie Sie verwaltete Identitäten für die Event Hubs-Eingaben und -Ausgaben eines Stream Analytics-Auftrags über das Azure-Portal aktivieren. Bevor Sie eine verwaltete Identität aktivieren können, müssen Sie über einen Stream Analytics-Auftrag und eine Event Hub-Ressource verfügen.

## <a name="create-a-managedidentity"></a>Erstellen einer verwalteten Identität  

Erstellen Sie zuerst eine verwaltete Identität für den Azure Stream Analytics-Auftrag.  

1. Öffnen Sie den Azure Stream Analytics-Auftrag im Azure-Portal.  

1. Wählen Sie im linken Navigationsmenü unter  *Konfigurieren* die Option  **Verwaltete Identität** aus. Aktivieren Sie dann das Kontrollkästchen neben  **Systemseitig zugewiesene verwaltete Identität verwenden**, und klicken Sie auf  **Speichern**.

   :::image type="content" source="media/event-hubs-managed-identity/system-assigned-managed-identity.png" alt-text="Systemseitig zugewiesene verwaltete Identität":::  

1. Ein Dienstprinzipal für die Identität des Stream Analytics-Auftrags wird in Azure Active Directory erstellt. Der Lebenszyklus der neu erstellten Identität wird von Azure verwaltet. Wenn der Stream Analytics-Auftrag gelöscht wird, wird die zugeordnete Identität (also der Dienstprinzipal) von Azure automatisch ebenfalls gelöscht.  

   Wenn Sie die Konfiguration speichern, wird die Objekt-ID (OID) des Dienstprinzipals als Prinzipal-ID aufgeführt, wie hier gezeigt:  

   :::image type="content" source="media/event-hubs-managed-identity/principal-id.png" alt-text="Prinzipal-ID":::

   Der Dienstprinzipal weist den gleichen Namen auf wie der Stream Analytics-Auftrag. Wenn der Name des Auftrags z. B.  `MyASAJob` lautet, erhält auch der Dienstprinzipal den Namen  `MyASAJob`.  

## <a name="grant-the-stream-analytics-job-permissionsto-access-the-event-hub"></a>Erteilen von Berechtigungen für den Stream Analytics-Auftrag zum Zugriff auf Event Hub

Damit der Stream Analytics-Auftrag über eine verwaltete Identität auf Ihren Event Hub zugreifen kann, muss der von Ihnen erstellte Dienstprinzipal über bestimmte Berechtigungen für den Event Hub verfügen.

1. Wechseln Sie in Ihrem Event Hub zu **Zugriffssteuerung (IAM)** .

1. Wählen Sie **+Hinzufügen** und dann **Rollenzuweisung hinzufügen** aus.

1. Geben Sie auf der Seite **Rollenzuweisung hinzufügen** die folgenden Optionen ein:

   |Parameter|Wert|
   |---------|-----|
   |Role|Azure Event Hubs-Datenbesitzer|
   |Zugriff zuweisen zu|Benutzer, Gruppe oder Dienstprinzipal|
   |Auswählen|Name Ihres Stream Analytics-Auftrags|

   :::image type="content" source="media/event-hubs-managed-identity/add-role-assignment.png" alt-text="Hinzufügen der Rollenzuweisung":::

1. Klicken Sie auf **Speichern**, und warten Sie etwa eine Minute, damit die Änderungen weitergegeben werden können.

Sie können diese Rolle auch auf Ebene des Event Hub-Namespace zuweisen. Damit werden die Berechtigungen automatisch an alle in diesem Namespace erstellten Event Hubs weitergegeben. Das bedeutet, dass alle Event Hubs in einem Namespace als Ressource für die Authentifizierung einer verwalteten Identität in Ihrem Stream Analytics-Auftrag verwendet werden können.

## <a name="create-anevent-hub-input-or-output"></a>Erstellen einer Event Hub-Eingabe oder -Ausgabe  

Nachdem die verwaltete Identität konfiguriert wurde, können Sie dem Stream Analytics-Auftrag die Event Hub-Ressource als Eingabe oder Ausgabe hinzufügen.  

### <a name="add-the-event-hub-as-an-input"></a>Hinzufügen des Event Hubs als Eingabe 

1. Wechseln Sie zum Stream Analytics-Auftrag, und navigieren Sie unter **Auftragstopologie** zur Seite **Eingaben**.

1. Wählen Sie **Datenstromeingabe hinzufügen > Event Hub** aus. Suchen Sie im Fenster mit den Eingabeeigenschaften nach Ihrem Event Hub, und wählen Sie ihn aus. Wählen Sie anschließend in der Dropdownliste *Authentifizierungsmodus* die Option **Verwaltete Identität** aus.

1. Geben Sie die restlichen Eigenschaften an, und klicken Sie auf **Speichern**.

### <a name="add-the-event-hub-as-an-output"></a>Hinzufügen des Event Hubs als Ausgabe

1. Wechseln Sie zum Stream Analytics-Auftrag, und navigieren Sie unter **Auftragstopologie** zur Seite **Ausgaben**.

1. Wählen Sie **Hinzufügen > Event Hub** aus. Suchen Sie im Fenster mit den Ausgabeeigenschaften nach Ihrem Event Hub, und wählen Sie ihn aus. Wählen Sie anschließend in der Dropdownliste *Authentifizierungsmodus* die Option **Verwaltete Identität** aus.

1. Geben Sie die restlichen Eigenschaften an, und klicken Sie auf **Speichern**.

## <a name="next-steps"></a>Nächste Schritte

* [Event Hubs-Ausgabe für Azure Stream Analytics](event-hubs-output.md)
* [Streamen von Daten aus Event Hubs](stream-analytics-define-inputs.md#stream-data-from-event-hubs)