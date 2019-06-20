---
title: Azure AD Connect Health – Warnung „Die Daten des Integritätsdiensts sind nicht aktuell“ | Microsoft Docs
description: In diesem Dokument wird beschrieben, warum die Warnung „Die Daten des Integritätsdiensts sind nicht aktuell“ angezeigt wird und welche Schritte zur Problembehandlung Sie ausführen können.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: zhiweiw
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ffd783ec41b1b0c4a11ee426648c1e36fbbbf75
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60349908"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Warnung „Die Daten des Integritätsdiensts sind nicht aktuell“

## <a name="overview"></a>Übersicht

Die Agents auf den lokalen Computern, die von Azure AD Connect Health überwacht werden, laden regelmäßig Daten in den Azure AD Connect Health-Dienst hoch. Wenn der Dienst von einem Agent keine Daten erhält, sind die im Portal angezeigten Informationen nicht mehr aktuell. Um auf dieses Problem aufmerksam zu machen, löst der Dienst die Benachrichtigung **Die Daten des Integritätsdiensts sind nicht aktuell.** aus. Diese Benachrichtigung wird generiert, wenn der Dienst in den letzten beiden Stunden keine vollständigen Daten empfangen hat.  

- Die Statusbenachrichtigung **Warnung** wird ausgelöst, wenn der Integritätsdienst in den letzten beiden Stunden nur **unvollständige** Datentypen vom Server erhalten hat. Bei der Statusbenachrichtigung „Warnung“ werden keine E-Mail-Benachrichtigungen an konfigurierte Empfänger ausgelöst. 
- Die Statusbenachrichtigung **Fehler** wird ausgelöst, wenn der Integritätsdienst in den letzten beiden Stunden keinerlei Datentypen vom Server erhalten hat. Bei der Statusbenachrichtigung „Fehler“ werden E-Mail-Benachrichtigungen an konfigurierte Empfänger ausgelöst.

Der Dienst ruft die Daten von Agents ab, die auf den lokalen Computern ausgeführt werden (abhängig vom Diensttyp). Die folgende Tabelle gibt Aufschluss darüber, welche Agents auf dem Computer ausgeführt werden, was ihre Aufgabe ist und welche Datentypen der Dienst generiert. In manchen Fällen sind mehrere Dienste in den Prozess involviert, von denen jeder als möglicher Verursacher in Frage kommt. 

## <a name="understanding-the-alert"></a>Grundlegendes zur Benachrichtigung

Auf dem Blatt **Benachrichtigungsdetails** sehen Sie, wann die Benachrichtigung ausgelöst und zuletzt erkannt wurde. Die Benachrichtigung wird durch einen alle zwei Stunden ausgeführten Hintergrundprozess generiert und neu ausgewertet. Im folgenden Beispiel wurde die erste Benachrichtigung am 10.3. um 9:59 Uhr ausgelöst. Bei der erneuten Auswertung am 12.3. um 10:00 Uhr war die Benachrichtigung immer noch vorhanden. Auf dem Blatt ist auch die Zeit angegeben, zu der der Integritätsdienst einen bestimmten Datentyp zuletzt empfangen hat. 
 
 ![Azure AD Connect Health: Benachrichtigungsdetails](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
In der folgenden Tabelle werden die Diensttypen den entsprechenden erforderlichen Datentypen zugeordnet:

| Dienstart | Agent (Name des Windows-Diensts) | Zweck | Generierter Datentyp  |
| --- | --- | --- | --- |  
| Azure AD Connect (Synchronisierung) | Azure AD Connect Health Sync Insights-Dienst | Sammeln AAD Connect-spezifischer Informationen (Connectors, Synchronisierungsregeln und Ähnliches) | - AadSyncService-SynchronizationRules <br />  - AadSyncService-Connectors <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync-Überwachungsdienst | Sammeln AAD Connect-spezifischer Leistungsindikatoren, ETW-Ablaufverfolgungen und Dateien | Leistungsindikator |
| AD DS | Azure AD Connect Health AD DS Insights Service | Ausführen synthetischer Tests sowie Sammeln von Topologieinformationen und Replikationsmetadaten |  - Adds-TopologyInfo-Json <br /> - Common-TestData-Json (erstellt die Testergebnisse)   | 
|  | Azure AD Connect Health AD DS Monitoring Service | Sammeln AD DS-spezifischer Leistungsindikatoren, ETW-Ablaufverfolgungen und Dateien | - Leistungsindikator  <br /> - Common-TestData-Json (lädt die Testergebnisse hoch)  |
| AD FS | Azure AD Connect Health AD FS Diagnostics Service | Ausführen synthetischer Tests | TestResult (erstellt die Testergebnisse) | 
| | Azure AD Connect Health AD FS Insights Service  | Sammeln von AD FS-Nutzungsmetriken | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring Service | Sammeln AD FS-spezifischer Leistungsindikatoren, ETW-Ablaufverfolgungen und Dateien | TestResult (lädt die Testergebnisse hoch) |

## <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung 

Dieser Abschnitt enthält die erforderlichen Schritte zum Diagnostizieren des Problems. Als Erstes müssen einige grundlegende Prüfungen ausgeführt werden, die für alle Diensttypen geeignet sind. Die folgende Tabelle enthält die spezifischen Schritte für den jeweiligen Dienst- und Datentyp. 

> [!IMPORTANT] 
> Diese Warnung folgt der [Datenaufbewahrungsrichtlinie](reference-connect-health-user-privacy.md#data-retention-policy) von Connect Health.

* Vergewissern Sie sich, dass jeweils die neueste Version des Agents installiert ist. Den Versionsverlauf finden Sie [hier](reference-connect-health-version-history.md). 
* Vergewissern Sie sich, dass die Agent-Dienste von Azure AD Connect Health auf dem Computer den Status **Wird ausgeführt** haben. Beispiel: Connect Health für AD FS sollte über drei Dienste verfügen.
  ![Überprüfen von Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Lesen Sie unbedingt den [Abschnitt „Anforderungen“](how-to-connect-health-agent-install.md#requirements), und stellen Sie sicher, dass Sie die Anforderungen erfüllen.
* Verwenden Sie das [Tool zum Testen der Konnektivität](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service), um Konnektivitätsprobleme zu ermitteln.
* Wenn Sie einen HTTP-Proxy haben, führen Sie die folgenden [Konfigurationsschritte](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy) aus. 


## <a name="next-steps"></a>Nächste Schritte
Sollte bei einem der obigen Schritte ein Problem festgestellt werden, beheben Sie es, und warten Sie, bis die Warnung aufgelöst wird. Der Hintergrundprozess für Warnungen wird alle zwei Stunden ausgeführt. Es kann also bis zu zwei Stunden dauern, bis die Warnung aufgelöst wurde. 

* [Azure AD Connect Health-Datenaufbewahrungsrichtlinie](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health – FAQ](reference-connect-health-faq.md)
