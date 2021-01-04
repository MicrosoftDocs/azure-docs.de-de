---
title: Erstellen eines Azure Remote Rendering-Kontos
description: Beschreibt die Schritte zum Erstellen eines Azure Remote Rendering-Kontos.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: 8169b277dfae918e86ac493259325ff84d0b6a4e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998531"
---
# <a name="create-an-azure-remote-rendering-account"></a>Erstellen eines Azure Remote Rendering-Kontos

Dieses Kapitel führt Sie durch die Schritte zum Erstellen eines Kontos für den **Azure Remote Rendering**-Dienst. Zum Abschließen der Schnellstarts oder Tutorials ist ein gültiges Konto zwingend erforderlich.

## <a name="create-an-account"></a>Erstellen eines Kontos

Die folgenden Schritte sind erforderlich, um ein Konto für den Azure Remote Rendering-Dienst zu erstellen:

1. Wechseln Sie zur [Mixed Reality-Vorschauseite](https://aka.ms/MixedRealityPrivatePreview).
1. Klicken Sie auf die Schaltfläche „Ressource erstellen“.
1. Geben Sie im Suchfeld („Marketplace durchsuchen“) „Remote Rendering“ ein, und drücken Sie die EINGABETASTE.
1. Klicken Sie in der Ergebnisliste auf die Kachel „Remote Rendering“.
1. Klicken Sie im nächsten Bildschirm auf die Schaltfläche „Erstellen“. Zum Erstellen eines neuen Remote Rendering-Kontos wird ein Formular geöffnet:
    1. Legen Sie „Ressourcenname“ auf den Namen des Kontos fest.
    1. Aktualisieren Sie „Abonnement“, wenn erforderlich.
    1. Legen Sie „Ressourcengruppe“ auf eine Ressourcengruppe Ihrer Wahl fest.
    1. Wählen Sie eine Region aus der Dropdownliste „Standort“ aus, in der diese Ressource erstellt werden soll. Weitere Informationen finden Sie in den [Kontoregionen](create-an-account.md#account-regions) weiter unten.
1. Nachdem das Konto erstellt wurde, navigieren Sie zu diesem Konto, und gehen Sie dann folgendermaßen vor:
    1. Notieren Sie sich die Konto-ID auf der Registerkarte *Übersicht*.
    1. Notieren Sie sich auf der Registerkarte *Einstellungen > Zugriffsschlüssel* den „Primärschlüssel“. Dies ist der geheime Kontoschlüssel des Kontos.

### <a name="account-regions"></a>Kontoregionen
Der Standort, der bei der Kontoerstellung eines Kontos angegeben wird, bestimmt, welcher Region die Kontoressource zugeordnet wird. Dieser kann nach dem Erstellen nicht mehr geändert werden. Das Konto kann jedoch verwendet werden, um eine Verbindung mit einer Remote Rendering-Sitzung in einer beliebigen [unterstützten Region](./../reference/regions.md) herzustellen, unabhängig vom Standort des Kontos.

### <a name="retrieve-the-account-information"></a>Abrufen der Kontoinformationen

In den Beispielen und Tutorials müssen Sie die Konto-ID und einen Schlüssel angeben. Beispielsweise in der Datei **arrconfig.json**, die für die PowerShell-Beispielskripts verwendet wird:

```json
"accountSettings": {
    "arrAccountId": "<fill in the account ID from the Azure portal>",
    "arrAccountKey": "<fill in the account key from the Azure portal>",
    "region": "<select from available regions>"
},
```

Weitere Informationen zum Ausfüllen der Option *Region* finden Sie in der [Liste der verfügbaren Regionen](../reference/regions.md).

Die Werte für **`arrAccountId`** und **`arrAccountKey`** finden Sie im Portal, wie in den folgenden Schritten beschrieben:

* [Navigieren Sie zum Azure-Portal](https://www.portal.azure.com).
* Suchen Sie nach Ihrem **„Remote Rendering-Konto“** . Es sollte sich in der Liste **„Zuletzt verwendete Ressourcen“** befinden. Sie können auch oben in der Suchleiste nach dem Konto suchen. Vergewissern Sie sich in diesem Fall, dass das Abonnement, das Sie verwenden möchten, im Standardabonnementfilter (Filtersymbol neben der Suchleiste) ausgewählt ist:

![Abonnementfilter](./media/azure-subscription-filter.png)

Wenn Sie auf Ihr Konto klicken, gelangen Sie zu diesem Bildschirm, auf dem die **Konto-ID** direkt angezeigt wird:

![Azure-Konto-ID](./media/azure-account-id.png)

Wählen Sie im Bereich auf der linken Seite **Zugriffsschlüssel** für den Schlüssel aus. Auf der nächsten Seite wird ein primärer und ein sekundärer Schlüssel angezeigt:

![Azure-Zugriffsschlüssel](./media/azure-account-primary-key.png)

Der Wert für **`arrAccountKey`** kann ein primärer oder ein sekundärer Schlüssel sein.

## <a name="link-storage-accounts"></a>Verknüpfen von Speicherkonten

In diesem Abschnitt wird erläutert, wie Speicherkonten mit Ihrem Remote Rendering-Konto verknüpft werden. Wenn ein Speicherkonto verknüpft ist, ist es nicht erforderlich, jedes Mal einen SAS-URI zu generieren, wenn Sie mit den Daten in Ihrem Konto interagieren möchten, z. B. beim Laden eines Modells. Stattdessen können Sie die Namen der Speicherkonten direkt verwenden, wie im Abschnitt [Laden eines Modells](../concepts/models.md#loading-models) beschrieben wird.

Die Schritte in diesem Absatz müssen für jedes Speicherkonto ausgeführt werden, für das diese alternative Zugriffsmethode verwendet werden soll. Wenn Sie noch keine Speicherkonten erstellt haben, können Sie den entsprechenden Schritt unter [Schnellstart: Konvertieren eines Modells für das Rendern](../quickstarts/convert-model.md#storage-account-creation) nachvollziehen.

Es wird davon ausgegangen, dass Sie jetzt über ein Speicherkonto verfügen. Navigieren Sie im Portal zum Speicherkonto, und wechseln Sie zur Registerkarte **Zugriffssteuerung (IAM)** für dieses Speicherkonto:

![Speicherkonto (IAM)](./media/azure-storage-account.png)

 Stellen Sie sicher, dass Sie über Besitzerberechtigungen für dieses Speicherkonto verfügen, damit Sie Rollenzuweisungen hinzufügen können. Wenn Sie keinen Zugriff besitzen, ist die Option **Rollenzuweisung hinzufügen** deaktiviert.

 Sie müssen drei verschiedene Rollen hinzufügen, wie in den nächsten Schritten beschrieben. Wenn Sie nicht alle drei Zugriffsebenen bereitstellen, treten beim Zugriff auf das Speicherkonto Berechtigungsprobleme auf.

 Klicken Sie auf die Schaltfläche **Hinzufügen** auf der Kachel „Rollenzuweisung hinzufügen“, um die erste Rolle hinzuzufügen:

![Speicherkonto (IAM): Rollenzuweisung hinzufügen](./media/azure-add-role-assignment.png)

* Die erste zuzuweisende Rolle ist **Besitzer**, wie im Screenshot oben gezeigt.
* Wählen Sie **Remote Rendering-Konto** aus der Dropdownliste **Zugriff zuweisen zu** aus.
* Wählen Sie in den letzten Dropdownlisten Ihr Abonnement und das Remote Rendering-Konto aus.

> [!WARNING]
> Wenn Ihr Remote Rendering-Konto nicht aufgeführt ist, lesen Sie den [Abschnitt zur Problembehandlung](../resources/troubleshoot.md#cant-link-storage-account-to-arr-account).

Wiederholen Sie das Hinzufügen neuer Rollen zwei weitere Male für die jeweilige Auswahl aus der Dropdownliste **Rolle**:

* **Mitwirkender von Speicherkonto**
* **Mitwirkender an Speicherblobdaten**

Die anderen Dropdownlisten werden wie im ersten Schritt beschrieben ausgewählt.

Nachdem Sie alle drei Rollen hinzugefügt haben, besitzt Ihr Azure Remote Rendering-Konto Zugriff auf Ihr Speicherkonto unter Verwendung der systemseitig zugewiesenen verwalteten Dienstidentitäten.
> [!IMPORTANT]
> Azure-Rollenzuweisungen werden von Azure Storage zwischengespeichert. Daher entsteht unter Umständen eine Verzögerung von 30 Minuten zwischen dem Zeitpunkt der Zugriffsgewährung für das Remote Rendering-Konto und dem Zeitpunkt, zu dem es für den Zugriff auf das Speicherkonto verwendet werden kann. Detailinformationen finden Sie in der [Dokumentation zur rollenbasierten Zugriffssteuerung in Azure (Azure RBAC)](../../role-based-access-control/troubleshooting.md#role-assignment-changes-are-not-being-detected).

## <a name="next-steps"></a>Nächste Schritte

* [Authentifizierung](authentication.md)
* [Verwenden der Azure-Front-End-APIs für die Authentifizierung](frontend-apis.md)
* [PowerShell-Beispielskripts](../samples/powershell-example-scripts.md)