---
title: Verwalten von Daten mithilfe von Azure Cosmos DB-Explorer
description: Azure Cosmos DB-Explorer ist eine eigenständige webbasierte Schnittstelle, die Ihnen ermöglicht, in Azure Cosmos DB gespeicherte Daten anzuzeigen und zu verwalten.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/23/2020
ms.author: dech
ms.openlocfilehash: d4baa92fe4aa2ed402c394198684c4deec2bf9f1
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348601"
---
# <a name="work-with-data-using-azure-cosmos-db-explorer"></a>Arbeiten mit Daten unter Verwendung von Azure Cosmos DB-Explorer 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB-Explorer ist eine eigenständige webbasierte Schnittstelle, die Ihnen ermöglicht, in Azure Cosmos DB gespeicherte Daten anzuzeigen und zu verwalten. Azure Cosmos DB-Explorer entspricht der vorhandenen Registerkarte **Daten-Explorer**, die beim Erstellen eines Azure Cosmos DB-Kontos im Azure-Portal verfügbar ist. Die wichtigsten Vorteile von Azure Cosmos DB-Explorer im Vergleich zum vorhandenen Daten-Explorer:

* Sie verfügen über einen vollständigen Bildschirm, um Ihre Daten anzuzeigen und Abfragen, gespeicherte Prozeduren und Trigger auszuführen und deren Ergebnisse anzuzeigen.  

* Sie können temporären oder permanenten Lese- oder Lese-/Schreibzugriff für Ihr Datenbankkonto und seine Sammlungen anderen Benutzern erteilen, die keinen Zugriff auf das Azure-Portal oder das Abonnement haben.  

* Sie können die Ergebnisse der Abfrage für andere Benutzer freigeben, die keinen Zugriff auf das Azure-Portal oder das Abonnement haben.  

## <a name="access-azure-cosmos-db-explorer"></a>Zugreifen auf Azure Cosmos DB-Explorer

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an. 

2. Suchen und navigieren Sie über **Alle Ressourcen** zu Ihrem Azure Cosmos DB-Konto, klicken Sie auf „Schlüssel“, und kopieren Sie den Wert von **Primäre Verbindungszeichenfolge**.  

3. Wechseln Sie zu https://cosmos.azure.com/, fügen Sie die Verbindungszeichenfolge ein, und klicken Sie auf **Verbinden**. Durch Verwenden der Verbindungszeichenfolge können Sie ohne Zeitbeschränkung auf Azure Cosmos DB-Explorer zugreifen.  

   Wenn Sie anderen Benutzern temporären Zugriff auf Ihr Azure Cosmos DB-Konto erteilen möchten, können Sie dafür die Lese-/Schreibzugriff- und die Lesezugriff-URL verwenden. 

4. Öffnen Sie das Blatt **Daten-Explorer**, und klicken Sie auf **Vollbildmodus öffnen**. Über das Popupdialogfeld können Sie zwei Zugriffs-URLs anzeigen: **Lesen/Schreiben** und **Lesen**. Diese URLs ermöglichen Ihnen, Ihr Azure Cosmos DB-Konto temporär für andere Benutzer freizugeben. Der Zugriff auf das Konto läuft nach 24 Stunden ab. Danach können Sie mithilfe einer neuen Zugriffs-URL oder der Verbindungszeichenfolge eine neue Verbindung herstellen. 

   **Lesen/Schreiben**: Wenn Sie die URL für den Lese-/Schreibzugriff für andere Benutzer freigeben, können diese die Datenbanken, Sammlungen, Abfragen und andere Ressourcen anzeigen und ändern, die diesem speziellen Konto zugeordnet sind.

   **Lesen**: Wenn Sie die URL für den Lesezugriff für andere Benutzer freigeben, können diese die Datenbanken, Sammlungen, Abfragen und andere Ressourcen anzeigen, die diesem speziellen Konto zugeordnet sind. Wenn Sie beispielsweise die Ergebnisse einer Abfrage für Ihre Teamkollegen freigeben möchten, die keinen Zugriff auf das Azure-Portal oder Ihr Azure Cosmos DB-Konto haben, können Sie ihnen diese URL bereitstellen.

   Wählen Sie den Typ des Zugriffs, mit dem Sie das Konto öffnen möchten, und klicken Sie auf **Öffnen**. Nachdem Sie den Explorer geöffnet haben, können Sie genauso vorgehen, wie auf der Registerkarte „Daten-Explorer“ im Azure-Portal.

   :::image type="content" source="./media/data-explorer/open-data-explorer-with-access-url.png" alt-text="Öffnen von Azure Cosmos DB-Explorer":::

## <a name="known-issues"></a>Bekannte Probleme

Zurzeit wird die Option **Vollbildmodus öffnen**, über die Sie den temporären Lese-/Schreib- oder Lesezugriff freigeben, noch nicht für Azure Cosmos DB Gremlin- und Tabellen-API-Konten unterstützt. Sie können Ihre Gremlin- und Tabellen-API-Konten noch immer anzeigen, indem Sie die Verbindungszeichenfolge an Azure Cosmos DB Explorer übergeben. 

Derzeit wird das Anzeigen von Dokumenten, die eine UUID enthalten, in Data Explorer nicht unterstützt. Dies wirkt sich nicht auf das Laden von Sammlungen aus, sondern nur auf einzelne Dokumente oder Abfragen, die diese Dokumente enthalten. Um diese Dokumente anzuzeigen und zu verwalten, sollten Benutzer weiterhin das Tool verwenden, das ursprünglich zum Erstellen dieser Dokumente verwendet wurde.

Kunden, die HTTP 401-Fehler erhalten, verfügen möglicherweise nicht über ausreichende Azure RBAC-Berechtigungen für das Azure-Konto des Kunden. Dies trifft insbesondere dann zu, wenn das Konto über eine benutzerdefinierte Rolle verfügt. Alle benutzerdefinierten Rollen müssen über die Aktion `Microsoft.DocumentDB/databaseAccounts/listKeys/*` verfügen, um Data Explorer verwenden zu können, wenn die Anmeldung mit Azure Active Directory-Anmeldeinformationen erfolgt.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die ersten Schritte der Datenverwaltung in Azure Cosmos DB-Explorer erlernt haben, fahren Sie wie folgt fort:

* Definieren Sie zuerst [Abfragen](./sql-query-getting-started.md) mithilfe von SQL-Syntax, und führen Sie [serverseitige Programmierung](stored-procedures-triggers-udfs.md) mit gespeicherten Prozeduren, UDFs und Triggern durch.
