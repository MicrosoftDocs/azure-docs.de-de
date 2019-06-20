---
title: Verknüpfen von Azure ATP-Daten mit der Vorschauversion von Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure ATP-Daten mit Azure Sentinel verknüpfen.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5bf3cc44-ecda-4c78-8a63-31ab42f43605
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: f0d86a62c59df5bebd34137d0903fcaa7014573d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65204274"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Verknüpfen von Daten aus Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> Azure Sentinel ist derzeit als öffentliche Vorschauversion (Public Preview) verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Sie können Protokolle aus [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) mit einem einzigen Klick nach Azure Sentinel streamen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Benutzer mit globalen Administrator- oder Sicherheitsadministratorberechtigungen.
- Sie müssen ein Private Vorschau-Kunde von Azure ATP sein.

## <a name="connect-to-azure-atp"></a>Herstellen einer Verbindung mit Azure ATP

Stellen Sie sicher, dass die private Vorschauversion von Azure ATP [in Ihrem Netzwerk aktiviert](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1) ist.
Wenn Azure ATP bereitgestellt ist und Daten erfasst, können die verdächtigen Warnungen problemlos an Azure Sentinel gestreamt werden. Es kann bis zu 24 Stunden dauern, bis mit dem Streamen der Warnungen an Azure Sentinel begonnen wird.



1. Klicken Sie in Azure Sentinel auf **Data connectors** (Datenconnectors) und anschließend auf die Kachel **Azure ATP**.

2. Klicken Sie auf **Verbinden**.

6. Um das relevante Schema für die Azure ATP-Warnungen in Log Analytics zu verwenden, suchen Sie nach **SecurityAlert**.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie Azure Advanced Threat Protection mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats.md).

