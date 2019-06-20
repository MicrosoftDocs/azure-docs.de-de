---
title: Streamen von Azure Active Directory-Protokollen in SumoLogic mit Azure Monitor | Microsoft-Dokumentation
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
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ccf9cd550b3848be841f193f4ab8a6094cf671d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60437853"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor"></a>Integrieren von Azure Active Directory-Protokollen in SumoLogic mit Azure Monitor

In diesem Artikel erfahren Sie, wie Sie Azure Active Directory-Protokolle (Azure AD) mithilfe von Azure Monitor in SumoLogic integrieren. Zunächst leiten Sie die Protokolle an einen Azure Event Hub weiter und integrieren dann den Event Hub in SumoLogic.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um dieses Feature verwenden zu können:
* Ein Azure Event Hub, der Azure AD-Aktivitätsprotokolle enthält. Erfahren Sie, [wie Sie Aktivitätsprotokolle an einen Event Hub streamen](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Ein SumoLogic-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Schritte zum Integrieren von Azure AD-Protokollen in SumoLogic 

1. Beginnen Sie mit dem [Streamen von Azure AD-Protokollen an einen Azure Event Hub](quickstart-azure-monitor-stream-logs-to-event-hub.md).
2. Konfigurieren Sie Ihre SumoLogic-Instanz für das [Sammeln von Protokollen für Azure Active Directory](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory).
3. [Installieren Sie die Azure AD-App für SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards), um die vorkonfigurierten Dashboards für die Echtzeitanalyse Ihrer Umgebung zu verwenden.

   ![Dashboard](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>Nächste Schritte

* [Interpret audit logs schema in Azure Monitor](reference-azure-monitor-audit-log-schema.md) (Interpretieren des Überwachungsprotokollschemas in Azure Monitor)
* [Interpret sign-in logs schema in Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md) (Interpretieren des Anmeldeprotokollschemas in Azure Monitor)
* [Häufig gestellte Fragen und bekannte Probleme](concept-activity-logs-azure-monitor.md#frequently-asked-questions)