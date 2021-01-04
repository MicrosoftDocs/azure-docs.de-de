---
title: Importieren und Ausführen von Notebooks aus einem GitHub-Repository in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie eine Verbindung mit GitHub herstellen und die Notebooks aus einem GitHub-Repository in Ihr Azure Cosmos-Konto importieren. Nach dem Importieren können Sie sie ausführen, bearbeiten und die Änderungen wieder in GitHub speichern.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/19/2020
ms.openlocfilehash: 8315369d2100036a50aae770267aa04bceb2dfb0
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339800"
---
# <a name="import-notebooks-from-a-github-repo-into-azure-cosmos-db"></a>Importieren von Notebooks aus einem GitHub-Repository in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Nach dem [Aktivieren der Notebookunterstützung](enable-notebooks.md) für Ihre Azure Cosmos-Konten können Sie neue Notebooks erstellen, neue Notebooks von Ihrem lokalen Computer hochladen oder die vorhandenen Notebooks aus Ihren GitHub-Konten importieren. In diesem Artikel erfahren Sie, wie Sie Ihren Notebooks-Arbeitsbereich mit GitHub verbinden und die Notebooks aus einem GitHub-Repository in Ihr Azure Cosmos-Konto importieren. Nach dem Importieren können Sie sie ausführen, Änderungen vornehmen und die Änderungen wieder in GitHub speichern.

## <a name="get-notebooks-from-github"></a>Abrufen von Notebooks aus GitHub

Sie können eine Verbindung mit Ihren eigenen GitHub-Repositorys oder mit anderen öffentlichen GitHub-Repositorys herstellen, um Notebooks in Azure Cosmos DB zu lesen, zu erstellen und freizugeben. Führen Sie die folgenden Schritte aus, um eine Verbindung mit einem GitHub-Konto herzustellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu Ihrem Azure Cosmos-Konto.

1. Öffnen Sie die Registerkarte **Daten-Explorer**. Auf dieser Registerkarte werden alle vorhandenen Datenbanken, Container und Notebooks angezeigt.

1. Wählen Sie das Menüelement **Mit GitHub verbinden** aus.

1. Daraufhin wird eine Registerkarte geöffnet, auf der Sie auswählen können, ob Sie nur eine Verbindung mit **öffentlichen Repositorys** oder eine Verbindung mit **öffentlichen und privaten Repositorys** herstellen möchten.  Wählen Sie die erforderliche Option und anschließend **Authorize access** (Zugriff autorisieren) aus. Die Autorisierung ist erforderlich, um Azure Cosmos DB den Zugriff auf die Repositorys in Ihrem GitHub-Konto zu ermöglichen.

   :::image type="content" source="./media/import-github-notebooks/authorize-access-github.png" alt-text="Autorisieren des Zugriffs auf Ihre GitHub-Repositorys durch Azure Cosmos DB":::

1. Sie werden zur Webseite „github.com“ umgeleitet, wo Sie die Autorisierung bestätigen können. Wählen Sie die Schaltfläche **Authorize AzureCosmosDBNotebooks** („AzureCosmosDBNotebooks“ autorisieren) aus, und geben Sie das Kennwort Ihres GitHub-Kontos ein.

1. Nach erfolgreicher Autorisierung gelangen Sie wieder zurück zu Ihrem Azure Cosmos-Konto. Nun werden alle öffentlichen/privaten Repositorys aus Ihrem GitHub-Konto angezeigt. Sie können ein Repository aus der verfügbaren Liste auswählen oder eines direkt mithilfe der zugehörigen URL hinzufügen.

1. Nachdem Sie das erforderliche Repository ausgewählt haben, wird der Repositoryeintrag aus dem Abschnitt **Unpinned repos** (Nicht angeheftete Repositorys) in den Abschnitt **Pinned repos** (Angeheftete Repositorys) verschoben. Bei Bedarf können Sie auch einen bestimmten Branch dieses Repositorys auswählen, aus dem die Notebooks importiert werden sollen.

   :::image type="content" source="./media/import-github-notebooks/choose-repo-branch.png" alt-text="Auswählen eines Repositorys und eines Branchs":::

1. Wählen Sie **OK** aus, um den Importvorgang abzuschließen. Alle Notebooks, die im ausgewählten Branch Ihres Repositorys verfügbar sind, werden in Ihr Azure Cosmos-Konto importiert.

Nach erfolgter GitHub-Kontointegration ist die Liste der Repositorys und Notebooks in Ihrem Azure Cosmos-Konto nur für Sie sichtbar. Das gilt auch, wenn sich mehrere Benutzer bei dem Azure Cosmos DB-Konto anmelden und ihre eigenen Konten hinzufügen. Anders ausgedrückt: Mehrere Benutzer können das gleiche Azure Cosmos-Konto verwenden, um den Notebook-Arbeitsbereich mit GitHub zu verbinden. Den einzelnen Benutzern wird jedoch jeweils nur die Liste der von ihnen importierten Repositorys und Notebooks angezeigt. Von anderen importierte Notebooks sind für Sie nicht sichtbar.

Wenn Sie die Verbindung zwischen Ihrem GitHub-Konto und dem Notebook-Arbeitsbereich trennen möchten, öffnen Sie die Registerkarte **Daten-Explorer** , wählen Sie `…` neben **GitHub-Repositorys** aus, und wählen Sie anschließend **Disconnect from GitHub** (GitHub-Verbindung trennen) aus.

## <a name="edit-a-notebook-and-push-changes-to-github"></a>Bearbeiten eines Notebooks und Pushen der Änderungen an GitHub

Sie können ein vorhandenes Notebook bearbeiten oder dem Repository ein neues Notebook hinzufügen und die Änderungen wieder in GitHub speichern.

Wählen Sie nach der Bearbeitung eines vorhandenen Notebooks **Speichern** aus. Daraufhin wird ein Dialogfeld geöffnet, in dem Sie die Commitnachricht für die vorgenommenen Änderungen eingeben können. Wählen Sie **Committen** aus. Das Notebook in GitHub wird aktualisiert. Sie können die Aktualisierungen überprüfen, indem Sie sich bei Ihrem GitHub-Konto anmelden und den Commitverlauf anzeigen.

Im regulären GitHub-Flow werden die Änderungen nach dem Committen in der Regel in ein Remoterepository gepusht. In diesem Fall dient die Option „Committen“ jedoch dazu, Ihre Aktualisierungen in GitHub bereitzustellen, zu committen und zu pushen.

:::image type="content" source="./media/import-github-notebooks/commit-changes-github.png" alt-text="Bearbeiten von Notebooks und Committen von Änderungen in GitHub":::

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu den Vorteilen von Jupyter Notebook-Instanzen in Azure Cosmos DB finden Sie [hier](cosmosdb-jupyter-notebooks.md).

