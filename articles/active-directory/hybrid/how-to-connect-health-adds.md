---
title: Verwenden von Azure AD Connect Health mit AD DS | Microsoft-Dokumentation
description: Auf dieser Seite zu Azure AD Connect Health wird beschrieben, wie Sie AD DS überwachen.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 19e3cf15-f150-46a3-a10c-2990702cd700
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73f30f4f16ad879468a424d6e5cbe81e68b7c33d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60350648"
---
# <a name="using-azure-ad-connect-health-with-ad-ds"></a>Verwenden von Azure AD Connect Health mit AD DS
Die folgende Dokumentation gilt speziell für die Überwachung von Active Directory-Domänendiensten mit Azure AD Connect Health. Unterstützte Versionen von AD DS: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 und Windows Server 2016.

Weitere Informationen zum Überwachen von AD FS mit Azure AD Connect Health finden Sie unter [Verwenden von Azure AD Connect Health mit AD FS](how-to-connect-health-adfs.md). Informationen zum Überwachen von Azure AD Connect (Sync) mit Azure AD Connect Health finden Sie unter [Verwenden von Azure AD Connect Health für die Synchronisierung](how-to-connect-health-sync.md).

![Azure AD Connect Health für AD DS](./media/how-to-connect-health-adds/domainservicesnapshot.PNG)

## <a name="alerts-for-azure-ad-connect-health-for-ad-ds"></a>Warnungen für Azure AD Connect Health für AD DS
Der Abschnitt „Warnungen“ in Azure AD Connect Health für AD DS enthält eine Liste mit aktiven und behobenen Warnungen für ihre Domänencontroller. Wenn Sie eine aktive oder behobene Warnung auswählen, wird ein neues Blatt mit weiteren Informationen sowie mit Lösungsschritten und Links zur entsprechenden Dokumentation geöffnet. Jeder Warnungstyp kann über eine oder mehrere Instanzen verfügen, die jeweils den Domänencontrollern entsprechen, die von einer Warnung betroffen sind. Im unteren Bereich des Blatts mit den Warnungen können Sie auf einen betroffenen Domänencontroller doppelklicken, um ein zusätzliches Blatt mit weiteren Details zur entsprechenden Warnungsinstanz zu öffnen.

Auf diesem Blatt können Sie E-Mail-Benachrichtigungen für Warnungen aktivieren und den angezeigten Zeitraum ändern. Wenn Sie den Zeitraum vergrößern, werden auch bereits behobene Warnungen angezeigt.

![Azure AD Connect-Synchronisierungsfehler](./media/how-to-connect-health-adds/aadconnect-health-adds-alerts.png)

## <a name="domain-controllers-dashboard"></a>Dashboard „Domänencontroller“
Dieses Dashboard enthält eine topologische Ansicht Ihrer Umgebung mit wichtigen Betriebsmetriken und dem Integritätsstatus Ihrer überwachten Domänencontroller. Anhand der dargestellten Metriken können Sie schnell Domänencontroller identifizieren, die ggf. näher untersucht werden müssen. Standardmäßig wird nur ein Teil der Spalten angezeigt. Sie können jedoch alle verfügbaren Spalten anzeigen, indem Sie auf den Spaltenbefehl doppelklicken. Wählen Sie die für Sie relevanten Spalten aus, und machen Sie das Dashboard so zu einem zentralen Ort, an dem Sie die Integrität Ihrer AD DS-Umgebung komfortabel überwachen können.

![Domänencontroller](./media/how-to-connect-health-adds/aadconnect-health-adds-domainsandsites-dashboard.png)

Domänencontroller können nach der jeweiligen Domäne oder dem Standort gruppiert werden. Dadurch lässt sich die Umgebungstopologie leichter nachvollziehen. Durch einen Doppelklick auf die Blattüberschrift kann das Dashboard maximiert werden, um den gesamten verfügbaren Platz auf dem Bildschirm auszunutzen. Diese größere Ansicht ist hilfreich, wenn mehrere Spalten angezeigt werden.

## <a name="replication-status-dashboard"></a>Dashboard „Replikationsstatus“
Dieses Dashboard enthält eine Ansicht mit dem Replikationsstatus und der Replikationstopologie Ihrer überwachten Domänencontroller. Der Status des letzten Replikationsversuchs wird zusammen mit hilfreicher Dokumentation zu gefundenen Fehlern angezeigt. Durch einen Doppelklick auf einen fehlerhaften Domänencontroller können Sie ein neues Blatt öffnen, das unter anderem Fehlerdetails, empfohlene Lösungsschritte und Links zur Problembehandlungsdokumentation enthält.

![Replikationsstatus](./media/how-to-connect-health-adds/aadconnect-health-adds-replication.png)

## <a name="monitoring"></a>Überwachung
Dieses Feature bietet grafische Trends für verschiedene Leistungsindikatoren, die kontinuierlich für die überwachten Domänencontroller erfasst werden. Die Leistung eines Domänencontrollers kann leicht mit der Leistung aller anderen überwachten Domänencontroller in der Gesamtstruktur verglichen werden. Darüber hinaus können Sie verschiedene Leistungsindikatoren nebeneinander anzeigen, was bei der Behandlung von Problemen in Ihrer Umgebung hilfreich ist.

![Überwachung](./media/how-to-connect-health-adds/aadconnect-health-adds-monitoring.png)

Standardmäßig sind vorab vier Leistungsindikatoren ausgewählt. Sie können aber weitere Leistungsindikatoren hinzufügen, indem Sie auf den Befehl „Filter“ klicken und alle gewünschten Leistungsindikatoren aktivieren oder deaktivieren. Darüber hinaus können Sie auf ein Leistungsindikatordiagramm doppelklicken, um ein neues Blatt mit Datenpunkten für die einzelnen überwachten Domänencontroller zu öffnen.

## <a name="related-links"></a>Verwandte Links
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Installieren des Azure AD Connect Health-Agents](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health-Vorgänge](how-to-connect-health-operations.md)
* [Verwenden von Azure AD Connect Health mit AD FS](how-to-connect-health-adfs.md)
* [Verwenden von Azure AD Connect Health für die Synchronisierung](how-to-connect-health-sync.md)
* [Azure AD Connect Health – FAQ](reference-connect-health-faq.md)
* [Azure AD Connect Health: Versionsverlauf](reference-connect-health-version-history.md)

