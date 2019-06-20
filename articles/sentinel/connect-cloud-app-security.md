---
title: Verknüpfen von Cloud App Security-Daten mit Azure Sentinel Preview | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Cloud App Security-Daten mit Azure Sentinel verknüpfen.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cd9e5e27-fdd4-4717-8924-be4c1c430f23
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: a418bb318654752eaf48ffbdd05b80cabb487ece
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65207546"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Verknüpfen von Microsoft Cloud App Security-Daten 

> [!IMPORTANT]
> Azure Sentinel ist derzeit als öffentliche Vorschauversion (Public Preview) verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sie können Protokolle von [Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) mit einem einzigen Klick in Azure Sentinel streamen. Durch diese Verbindung könen Sie die Warnungen von Cloud App Security in Azure Sentinel streamen. 

## <a name="prerequisites"></a>Voraussetzungen

- Ein Benutzer mit globalen Administrator- oder Sicherheitsadministratorberechtigungen

## <a name="connect-to-cloud-app-security"></a>Herstellen einer Verbindung mit Cloud App Security

Wenn Sie Cloud App Security bereits haben, vergewissern Sie sich, dass es [in Ihrem Netzwerk aktiviert ist](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Wenn Cloud App Security bereitgestellt ist und Daten erhält, können die Warnungsdaten problemlos an Azure Sentinel gestreamt werden.


1. Wählen Sie in Azure Sentinel die Option **Data connectors** (Datenconnectors) aus, und klicken Sie dann auf die Kachel **Cloud App Security**.

2. Klicken Sie auf **Verbinden**.

3. Um das relevante Schema für die Cloud App Security-Warnungen in Log Analytics zu verwenden, suchen Sie nach **SecurityAlert**.


## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument wurde erläutert, wie Sie Microsoft Cloud App Security mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats.md).
