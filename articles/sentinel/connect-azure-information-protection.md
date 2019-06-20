---
title: Verknüpfen von Azure Information Protection-Daten mit Azure Sentinel Preview | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Information Protection-Daten mit Azure Sentinel verknüpfen.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 4b73edd10521b23fb4befbe4fe7d9f0c7b496de3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65204300"
---
# <a name="connect-data-from-azure-information-protection"></a>Verknüpfen von Daten aus Azure Information Protection

> [!IMPORTANT]
> Azure Sentinel ist derzeit als öffentliche Vorschauversion (Public Preview) verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sie können Protokolle aus [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) mit einem einzigen Klick nach Azure Sentinel streamen. Azure Information Protection schützt Ihre Daten, unabhängig davon, ob sie in der Cloud oder in lokalen Infrastrukturen gespeichert sind, und kontrolliert und schützt E-Mails, Dokumente und sensible Daten, die Sie außerhalb Ihres Unternehmens teilen. Von der einfachen Klassifizierung bis hin zu eingebetteten Bezeichnungen und Berechtigungen: Sie können den Schutz der Daten jederzeit mit Azure Information Protection erweitern. Wenn Sie Azure Information Protection mit Azure Sentinel verbinden, streamen Sie alle Warnungen von Azure Information Protection an Azure Sentinel.


## <a name="prerequisites"></a>Voraussetzungen

- Benutzer mit Berechtigungen als globaler Administrator oder Sicherheitsadministrator oder mit Information Protection-Berechtigungen


## <a name="connect-to-azure-information-protection"></a>Herstellen einer Verbindung mit Azure Information Protection

Wenn Sie Azure Information Protection bereits haben, vergewissern Sie sich, dass es [in Ihrem Netzwerk aktiviert ist](https://docs.microsoft.com/azure/information-protection/activate-service).
Wenn Azure Information Protection bereitgestellt ist und Daten erhält, können die Warnungsdaten problemlos an Azure Sentinel gestreamt werden.


1. Wählen Sie in Azure Sentinel die Option **Data connectors** (Datenconnectors) aus, und klicken Sie dann auf die Kachel **Azure Information Protection**.

2. Navigieren Sie zum [Azure Information Protection-Portal](https://portal.azure.com/?ScannerConfiguration=true&EndpointDiscovery=true#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/quickstartBlade). 

3. Richten Sie unter **Verbindung** das Streamen von Protokollen von Azure Information Protection an Azure Sentinel ein, indem Sie auf [Analyse konfigurieren](https://portal.azure.com/#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/analyticsOnboardBlade) klicken.

4. Wählen Sie den Arbeitsbereich aus, in dem Sie Azure Sentinel bereitgestellt haben. 

5. Klicken Sie auf **OK**.

6. Um in der Protokollanalyse (Log Analytics) das relevante Schema für die Azure Information Protection-Warnungen zu verwenden, suchen Sie nach **InformationProtectionLogs_CL**.




## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie Azure Information Protection mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats.md).
