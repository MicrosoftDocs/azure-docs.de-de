---
title: Konfigurieren von Datenbankeinstellungen in Azure API for FHIR
description: In diesem Artikel wird beschrieben, wie Sie Datenbankeinstellungen in Azure API for FHIR konfigurieren.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2019
ms.author: matjazl
ms.openlocfilehash: 3fe4118a8ecf4479732ba4073b342d3ec9f941b8
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398197"
---
# <a name="configure-database-settings"></a>Konfigurieren der Datenbankeinstellungen 

Azure API for FHIR verwendet eine Datenbank zum Speichern der Daten. Die Leistung der zugrunde liegenden Datenbank hängt von der Anzahl der Anforderungseinheiten (Request Unit, RU) ab, die während der Dienstbereitstellung oder nach der Dienstbereitstellung in den Datenbankeinstellungen ausgewählt wurde.

Azure API for FHIR wendet beim Festlegen der Leistung der zugrunde liegenden Datenbank das Konzept der Anforderungseinheiten von Cosmos DB an (siehe [Anforderungseinheiten in Azure Cosmos DB](../cosmos-db/request-units.md)). 

Durchsatz muss bereitgestellt werden, um zu gewährleisten, dass jederzeit genügend Systemressourcen für Ihre Datenbank zur Verfügung stehen. Die erforderliche Anzahl von RUs für Ihre Anwendung hängt von den ausgeführten Vorgängen ab. Vorgänge können von einfachen Lese- und Schreibvorgängen bis hin zu komplexeren Abfragen reichen. 

> [!NOTE]
> Da verschiedene Vorgänge eine unterschiedliche Anzahl von RUs beanspruchen, wird die tatsächliche Anzahl verwendeter RUs bei jedem API-Aufruf im Antwortheader zurückgegeben. Auf diese Weise können Sie ein Profil für die Anzahl der von Ihrer Anwendung genutzten RUs erstellen.

## <a name="update-throughput"></a>Aktualisieren des Durchsatzes

Wenn Sie diese Einstellung im Azure-Portal ändern möchten, navigieren Sie zu Ihrer Azure API for FHIR-Instanz, und öffnen Sie das Blatt „Datenbank“. Anschließend ändern Sie den bereitgestellten Durchsatz in den gewünschten Wert entsprechend Ihren Leistungsanforderungen. Sie können den Wert in maximal 10.000 RU/s ändern. Wenn Sie einen höheren Wert benötigen, wenden Sie sich an den Azure-Support.

Wenn der Datenbankdurchsatz größer als 10.000 RU/s ist oder die in der Datenbank gespeicherten Daten 50 GB übersteigen, muss Ihre Clientanwendung in der Lage sein, Fortsetzungstoken zu verarbeiten. In der Datenbank wird für jeden Durchsatzzuwachs von 10.000 RU/s, oder wenn die Menge der gespeicherten Daten mehr als 50 GB beträgt, eine neue Partition erstellt. Mehrere Partitionen erstellen eine Antwort über mehrere Seiten, in der die Paginierung mithilfe von Fortsetzungstoken implementiert wird.

> [!NOTE] 
> Ein höherer Wert bedeutet einen höheren Azure API for FHIR-Durchsatz und höhere Kosten für den Dienst.

![Konfigurieren von Cosmos DB](media/database/database-settings.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie die RUs für Azure API for FHIR aktualisieren. Informationen zum Konfigurieren kundenseitig verwalteter Schlüssel als Datenbankeinstellung:

>[!div class="nextstepaction"]
>[Konfigurieren von kundenseitig verwalteten Schlüsseln](customer-managed-key.md)

Oder Sie können eine vollständig verwaltete Azure API for FHIR-Instanz bereitstellen:
 
>[!div class="nextstepaction"]
>[Bereitstellen von Azure API for FHIR](fhir-paas-portal-quickstart.md)