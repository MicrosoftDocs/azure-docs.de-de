---
title: Integrieren von Azure Active Directory-Protokollen in Splunk mit Azure Monitor | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Active Directory-Protokolle mit Azure Monitor in SumoLogic integrieren.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f30e19a351f7b25f995a85cfd566bcba091ac27
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65597896"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Gewusst wie: Integrieren von Azure Active Directory-Protokollen in Splunk mit Azure Monitor

In diesem Artikel erfahren Sie, wie Sie Azure Active Directory-Protokolle (Azure AD) in Splunk mit Azure Monitor integrieren. Zunächst leiten Sie die Protokolle an einen Azure Event Hub weiter und integrieren dann den Event Hub in Splunk.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um dieses Feature verwenden zu können:
* Ein Azure Event Hub, der Azure AD-Aktivitätsprotokolle enthält. Erfahren Sie, [wie Sie Aktivitätsprotokolle an einen Event Hub streamen](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Das Azure Monitor-Add-On für Splunk. [Laden Sie Ihre Splunk-Instanz herunter, und konfigurieren Sie sie](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="integrate-azure-active-directory-logs"></a>Integrieren von Azure Active Directory-Protokollen 

1. Öffnen Sie Ihre Splunk-Instanz, und wählen Sie **Data Summary** (Datenzusammenfassung) aus.

    ![Die Schaltfläche für die Datenzusammenfassung](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Wählen Sie die Registerkarte **Sourcetypes** (Quelltypen) und dann **amal: aadal:audit** aus.

    ![Die Registerkarte mit Quelltypen der Datenzusammenfassung](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Die Azure AD-Aktivitätsprotokolle werden wie in der folgenden Abbildung angezeigt:

    ![Aktivitätsprotokolle](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Falls Sie kein Add-On in Ihrer Splunk-Instanz installieren können, z.B. bei Verwendung eines Proxys oder bei Ausführung in Splunk Cloud, können Sie diese Ereignisse an die HTTP-Ereignissammlung von Splunk weiterleiten. Verwenden Sie dazu diese [Azure-Funktion](https://github.com/Microsoft/AzureFunctionforSplunkVS), die durch neue Nachrichten in Event Hub ausgelöst wird. 
>

## <a name="next-steps"></a>Nächste Schritte

* [Interpret audit logs schema in Azure Monitor](reference-azure-monitor-audit-log-schema.md) (Interpretieren des Überwachungsprotokollschemas in Azure Monitor)
* [Interpret sign-in logs schema in Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md) (Interpretieren des Anmeldeprotokollschemas in Azure Monitor)
* [Häufig gestellte Fragen und bekannte Probleme](concept-activity-logs-azure-monitor.md#frequently-asked-questions)