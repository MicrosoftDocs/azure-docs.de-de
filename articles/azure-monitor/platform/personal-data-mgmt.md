---
title: Leitfaden für in Azure Log Analytics gespeicherte personenbezogene Daten | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie personenbezogene Daten verwalten, die in Azure Log Analytics gespeichert sind, und wie Sie diese Daten identifizieren und entfernen.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: magoedte
ms.openlocfilehash: 0cf5a80e3eedbe7efb8463162b5b3ed489ac08c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61087248"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Leitfaden für personenbezogene Daten, die in Log Analytics und Application Insights gespeichert sind

Log Analytics ist ein Datenspeicher, der wahrscheinlich auch personenbezogene Daten enthält. Application Insights speichert die Daten auf einer Log Analytics-Partition. In diesem Artikel erfahren Sie, wo sich solche Daten üblicherweise in Log Analytics und Application Insights befinden und welche Möglichkeiten Sie im Zusammenhang mit diesen Daten haben.

> [!NOTE]
> Im Rahmen dieses Artikels bezieht sich _Protokolldaten_ auf Daten, die von einem Log Analytics-Arbeitsbereich gesendet werden, während _Anwendungsdaten_ Daten bezeichnet, die von Application Insights gesammelt werden.

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategie für den Umgang mit personenbezogenen Daten

Die Gestaltung der Strategie für den Umgang mit personenbezogenen Daten liegt letztendlich bei Ihnen und Ihrem Unternehmen. Im Anschluss finden Sie einige mögliche Herangehensweisen. Diese sind absteigend nach technischer Präferenz sortiert:

* Sorgen Sie nach Möglichkeit dafür, dass Daten, die als „personenbezogen“ betrachtet werden können, gar nicht erst gesammelt oder aber verschleiert, anonymisiert oder anderweitig verändert werden. Dies ist _mit Abstand_ die bevorzugte Vorgehensweise und erspart die Erstellung einer wirksamen und äußerst kostspieligen Datenverarbeitungsstrategie.
* Sollte diese Vorgehensweise nicht möglich sein, versuchen Sie, die Daten zu normalisieren, um die Auswirkungen auf die Datenplattform und auf die Leistung zu verringern. Statt beispielsweise eine explizite Benutzer-ID zu protokollieren, erstellen Sie Nachschlagedaten, die den Benutzernamen und die dazugehörigen Details mit einer internen ID korrelieren, die dann an anderer Stelle protokolliert werden kann. Wenn Sie daraufhin von einem Benutzer gebeten werden, seine personenbezogenen Informationen zu löschen, muss ggf. nur die entsprechende Zeile in der Nachschlagetabelle gelöscht werden. 
* Falls personenbezogene Daten gesammelt werden müssen, erstellen Sie einen Prozess basierend auf dem Bereinigungs-API-Pfad und dem vorhandenen Abfrage-API-Pfad, um allen Vorschriften zu genügen, die möglicherweise im Zusammenhang mit dem Exportieren und Löschen personenbezogener Daten eines Benutzers gelten. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Wo befinden sich personenbezogene Daten in Log Analytics?

Log Analytics ist ein flexibler Datenspeicher, der zwar ein Schema für die Daten vorgibt, jedoch für jedes Feld das Überschreiben mit benutzerdefinierten Werten zulässt. Darüber hinaus können beliebige benutzerdefinierte Schemas erfasst werden. Daher ist es unmöglich zu sagen, wo genau personenbezogene Daten in Ihrem jeweiligen Arbeitsbereich vorhanden sind. Bei den folgenden Speicherorten handelt es sich jedoch um gute Ausgangspunkte für die Suche in Ihrem Datenbestand:

### <a name="log-data"></a>Protokolldaten

* *IP-Adressen*: Log Analytics erfasst verschiedenste IP-Informationen für viele verschiedene Tabellen. Die folgende Abfrage zeigt beispielsweise alle Tabellen, in denen in den letzten 24 Stunden IPv4-Adressen erfasst wurden:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *Benutzer-IDs*: Benutzer-IDs befinden sich in verschiedensten Lösungen und Tabellen. Mit dem Suchbefehl können Sie Ihr gesamtes Dataset nach einem bestimmten Benutzernamen durchsuchen:
    ```
    search "[username goes here]"
    ```
  Wichtig: Suchen Sie nicht nur nach lesbaren Benutzernamen, sondern auch nach GUIDs, die direkt zu einem bestimmten Benutzer zurückverfolgt werden können.
* *Geräte-IDs*: Geräte-IDs sind mit Benutzer-IDs vergleichbar und werden gelegentlich als „privat“ betrachtet. Verwenden Sie die gleiche Vorgehensweise, die weiter oben für Benutzer-IDs angegeben ist, um Tabellen zu ermitteln, bei denen dies ggf. relevant ist. 
* *Benutzerdefinierte Daten*: In Log Analytics stehen verschiedene Erfassungsmethoden zur Verfügung: benutzerdefinierte Protokolle und benutzerdefinierte Felder, die [HTTP-Datensammler-API](../../azure-monitor/platform/data-collector-api.md) und benutzerdefinierte Daten, die im Rahmen von Systemereignisprotokollen erfasst werden. Alle diese Optionen können personenbezogene Daten enthalten und müssen auf entsprechende Daten untersucht werden.
* *Durch die Lösung erfasste Daten*: Da es sich bei dem Lösungsmechanismus um einen Mechanismus mit offenem Ende handelt, empfiehlt es sich, alle von Lösungen generierten Tabellen zu überprüfen, um die Compliance zu gewährleisten.

### <a name="application-data"></a>Anwendungsdaten

* *IP-Adressen*: Obwohl Application Insights standardmäßig alle IP-Adressfelder mit „0.0.0.0“ verschleiert, ist es durchaus üblich, diesen Wert mit der tatsächlichen Benutzer-IP zu überschreiben, um Sitzungsinformationen zu speichern. Mithilfe der folgenden Analytics-Abfrage kann nach allen Tabellen gesucht werden, die in den letzten 24 Stunden in der Spalte für die IP-Adresse andere Werte als „0.0.0.0“ enthalten haben:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *Benutzer-IDs*: Standardmäßig verwendet Application Insights zufällig generierte IDs für Benutzer und die Sitzungsnachverfolgung. Allerdings werden diese Felder häufig überschrieben, um eine ID zu speichern, die für die Anwendung größere Relevanz hat, z.B. Benutzernamen, AAD-GUIDs usw. Diese IDs werden häufig als personenbezogene Daten angesehen und sollten deshalb entsprechend gehandhabt werden. Wir empfehlen, diese IDs immer zu verschleiern oder zu anonymisieren. Felder, in denen diese Werte häufig vorkommen, sind beispielsweise „session_Id“, „user_Id“, „user_AuthenticatedId“, „user_AccountId“ sowie „customDimensions“.
* *Benutzerdefinierte Daten*: Application Insights ermöglicht es Ihnen, eine Gruppe benutzerdefinierter Dimensionen an einen beliebigen Datentyp anzufügen. Bei diesen Dimensionen kann es sich um *beliebige* Daten handeln. Ermitteln Sie mithilfe der folgenden Abfrage alle benutzerdefinierten Dimensionen, die in den letzten 24 Stunden gesammelt wurden:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Daten im Speicher und während der Übertragung*: Application Insights erfasst Ausnahmen, Anforderungen, Abhängigkeitsaufrufe und Ablaufverfolgungen. Personenbezogene Daten können häufig auf Code- und HTTP-Aufrufebene gesammelt werden. Überprüfen Sie die Tabellen für Ausnahmen, Anforderungen, Abhängigkeiten und Ablaufverfolgungen, um solche Daten zu ermitteln. Verwenden Sie nach Möglichkeit [Telemetrieinitialisierer](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling), um diese Daten zu verschleiern.
* *Aufzeichnungen des Momentaufnahmedebuggers*: Mithilfe des Features [Momentaufnahmedebugger](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) in Application Insights können Sie Debugmomentaufnahmen erfassen, sobald eine Ausnahme in der Produktionsinstanz Ihrer Anwendung abgefangen wird. Momentaufnahmen zeigen die gesamte Stapelüberwachung, die zu den Ausnahmen führt, sowie die Werte für lokale Variablen in jedem Schritt im Stapel. Leider ermöglicht dieses Feature weder das selektive Löschen von Fangpunkten noch den programmgesteuerten Zugriff auf Daten innerhalb der Momentaufnahme. Falls die Standardaufbewahrungsrate für Momentaufnahmen Ihren Compliancevorgaben nicht entspricht, wird empfohlen, das Feature zu deaktivieren.

## <a name="how-to-export-and-delete-private-data"></a>Exportieren und Löschen personenbezogener Daten

Wie bereits weiter oben im Abschnitt [Strategie für den Umgang mit personenbezogenen Daten](#strategy-for-personal-data-handling) erwähnt, wird __dringend__ empfohlen, die Datensammlungsrichtlinien nach Möglichkeit so zu strukturieren, dass personenbezogene Daten gar nicht erst gesammelt oder aber verschleiert, anonymisiert oder anderweitig verändert werden, sodass sie nicht mehr als „personenbezogen“ anzusehen sind. Der Umgang mit den Daten ist in erster Linie mit Kosten für Sie und Ihr Team verbunden: Sie müssen eine Strategie ausarbeiten und automatisieren sowie eine Schnittstelle entwickeln, über die Ihre Kunden mit ihren Daten interagieren können. Außerdem fallen laufende Wartungskosten an. Darüber hinaus bedeutet dies einen hohen Rechenaufwand für Log Analytics und Application Insights, und eine hohe Anzahl gleichzeitiger Abfrage- oder Bereinigungs-API-Aufrufe kann sich negativ auf alle anderen Interaktionen mit Log Analytics-Funktionen auswirken. Allerdings gibt es tatsächlich einige Szenarien, in denen personenbezogene Daten erfasst werden müssen. In diesen Fällen sollten Sie die Daten wie in diesem Abschnitt beschrieben handhaben.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Anzeigen und Exportieren

Sowohl für das Anzeigen als auch für das Exportieren von Datenanforderungen sollte die [Log Analytics-Abfrage-API](https://dev.loganalytics.io/) oder die [Application Insights-Abfrage-API](https://dev.applicationinsights.io/quickstart) verwendet werden. Die Logik, mit der die Daten in eine geeignete Form konvertiert werden, um sie für Ihre Benutzer bereitzustellen, muss von Ihnen selbst implementiert werden. [Azure Functions](https://azure.microsoft.com/services/functions/) eignet sich hervorragend zum Hosten einer solchen Logik.

> [!IMPORTANT]
>  Obwohl die Mehrzahl der Bereinigungsvorgänge wesentlich schneller als die SLA ausgeführt werden kann, ist – aufgrund der starken Auswirkung auf die verwendete Datenplattform – **die formelle SLA für die Ausführung von Bereinigungsvorgängen auf 30 Tage festgelegt**. Dies ist ein automatisierter Prozess; es gibt keine Möglichkeit anzufordern, dass ein Vorgang schneller verarbeitet werden soll.

### <a name="delete"></a>Löschen

> [!WARNING]
> Löschvorgänge in Log Analytics sind destruktiv und unumkehrbar. Seien Sie bei der Ausführung äußerst vorsichtig.

Im Rahmen der Vorgehensweisen für den Datenschutz haben wir einen API-Pfad für die *Bereinigung* verfügbar gemacht. Dieser Pfad sollte aufgrund der Risiken im Zusammenhang mit seiner Verwendung, möglichen Leistungseinbußen sowie einer möglichen Verzerrung von Aggregationen, Messungen und anderen Aspekten Ihrer Log Analytics-Daten möglichst selten verwendet werden. Alternative Methoden für den Umgang mit personenbezogenen Daten finden Sie im Abschnitt [Strategie für den Umgang mit personenbezogenen Daten](#strategy-for-personal-data-handling).

Das Bereinigen ist ein Vorgang, der hohe Berechtigungen erfordert, und weder eine App noch ein Benutzer in Azure (einschließlich des Ressourcenbesitzers) ist zur Ausführung berechtigt, sofern nicht in Azure Resource Manager explizit eine entsprechende Rolle zugewiesen wurde. Dies ist die Rolle _Datenpurger_, die aufgrund des möglichen Datenverlusts sehr zurückhaltend vergeben werden sollte. 

Nachdem die Azure Resource Manager-Rolle zugewiesen wurde, sind zwei neue API-Pfade verfügbar: 

#### <a name="log-data"></a>Protokolldaten

* [POST purge](https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge): Verwendet ein Objekt, das Parameter der zu löschenden Daten angibt, und gibt eine Verweis-GUID zurück. 
* GET purge status: Der Aufruf von „POST purge“ gibt einen Header vom Typ „x-ms-status-location“ zurück, der eine URL enthält, die Sie zum Ermitteln des Status Ihrer Bereinigungs-API aufrufen können. Beispiel:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperatonalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  Die Mehrzahl der Bereinigungsvorgänge wird wahrscheinlich deutlich schneller ausgeführt als in unserer SLA angegeben. Aufgrund der starken Auswirkungen auf die von Log Analytics verwendete Datenplattform **wurde die formelle SLA für die Ausführung von Bereinigungsvorgängen aber auf 30 Tage festgelegt**. 

#### <a name="application-data"></a>Anwendungsdaten

* [POST purge](https://docs.microsoft.com/rest/api/application-insights/components/purge): Verwendet ein Objekt, das Parameter der zu löschenden Daten angibt, und gibt eine Verweis-GUID zurück.
* GET purge status: Der Aufruf von „POST purge“ gibt einen Header vom Typ „x-ms-status-location“ zurück, der eine URL enthält, die Sie zum Ermitteln des Status Ihrer Bereinigungs-API aufrufen können. Beispiel:

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Obwohl die Mehrzahl der Bereinigungsvorgänge wesentlich schneller als die SLA ausgeführt werden kann, ist aufgrund der starken Auswirkung auf die von Application Insights verwendete Datenplattform **die formelle SLA für die Ausführung von Bereinigungsvorgängen auf 30 Tage festgelegt**.

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zum Sammeln, Verarbeiten und Schützen von Daten in Log Analytics finden Sie unter [Log Analytics – Datensicherheit](../../azure-monitor/platform/data-security.md).
- Weitere Informationen zum Sammeln, Verarbeiten und Sichern von Daten in Application Insights finden Sie unter [Application Insights – Datensicherheit](../../azure-monitor/app/data-retention-privacy.md).
