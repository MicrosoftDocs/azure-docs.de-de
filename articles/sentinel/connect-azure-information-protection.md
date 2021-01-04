---
title: Verknüpfen von Azure Information Protection mit Azure Sentinel
description: Streamen Sie Protokollierungsinformationen aus Azure Information Protection in Azure Sentinel, indem Sie den Datenconnector „Azure Information Protection“ konfigurieren.
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 82ea47037902ce3a9449f71a9edf62cb80863d4b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655985"
---
# <a name="connect-data-from-azure-information-protection"></a>Verknüpfen von Daten aus Azure Information Protection

> [!IMPORTANT]
> Der Azure Information Protection-Datenconnector in Azure Sentinel ist derzeit als Public Preview verfügbar.
> Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sie können Protokollierungsinformationen aus [Azure Information Protection](https://azure.microsoft.com/services/information-protection/) in Azure Sentinel streamen, indem Sie den Datenconnector „Azure Information Protection“ konfigurieren. Azure Information Protection unterstützt Sie bei der Kontrolle und Absicherung Ihrer sensiblen Daten, und zwar unabhängig davon, ob sie in der Cloud oder lokal gespeichert sind.

Wenn die [zentrale Berichterstellung für Azure Information Protection](/azure/information-protection/reports-aip) bereits so konfiguriert ist, dass Protokollierungsinformationen von diesem Dienst im gleichen Log Analytics-Arbeitsbereich gespeichert werden, den Sie derzeit für Azure Sentinel ausgewählt haben, können Sie die Konfiguration dieses Datenconnectors überspringen. Die Protokollierungsinformationen aus Azure Information Protection stehen Azure Sentinel bereits zur Verfügung.

Wenn die Protokollierung von Informationen aus Azure Information Protection jedoch in einen anderen Log Analytics-Arbeitsbereich erfolgt als in denjenigen, den Sie derzeit für Azure Sentinel ausgewählt haben, führen Sie einen der folgenden Schritte aus:

- Ändern Sie den in Azure Sentinel ausgewählten Arbeitsbereich.

- Ändern Sie den Arbeitsbereich für Azure Information Protection, indem Sie diesen Datenconnector konfigurieren.
    
    Wenn Sie den Arbeitsbereich ändern, werden neue Berichtsdaten für Azure Information Protection jetzt in dem Arbeitsbereich gespeichert, den Sie für Azure Sentinel verwenden, und Verlaufsdaten stehen Azure Sentinel nicht zur Verfügung. Wenn der vorherige Arbeitsbereich für benutzerdefinierte Abfragen, Warnungen oder REST-APIs konfiguriert ist, müssen diese für den Azure Sentinel-Arbeitsbereich neu konfiguriert werden, falls Sie sie weiterhin für Azure Information Protection verwenden möchten. Für Clients und Dienste, die Azure Information Protection verwenden, ist keine Neukonfiguration erforderlich.

## <a name="prerequisites"></a>Voraussetzungen

- Eine der folgenden Azure AD-Administratorrollen für Ihren Mandanten: 
    - Azure Information Protection-Administrator
    - Sicherheitsadministrator
    - Complianceadministrator
    - Compliancedatenadministrator
    - Globaler Administrator
    
    > [!NOTE]
    > Die Rolle „Azure Information Protection-Administrator“ kann nicht verwendet werden, wenn sich Ihr Mandant auf der [Plattform für einheitliche Bezeichnungen](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform) befindet.
    
    Diese Administratorrollen werden nur für die Konfiguration des Azure Information Protection-Connectors benötigt und sind nicht erforderlich, wenn Azure Sentinel mit Azure Information Protection verknüpft ist.

- Lese- und Schreibberechtigungen für den Log Analytics-Arbeitsbereich, den Sie für Azure Sentinel und Azure Information Protection verwenden.

- Azure Information Protection wurde dem Azure-Portal hinzugefügt. Wenn Sie bei diesem Schritt Hilfe benötigen, siehe [Hinzufügen von Azure Information Protection zum Azure-Portal](/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal).

## <a name="connect-to-azure-information-protection"></a>Herstellen einer Verbindung mit Azure Information Protection

Befolgen Sie die folgenden Anweisungen, wenn Sie keinen Log Analytics-Arbeitsbereich für Azure Information Protection konfiguriert haben oder Sie den Arbeitsbereich ändern müssen, in dem die Protokollierungsinformationen von Azure Information Protection gespeichert sind.

1. Wählen Sie in Azure Sentinel die Option **Datenconnectors** > **Azure Information Protection (Vorschau)** aus.

2. Wählen Sie **Connectorseite öffnen** aus.

3. Wählen Sie unter **Konfiguration** die Option **Azure Information Protection-Protokolle verbinden** aus.

4. Wählen Sie auf dem Blatt **Analyse konfigurieren (Vorschau)** den Arbeitsbereich aus, den Sie derzeit für Azure Sentinel verwenden. Wenn Sie einen anderen Arbeitsbereich auswählen, stehen die Berichtsdaten von Azure Information Protection für Azure Sentinel nicht zur Verfügung.

5. Nachdem Sie einen Arbeitsbereich ausgewählt haben, wählen Sie **OK** aus. Der **Status** des Connectors wird in **Verbunden** geändert.

6. Die Berichtsdaten aus Azure Information Protection werden im ausgewählten Arbeitsbereich in der Tabelle **InformationProtectionLogs_CL** gespeichert. 
    
    Um das relevante Schema in Azure Monitor für diese Berichtsdaten zu verwenden, suchen Sie nach **InformationProtectionEvents**. Weitere Informationen zu diesen Ereignisfunktionen finden Sie in der Azure Information Protection-Dokumentation im Abschnitt [Benutzerfreundliche Schemareferenz für Ereignisfunktionen](/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions).

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie Azure Information Protection mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).