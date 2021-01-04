---
title: Anfordern einer Kontingenterhöhung für Azure Cosmos DB-Ressourcen
description: Erfahren Sie, wie Sie eine Kontingenterhöhung für Azure Cosmos DB-Ressourcen anfordern. Außerdem erfahren Sie, wie Sie ein Abonnement für den Zugriff auf eine Region aktivieren.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: e7ec71220b75647e789508c760e50957b3b497fa
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93090034"
---
# <a name="how-to-request-quota-increase-for-azure-cosmos-db-resources"></a>Anfordern einer Kontingenterhöhung für Azure Cosmos DB-Ressourcen
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Die Ressourcen in Azure Cosmos DB verfügen über [Standardkontingente und -limits](concepts-limits.md). In manchen Fällen erfordert Ihre Workload u. U. ein höheres Kontingent als das Standardkontingent. In diesem Fall müssen Sie beim Azure Cosmos DB-Team eine Erhöhung des Kontingents anfordern. In diesem Artikel wird erläutert, wie Sie eine Kontingenterhöhung für Azure Cosmos DB-Ressourcen anfordern. Außerdem erfahren Sie, wie Sie ein Abonnement für den Zugriff auf eine Region aktivieren.

## <a name="create-a-new-support-request"></a>Erstellen einer neuen Supportanfrage

Um eine Erhöhung des Kontingents anzufordern, müssen Sie eine neue Supportanfrage mit Details zu Ihrer Workload erstellen. Anschließend wird Ihre Anfrage vom Azure Cosmos DB-Team ausgewertet und genehmigt. Führen Sie die folgenden Schritte aus, um im Azure-Portal eine neue Supportanfrage zu erstellen:

1. Melden Sie sich beim Azure-Portal an.

1. Wählen Sie im Menü auf der linken Seite **Hilfe + Support** aus, und wählen Sie dann **Neue Supportanfrage** aus.

1. Geben Sie auf der Registerkarte **Grundlagen** die folgenden Informationen ein:

   * Wählen Sie unter **Problemtyp** die Option **Grenzwerte für Dienste und Abonnements (Kontingente)** aus.
   * Wählen Sie unter **Abonnement** das Abonnement aus, dessen Kontingent Sie erhöhen möchten.
   * Wählen Sie unter **Kontingenttyp** den Eintrag **Cosmos DB** aus.

   :::image type="content" source="./media/create-support-request-quota-increase/create-quota-increase-request.png" alt-text="Erstellen einer neuen Cosmos DB-Supportanfrage für eine Kontingenterhöhung":::

1. Geben Sie auf der Registerkarte **Details** die Details zu Ihrer Kontingentanforderung ein. Die auf dieser Registerkarte angegebenen Informationen werden verwendet, um Ihre Anfrage weiter zu bewerten und den Supporttechniker beim Beheben des Problems zu unterstützen.

1. Geben Sie die folgenden Details im Formular ein:

   * **Beschreibung** : Geben Sie eine kurze Beschreibung Ihrer Anfrage ein, z. B. zu Ihrer Workload und warum das Standardkontingent nicht ausreicht. Je nach dem Ressourcentyp, für den Sie eine Erhöhung des Kontingents anfordern, müssen Sie die folgenden Details im Feld **Beschreibung** angeben:

     **Regionsanfragen** : Wenn Sie beantragen, der Zulassungsliste eine Region hinzuzufügen, stellen Sie sicher, dass Sie die folgenden Werte angeben:

        * Regionsname
        * Abonnement-ID

     **Anfragen zum Durchsatzlimit** : Wenn Sie beantragen, das Kontingent für den Durchsatz zu erhöhen, stellen Sie sicher, dass Sie die folgenden Werte angeben:

        * Datenbankname
        * Abonnement-ID
        * Neues Durchsatzlimit

     **Anfragen zum Limit für Datenbankkonten** : Wenn Sie beantragen, das Kontingent für die Anzahl der Datenbankkonten in einem Abonnement zu erhöhen, stellen Sie sicher, dass Sie die folgenden Werte angeben:

       * Abonnement-ID
       * Neues Limit für Datenbankkonten

   * **Dateiupload** : Laden Sie die Diagnosedateien oder andere Dateien hoch, die Ihnen für die Supportanfrage relevant erscheinen. Weitere Informationen bzw. eine Anleitung zum Hochladen von Dateien finden Sie im Artikel des [Azure-Supports]( ../azure-portal/supportability/how-to-manage-azure-support-request.md#upload-files).

   * **Schweregrad** : Wählen Sie basierend auf den geschäftlichen Auswirkungen einen der verfügbaren Schweregrade aus.

   * **Bevorzugte Kontaktmethode** : Sie können wählen, ob der Kontakt entweder per **E-Mail** oder per **Telefon** hergestellt werden soll.

1. Geben Sie die restlichen Details im Formular an, z. B. Ihre Verfügbarkeit, die für den Support gewünschte Sprache, Kontaktinformationen, E-Mail-Adresse und Telefonnummer.

1. Klicken Sie auf **Weiter: Überprüfen + erstellen**. Überprüfen Sie die angegebenen Informationen, und wählen Sie **Erstellen** aus, um eine Supportanfrage zu erstellen.

Das Azure Cosmos DB-Supportteam wird Ihre Anfrage innerhalb von 24 Stunden auswerten und sich mit Ihnen in Verbindung setzen.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu den [Standardkontingenten des Azure Cosmos DB-Diensts](concepts-limits.md)
