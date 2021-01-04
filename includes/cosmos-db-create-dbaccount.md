---
title: include file
description: include file
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 08/19/2020
ms.custom: include file
ms.openlocfilehash: efdd4a065e1eab55f5af420585a44754d42a43e9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010378"
---
1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus.

1. Suchen Sie auf der Seite **Neu** nach **Azure Cosmos DB**, und wählen Sie die Option aus.

1. Wählen Sie auf der Seite **Azure Cosmos DB** die Option **Erstellen** aus.

1. Geben Sie auf der Seite **Azure Cosmos DB-Konto erstellen** die grundlegenden Einstellungen für das neue Azure Cosmos-Konto ein. 

    |Einstellung|Wert|BESCHREIBUNG |
    |---|---|---|
    |Subscription|Abonnementname|Wählen Sie das Azure-Abonnement aus, das Sie für dieses Azure Cosmos-Konto verwenden möchten. |
    |Ressourcengruppe|Ressourcengruppenname|Wählen Sie eine Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, und geben Sie einen eindeutigen Namen für die Ressourcengruppe ein. |
    |Kontoname|Ein eindeutiger Name|Geben Sie einen Namen ein, der Ihr Azure Cosmos-Konto identifiziert. Da *documents.azure.com* an den Namen angefügt wird, die Sie für die URI-Erstellung angeben, muss der Name eindeutig sein.<br><br>Der Name darf nur Kleinbuchstaben, Zahlen und den Bindestrich (-) enthalten. Er muss 3 bis 44 Zeichen umfassen.|
    |API|Der Typ des zu erstellenden Kontos|Wählen Sie **Core (SQL)** aus, um eine Dokumentdatenbank und eine Abfrage mit SQL-Syntax zu erstellen. <br><br>Die API bestimmt den Typ des zu erstellenden Kontos. Azure Cosmos DB stellt fünf APIs bereit: Core (SQL) und MongoDB für Dokumentdaten, Gremlin für Diagrammdaten sowie Azure Table und Cassandra. Derzeit müssen Sie ein separates Konto für jede API erstellen. |
    |Kapazitätsmodus|„Bereitgestellter Durchsatz“ oder „Serverlos“|Wählen Sie **Bereitgestellter Durchsatz** aus, um ein Konto im Modus [Bereitgestellter Durchsatz](../articles/cosmos-db/set-throughput.md) zu erstellen. Wählen Sie **Serverlos** aus, um ein Konto im Modus [Serverlos](../articles/cosmos-db/serverless.md) zu erstellen.|
    |Tarifspezifischen Rabatt für den Free-Tarif anwenden|„Anwenden“ oder „Nicht anwenden“|Mit dem Azure Cosmos DB-Tarif „Free“ erhalten Sie die ersten 400 RUs/Sek. sowie 5 GB Speicher kostenlos in einem Konto. Weitere Informationen zum [Tarif „Free“](https://azure.microsoft.com/pricing/details/cosmos-db/)|
    |Standort|Die Region, die Ihren Benutzern am nächsten liegt|Wählen Sie einen geografischen Standort aus, an dem Ihr Azure Cosmos DB-Konto gehostet werden soll. Verwenden Sie den Standort, der Ihren Benutzern am nächsten ist, damit sie möglichst schnell auf die Daten zugreifen können.|
    |Kontotyp|„Produktionsbezogen“ oder „Nicht produktionsbezogen“|Wählen Sie **Produktionsbezogen** aus, wenn das Konto für eine Produktionsworkload verwendet wird. Wählen Sie **Nicht produktionsbezogen** aus, wenn das Konto nicht für die Produktion, sondern beispielsweise für Entwicklung, Tests, Qualitätssicherung oder Staging verwendet wird. Hierbei handelt es sich um eine Azure-Ressourcentageinstellung, die das Portal optimiert, aber keine Auswirkungen auf das zugrunde liegende Azure Cosmos DB-Konto hat. Sie können diesen Wert jederzeit ändern.|

    > [!NOTE]
    > Sie können pro Azure-Abonnement maximal ein Azure Cosmos DB-Konto im Free-Tarif einrichten und müssen sich beim Erstellen des Kontos anmelden. Wird die Option zum Anwenden des tarifspezifischen Rabatts für den Free-Tarif nicht angezeigt, bedeutet dies, dass bereits ein anderes Konto im Abonnement mit dem Free-Tarif aktiviert wurde.
   
    > [!NOTE]
    > Die folgenden Optionen sind nicht verfügbar, wenn Sie als **Kapazitätsmodus** die Option **Serverlos** auswählen:
    > - Tarifspezifischen Rabatt für den Free-Tarif anwenden
    > - Georedundanz
    > - Schreibvorgänge in mehreren Regionen
    
    :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-detail.png" alt-text="Die Seite „Neues Konto“ für Azure Cosmos DB":::

1. Klicken Sie auf **Überprüfen + erstellen**. Sie können die Abschnitte **Netzwerk** und **Tags** überspringen.

1. Überprüfen Sie die Kontoeinstellungen, und wählen Sie anschließend **Erstellen** aus. Die Erstellung des Kontos dauert einige Minuten. Warten Sie, bis auf der Portalseite **Ihre Bereitstellung wurde abgeschlossen.** angezeigt wird. 

    :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png" alt-text="Der Bereich „Benachrichtigungen“ im Azure-Portal":::

1. Wählen Sie **Zu Ressource wechseln** aus, um zur Seite des Azure Cosmos DB-Kontos zu wechseln. 

    :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png" alt-text="Seite des Azure Cosmos DB-Kontos":::
