---
title: Bericht über riskante IP-Adressen für Azure AD Connect Health mit AD FS | Microsoft-Dokumentation
description: Beschreibt den Azure AD Connect Health für AD FS Bericht über riskante IP-Adressen.
services: active-directory
documentationcenter: ''
ms.reviewer: zhiweiwangmsft
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2019
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57d74272d77183baa2284265aee298967f641250
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504881"
---
# <a name="risky-ip-report-public-preview"></a>Bericht über riskante IP-Adressen (öffentliche Vorschauversion)
AD FS-Kunden können Endpunkte für die Kennwortauthentifizierung für den Zugriff über das Internet verfügbar machen, um Authentifizierungsdienste für Endbenutzer bereitzustellen, damit diese auf SaaS-Anwendungen wie Microsoft 365 zugreifen können. In diesem Fall ist es möglich, dass ein böswilliger Benutzer versucht, sich an Ihrem AD FS-System anzumelden, um das Kennwort eines Endbenutzers zu erraten und Zugriff auf Anwendungsressourcen zu erhalten. AD FS verfügt seit der Einbindung in Windows Server 2012 R2 über die Funktion zum Sperren von Extranet-Konten, um diese Arten von Angriffen zu verhindern. Falls Sie eine frühere Version verwenden, empfehlen wir Ihnen dringend, Ihr AD FS-System auf Windows Server 2016 zu aktualisieren. <br />

Außerdem ist es möglich, dass von einer einzelnen IP-Adresse versucht wird, mehrere Anmeldungen für mehrere Benutzer durchzuführen. In diesen Fällen kann es sein, dass die Anzahl von Versuchen pro Benutzer unter dem Schwellenwert für den Schutz durch Kontosperrung in AD FS liegt. Azure AD Connect Health enthält jetzt einen „Bericht über riskante IP-Adressen“, mit dem dieser Zustand erkannt wird und Administratoren benachrichtigt werden. Hier sind die wichtigsten Vorteile dieses Berichts angegeben: 
- Erkennung von IP-Adressen, für die ein Schwellenwert für fehlgeschlagene kennwortbasierte Anmeldungen überschritten wird
- Unterstützung von fehlgeschlagenen Anmeldungen aufgrund eines fehlerhaften Kennworts oder eines Extranet-Sperrzustands
- Sofortige E-Mail-Benachrichtigung als Warnung für Administratoren nach dem Eintreten mit anpassbaren E-Mail-Einstellungen
- Anpassbare Einstellungen für den Schwellenwert, die der Sicherheitsrichtlinie einer Organisation entsprechen
- Herunterladbare Berichte für Offlineanalyse und Integration in andere Systeme per Automatisierung

> [!NOTE]
> Für die Verwendung dieses Berichts muss die AD FS-Überwachung aktiviert sein. Weitere Informationen finden Sie unter [Aktivieren der Überwachung für AD FS](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs). <br />
> Zugriff auf die Vorschauversion erhalten nur globale Administratoren und [Sicherheitsleseberechtigte](../../role-based-access-control/built-in-roles.md#security-reader).  
>

> [!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Whitelist*, den Microsoft nicht länger verwendet. Sobald der Begriff aus der Software entfernt wurde, wird er auch aus diesem Artikel entfernt.

## <a name="what-is-in-the-report"></a>Inhalt des Berichts
Die IP-Adressen der Clients mit fehlgeschlagenen Anmeldeaktivitäten werden über Webanwendungsproxy-Server aggregiert. Jeder Eintrag im Bericht über riskante IP-Adressen enthält aggregierte Informationen zu fehlgeschlagenen AD FS-Anmeldeaktivitäten, für die der angegebene Schwellenwert überschritten wurde. Er enthält die folgenden Informationen: ![Screenshot: Bericht über riskante IP-Adressen mit hervorgehobenen Spaltenüberschriften](./media/how-to-connect-health-adfs/report4a.png)

| Berichtselement | BESCHREIBUNG |
| ------- | ----------- |
| Zeitstempel | Zeigt den Zeitstempel basierend auf der lokalen Zeit im Azure-Portal an, wenn das Erkennungszeitfenster beginnt.<br /> Alle täglichen Ereignisse werden um Mitternacht (UTC) generiert. <br />Für stündliche Ereignisse wird der Zeitstempel auf den Anfang der Stunde gerundet. Sie finden die Startzeit der ersten Aktivität unter „firstAuditTimestamp“ in der exportierten Datei. |
| Triggertyp | Zeigt den Typ des Erkennungszeitfensters an. Die Triggertypen der Aggregation sind „Pro Stunde“ und „Pro Tag“. Dies ist hilfreich beim Erkennen eines Brute-Force-Angriffs mit hoher Häufigkeit gegenüber einem langsamen Angriff, bei dem sich die Versuche auf den gesamten Tag verteilen. |
| IP-Adresse | Die einzelne riskante IP-Adresse, für die es entweder zu Anmeldeaktivitäten mit einem fehlerhaften Kennwort oder einer Extranetsperrung gekommen ist. Dies kann eine IPv4- oder eine IPv6-Adresse sein. |
| Fehleranzahl aufgrund eines falschen Kennworts | Die Anzahl von Fehlern der Art „Falsches Kennwort“, die für die IP-Adresse während des Erkennungszeitfensters aufgetreten sind. Fehler der Art „Falsches Kennwort“ können für bestimmte Benutzer mehrfach auftreten. Beachten Sie, dass hierin keine Fehlversuche aufgrund von abgelaufenen Kennwörtern enthalten sind. |
| Fehleranzahl aufgrund einer Extranetsperre | Die Anzahl von Fehlern der Art „Extranetsperre“, die für die IP-Adresse während des Erkennungszeitfensters aufgetreten sind. Die Fehler der Art „Extranetsperre“ können für bestimmte Benutzer mehrfach auftreten. Dies ist nur der Fall, wenn in AD FS (Versionen 2012 R2 oder höher) die Extranetsperre konfiguriert wurde. <b>Hinweis</b> Wir empfehlen Ihnen dringend, dieses Feature zu aktivieren, wenn Sie Extranetanmeldungen mit Kennwörtern zulassen. |
| Eindeutiger Benutzername bei Versuch | Die Anzahl von eindeutigen Benutzerkonten, mit denen über die IP-Adresse während des Erkennungszeitfensters Versuche unternommen wurden. Hiermit kann unterschieden werden, ob es sich um einen Angriff eines einzelnen Benutzers oder von mehreren Benutzern handelt.  |

Im Berichtseintrag unten ist beispielsweise angegeben, dass für die IP-Adresse <i>104.2XX.2XX.9</i> am 28.02.2018 in der Stunde zwischen 18 und 19 Uhr keine Fehler der Art „Falsches Kennwort“ und 284 Fehler der Art „Extranetsperre“ aufgetreten sind. Von diesen Vorgängen waren 14 eindeutige Benutzer betroffen. Für das Aktivitätsereignis wurde der angegebene Stundenschwellenwert im Bericht überschritten. 

![Screenshot: Beispiel für den Eintrag im Bericht über riskante IP-Adressen](./media/how-to-connect-health-adfs/report4b.png)

> [!NOTE]
> - In der Berichtsliste werden nur Aktivitäten angezeigt, für die der festgelegte Schwellenwert überschritten wird. 
> - Mit diesem Bericht ist die Zurückverfolgung für maximal 30 Tage möglich.
> - In diesem Warnbericht werden keine Exchange-IP-Adressen oder privaten IP-Adressen angezeigt. Sie sind weiterhin in der Exportliste enthalten. 
>

![Screenshot: Bericht über riskante IP-Adressen mit den hervorgehobenen Optionen „Herunterladen“, „Benachrichtigungseinstellungen“ und „Schwellenwerteinstellungen“](./media/how-to-connect-health-adfs/report4c.png)

## <a name="load-balancer-ip-addresses-in-the-list"></a>Load Balancer-IP-Adressen in der Liste
Bei Anmeldeaktivitäten der Load Balancer-Aggregation ist ein Fehler aufgetreten, und der Warnungsschwellenwert wurde erreicht. Falls IP-Adressen des Lastenausgleichs angezeigt werden, sendet Ihr externer Lastenausgleich höchstwahrscheinlich die Client-IP-Adresse nicht, wenn er die Anforderung an den Webanwendungsproxy-Server übergibt. Konfigurieren Sie Ihren Lastenausgleich so, dass Forward-Client-IP-Adressen ordnungsgemäß weitergegeben werden. 

## <a name="download-risky-ip-report"></a>Herunterladen des Berichts über riskante IP-Adressen 
Mit der Funktion zum **Herunterladen** kann der gesamte Bericht über riskante IP-Adressen der letzten 30 Tage aus dem Connect Health-Portal kopiert werden. Das Exportergebnis enthält alle fehlgeschlagenen AD FS-Anmeldeaktivitäten jedes Erkennungszeitfensters, damit Sie den Filtervorgang nach dem Export anpassen können. Zusätzlich zu den hervorgehobenen Aggregationen im Portal werden im Exportergebnis auch weitere Details zu fehlgeschlagenen Anmeldeaktivitäten pro IP-Adresse angezeigt:

|  Berichtselement  |  BESCHREIBUNG  | 
| ------- | ----------- | 
| firstAuditTimestamp | Zeigt den ersten Zeitstempel für den Zeitpunkt an, zu dem die fehlgeschlagenen Aktivitäten während des Erkennungszeitfensters begonnen haben.  | 
| lastAuditTimestamp | Zeigt den letzten Zeitstempel für den Zeitpunkt an, zu dem die fehlgeschlagenen Aktivitäten während des Erkennungszeitfensters geendet haben.  | 
| attemptCountThresholdIsExceeded | Dieses Flag wird angezeigt, wenn die aktuellen Aktivitäten den Schwellenwert für die Warnung überschreiten.  | 
| isWhitelistedIpAddress | Dieses Flag wird angezeigt, wenn die IP-Adresse für Warnungen und die Berichterstellung gefiltert wird. Private IP-Adressen (<i>10.x.x.x, 172.x.x.x und 192.168.x.x</i>) und Exchange-IP-Adressen werden gefiltert und als „True“ gekennzeichnet. Wenn Bereiche mit privaten IP-Adressen angezeigt werden, ist die Wahrscheinlichkeit hoch, dass Ihr externer Lastenausgleich die Client-IP-Adresse beim Übergeben der Anforderung an den Webanwendungsproxy-Server nicht sendet.  | 

## <a name="configure-notification-settings"></a>Konfigurieren von Benachrichtigungseinstellungen
Administratorkontakte des Berichts können über die **Benachrichtigungseinstellungen** aktualisiert werden. Standardmäßig ist die E-Mail-Benachrichtigung zur Warnung vor riskanten IP-Adressen nicht aktiviert. Sie können die Benachrichtigung aktivieren, indem Sie die Schaltfläche unter „E-Mail-Benachrichtigungen für den Bericht zu IP-Adressen erhalten, die den Schwellenwert für fehlerhafte Aktivitäten überschreiten“ aktivieren. Wie bei den generischen Einstellungen für Warnungsbenachrichtigungen in Connect Health auch, können Sie hier die angegebene Liste mit den Benachrichtigungsempfängern für den Bericht über riskante IP-Adressen anpassen. Sie können beim Vornehmen der Änderung auch alle globalen Administratoren benachrichtigen. 

## <a name="configure-threshold-settings"></a>Konfigurieren von Schwellenwerteinstellungen
Der Schwellenwert für Warnungen kann über die „Schwellenwerteinstellungen“ aktualisiert werden. Zu Beginn ist der Schwellenwert im System standardmäßig festgelegt. Die Standardwert werden weiter unten angegeben. Die Schwellenwerteinstellungen für den Bericht über riskante IP-Adressen enthalten vier Kategorien:

![Azure AD Connect Health-Portal](./media/how-to-connect-health-adfs/report4d.png)

| Schwellenwertelement | BESCHREIBUNG |
| --- | --- |
| (Benutzername/Kennwort ungültig + Extranetsperre)/Tag  | Schwellenwerteinstellung zum Melden der Aktivität und Auslösen der Warnungsbenachrichtigung, wenn die Anzahl von „Falsches Kennwort“ zusammen mit der Anzahl von Extranetsperren den Schwellenwert pro **Tag** überschreitet. Der Standardwert ist 100.|
| (Benutzername/Kennwort ungültig + Extranetsperre)/Stunde | Schwellenwerteinstellung zum Melden der Aktivität und Auslösen der Warnungsbenachrichtigung, wenn die Anzahl von „Falsches Kennwort“ zusammen mit der Anzahl von Extranetsperren den Schwellenwert pro **Stunde** überschreitet. Der Standardwert ist 50.|
| Extranetsperre/Tag | Schwellenwerteinstellung zum Melden der Aktivität und Auslösen der Warnungsbenachrichtigung, wenn die Anzahl von Extranetsperren den Schwellenwert pro **Tag** überschreitet. Der Standardwert ist 50.|
| Extranetsperre/Stunde| Schwellenwerteinstellung zum Melden der Aktivität und Auslösen der Warnungsbenachrichtigung, wenn die Anzahl von Extranetsperren den Schwellenwert pro **Stunde** überschreitet. Der Standardwert ist 25.|

> [!NOTE]
> - Die Änderung des Berichtsschwellenwerts wird eine Stunde nach der Einstellungsänderung angewendet. 
> - Vorhandene gemeldete Elemente sind von der Schwellenwertänderung nicht betroffen. 
> - Wir empfehlen Ihnen, die Anzahl von Ereignissen in Ihrer Umgebung zu analysieren und den Schwellenwert entsprechend anzupassen. 
>
>

## <a name="faq"></a>Häufig gestellte Fragen
**Warum werden im Bericht Bereiche mit privaten IP-Adressen angezeigt?**  <br />
Private IP-Adressen (<i>10.x.x.x, 172.x.x.x und 192.168.x.x</i>) und Exchange-IP-Adressen werden gefiltert und in der Liste der genehmigten IP-Adressen als „True“ gekennzeichnet. Wenn Bereiche mit privaten IP-Adressen angezeigt werden, ist die Wahrscheinlichkeit hoch, dass Ihr externer Lastenausgleich die Client-IP-Adresse beim Übergeben der Anforderung an den Webanwendungsproxy-Server nicht sendet.

**Warum enthält der Bericht IP-Adressen des Lastenausgleichs?**  <br />
Falls IP-Adressen des Lastenausgleichs angezeigt werden, sendet Ihr externer Lastenausgleich höchstwahrscheinlich die Client-IP-Adresse nicht, wenn er die Anforderung an den Webanwendungsproxy-Server übergibt. Konfigurieren Sie Ihren Lastenausgleich so, dass Forward-Client-IP-Adressen ordnungsgemäß weitergegeben werden. 

**Wie kann ich die IP-Adresse blockieren?**  <br />
Sie sollten identifizierte schädliche IP-Adressen der Firewall hinzufügen oder in Exchange blockieren.   <br />

**Warum werden in diesem Bericht keine Elemente angezeigt?** <br />
- Es liegen keine fehlgeschlagenen Anmeldeaktivitäten vor, für die die Schwellenwerteinstellungen überschritten werden.
- Stellen Sie sicher, dass in Ihrer AD FS-Serverliste keine Warnung vom Typ „Integritätsdienst ist nicht aktuell” aktiv ist.  Informieren Sie sich über das [Durchführen der Problembehandlung für diese Warnung](how-to-connect-health-data-freshness.md).
- Überwachungen sind in AD FS-Farmen nicht aktiviert.

**Warum habe ich keinen Zugriff auf den Bericht?**  <br />
Zugriff erhalten nur globale Administratoren und [Sicherheitsleseberechtigte](../../role-based-access-control/built-in-roles.md#security-reader). Wenden Sie sich an den globalen Administrator aus, um Zugriff zu erhalten.


## <a name="next-steps"></a>Nächste Schritte
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Installieren des Azure AD Connect Health-Agents](how-to-connect-health-agent-install.md)
