---
title: Webhooks zum Reagieren auf Registrierungsaktionen
description: Erfahren Sie, wie Sie Webhooks verwenden, um Ereignisse auszulösen, wenn in einem Ihrer Registrierungsrepositorys Push- oder Pull-Aktionen stattfinden.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 1db1098da81e6cf9ecb262c99f705b77af2efd26
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004482"
---
# <a name="using-azure-container-registry-webhooks"></a>Verwenden von Azure Container Registry-Webhooks

Eine Azure Container Registry speichert und verwaltet private Docker-Containerimages. Dies ähnelt der Art und Weise, in der Docker Hub öffentliche Docker-Images speichert. Sie können auch Repositorys für [Helm-Diagramme](container-registry-helm-repos.md) hosten (Vorschauversion), ein Paketformat zum Bereitstellen von Anwendungen in Kubernetes. Sie können Webhooks verwenden, um Ereignisse auszulösen, wenn in einem Ihrer Registrierungsrepositorys bestimmte Aktionen stattfinden. Webhooks können auf Ereignisse auf Ebene der Registrierung reagieren oder auf ein bestimmtes Repositorytag begrenzt werden. Mit einer [georeplizierten](container-registry-geo-replication.md) Registrierung können Sie jeden Webhook so konfigurieren, dass er auf Ereignisse auf einem bestimmten regionalen Replikat reagiert.

Ausführliche Informationen zu Webhookanforderungen finden Sie unter [Referenz zu Azure Container Registry-Webhookschemas](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Containerregistrierung: Erstellen Sie in Ihrem Azure-Abonnement eine Containerregistrierung. Verwenden Sie beispielsweise das [Azure-Portal](container-registry-get-started-portal.md) oder die [Azure CLI](container-registry-get-started-azure-cli.md). Die [Azure Container Registry-Dienstebenen](container-registry-skus.md) haben unterschiedliche Kontingente für Webhooks.
* Docker CLI: Installieren Sie die [Docker-Engine](https://docs.docker.com/engine/installation/), um Ihren lokalen Computer als Docker-Host einzurichten und auf die Befehle der Docker CLI zuzugreifen.

## <a name="create-webhook---azure-portal"></a>Erstellen von Webhooks – Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu der Containerregistrierung, für die Sie einen Webhook erstellen möchten.
1. Wählen Sie unter **Dienste** die Option **Webhooks** aus.
1. Wählen Sie in der Webhook-Symbolleiste die Option **Hinzufügen**.
1. Geben Sie folgende Informationen in das Formular *Webhook erstellen* ein:

| Wert | BESCHREIBUNG |
|---|---|
| Webhookname | Der Name, den Sie dem Webhook geben möchten. Er darf nur Buchstaben und Zahlen enthalten und muss 5 bis 50 Zeichen lang sein. |
| Standort | Geben Sie für eine [georeplizierte](container-registry-geo-replication.md) Registrierung die Azure-Region des Registrierungsreplikats an. 
| Dienst-URI | Der URI, an den der Webhook POST-Benachrichtigungen senden soll. |
| Benutzerdefinierte Header | Header, die Sie zusammen mit der POST-Anforderung übergeben möchten. Diese müssen im Format „Schlüssel: Wert“ vorliegen. |
| Triggeraktionen | Aktionen, die den Webhook auslösen. Zu den Aktionen gehören Pushvorgänge für Images, Löschen von Images, Pushvorgänge für Helm-Diagramme, Löschen von Helm-Diagrammen und Quarantäne für Images. Sie können eine oder mehrere Aktionen auswählen, um den Webhook auszulösen. |
| Status | Der Status des Webhooks nach der Erstellung. Der Webhook ist standardmäßig aktiviert. |
| `Scope` | Der Bereich, in dem der Webhook funktioniert. Wenn nichts anderes angegeben ist, gilt der Bereich für alle Ereignisse in der Registrierung. Der Bereich kann im Format „repository:tag“ für ein Repository oder ein Tag oder mit „respository:*“ für alle Tags in einem Repository angegeben werden. |

Beispielformular für Webhook:

![Screenshot: Benutzeroberfläche für die Erstellung von ACR-Webhooks im Azure-Portal](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Erstellen von Webhooks – Azure-Befehlszeilenschnittstelle (CLI)

Um einen Webhook über die Azure-Befehlszeilenschnittstelle zu erstellen, verwenden Sie den Befehl [az acr webhook create](/cli/azure/acr/webhook#az-acr-webhook-create). Mit dem folgenden Befehl wird ein Webhook für alle Löschereignisse von Images in der Registrierung *mycontainerregistry* erstellt:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Testen von Webhooks

### <a name="azure-portal"></a>Azure-Portal

Vor der Verwendung des Webhooks können Sie ihn mithilfe der Schaltfläche **Ping** testen. Der Pingbefehl sendet eine allgemeine POST-Anforderung an den angegebenen Endpunkt und protokolliert die Antwort. Mit dieser Pingfunktion können Sie überprüfen, ob Sie den Webhook richtig konfiguriert haben.

1. Wählen Sie den Webhook aus, den Sie testen möchten.
2. Wählen Sie in der oberen Symbolleiste **Ping**.
3. Überprüfen Sie die Antwort des Endpunkts in der Spalte **HTTP-STATUS**.

![ACR-Webhookerstellungsoberfläche im Azure-Portal](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Um einen ACR-Webhook über die Azure-Befehlszeilenschnittstelle zu testen, verwenden Sie den Befehl [az acr webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping).

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Zum Anzeigen der Ergebnisse verwenden Sie den Befehl [az acr webhook list-events](/cli/azure/acr/webhook).

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Löschen von Webhooks

### <a name="azure-portal"></a>Azure-Portal

Jeder Webhook kann gelöscht werden, indem Sie den Webhook und dann die Schaltfläche **Löschen** im Azure-Portal auswählen.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Nächste Schritte

### <a name="webhook-schema-reference"></a>Webhookschemareferenz

Ausführliche Informationen zum Format und zu den Eigenschaften der von Azure Container Registry ausgegebenen JSON-Ereignisnutzlasten finden Sie in der Webhookschemareferenz:

[Referenz zu Azure Container Registry-Webhookschemas](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Event Grid-Ereignisse

Neben den in diesem Artikel erörterten nativen Registrierungswebhookereignissen kann Azure Container Registry Ereignisse an Event Grid ausgeben:

[Schnellstart: Senden von Containerregistrierungsereignissen an Event Grid](container-registry-event-grid-quickstart.md)
