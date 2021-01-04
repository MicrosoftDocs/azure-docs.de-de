---
title: Steuern des Azure CDN-Zwischenspeicherverhaltens mit Chacheregeln | Microsoft-Dokumentation
description: Anhand von CDN-Cacheregeln können Sie das Standardverhalten bei Cacheablauf sowohl global als auch mit Bedingungen (z.B. URL-Pfad und Dateierweiterungen) festlegen oder ändern.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/19/2019
ms.author: allensu
ms.openlocfilehash: a5f4f6a6e72b57638688069111071a6e0a035c49
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018663"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Steuern des Azure CDN-Zwischenspeicherverhaltens mit Chacheregeln

> [!NOTE] 
> Cacheregeln sind nur für die Profile **Azure CDN Standard von Verizon** und **Azure CDN Standard von Akamai** verfügbar. Für Profile von **Azure CDN von Microsoft** müssen Sie die [Standard-Regel-Engine](cdn-standard-rules-engine-reference.md) verwenden und für Profile von **Azure CDN Premium von Verizon** die [Verizon Premium-Regel-Engine](./cdn-verizon-premium-rules-engine.md) im **Verwaltungsportal**, um eine ähnliche Funktionalität nutzen zu können.
 
Azure Content Delivery Network (CDN) bietet zwei Möglichkeiten, um zu steuern, wie Ihre Dateien zwischengespeichert werden: 

- Cacheregeln: In diesem Artikel wird beschrieben, wie Sie anhand von CDN-Cacheregeln (Content Delivery Network) das Standardverhalten bei Cacheablauf sowohl global als auch mit benutzerdefinierten Bedingungen (z.B. URL-Pfad und Dateierweiterungen) festlegen oder ändern können. Azure CDN bietet zwei Arten von Cacheregeln:

   - Globale Cacheregeln: Sie können für jeden Endpunkt in Ihrem Profil eine globale Cacheregel festlegen, die sich auf alle Anforderungen an den Endpunkt auswirkt. Die globale Cacheregel überschreibt alle HTTP-Header mit Cacheanweisungen, sofern solche festgelegt wurden.

   - Benutzerdefinierte Cacheregeln: Sie können für jeden Endpunkt in Ihrem Profil eine oder mehrere benutzerdefinierte Cacheregeln festlegen. Benutzerdefinierte Cacheregeln, die mit bestimmten Pfaden und Dateierweiterungen übereinstimmen, werden nacheinander verarbeitet und überschreiben die globale Cacheregel, sofern eine solche festgelegt wurde. 

- Zwischenspeicherung von Abfragezeichenfolgen: Sie können festlegen, wie das Azure CDN die Zwischenspeicherung von Anforderungen mit Abfragezeichenfolgen behandelt. Weitere Informationen finden Sie unter [Steuern des Azure Content Delivery Network-Zwischenspeicherverhaltens mit Abfragezeichenfolgen](cdn-query-string.md). Wenn die Datei nicht zwischengespeichert werden kann, hat die Einstellung für die Zwischenspeicherung von Abfragezeichenfolgen basierend auf Cacheregeln und CDN-Standardverhalten keine Auswirkungen.

Informationen zum Standardverhalten beim Zwischenspeichern und die Header mit Cacheanweisungen finden Sie unter [Funktionsweise der Zwischenspeicherung](cdn-how-caching-works.md). 


## <a name="accessing-azure-cdn-caching-rules"></a>Zugreifen auf Azure CDN-Cacheregeln

1. Öffnen Sie das Azure-Portal, und wählen Sie ein CDN-Profil sowie einen Endpunkt aus.

2. Wählen Sie im linken Bereich unter „Einstellungen“ die Option **Cacheregeln** aus.

   ![Schaltfläche für CDN-Cacheregeln](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   Die Seite **Cacheregeln** wird geöffnet.

   ![Seite „CDN-Cacheregeln“](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>Einstellungen für das Verhalten beim Zwischenspeichern
Für globale und benutzerdefinierte Cacheregeln können Sie die folgenden Einstellungen für **Verhalten beim Zwischenspeichern** festlegen:

- **Cache umgehen**: Die vom Ursprung angegebenen Header mit Cacheanweisungen werden nicht zwischengespeichert und ignoriert.

- **Überschreiben**: Die vom Ursprung angegebenen Header mit Cacheanweisungen werden ignoriert, und stattdessen wird die angegebene Cachedauer verwendet. Dadurch wird „cache-control: no-cache“ nicht überschrieben.

- **Bei Fehlen festlegen**: Die vom Ursprung angegebenen Header mit Cacheanweisungen werden berücksichtigt, sofern diese vorhanden sind. Anderenfalls wird die angegebene Cachedauer verwendet.

![Globale Cacheregeln](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Benutzerdefinierte Cacheregeln](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Dauer bis Cacheablauf
Bei globalen und benutzerdefinierten Cacheregeln können Sie die Dauer bis zum Cacheablauf in Tagen, Stunden, Minuten und Sekunden angeben:

- Für die Optionen **Überschreiben** und **Bei Fehlen festlegen** in den Einstellungen **Verhalten beim Zwischenspeichern** liegt der gültige Bereich der Cachedauer zwischen 0 Sekunden und 366 Tagen. Bei einem Wert von 0 Sekunden speichert das CDN den Inhalt im Cache, muss jedoch jede Anforderung mit dem Ursprungsserver erneut überprüfen.

- Bei der Einstellung **Cache umgehen** wird die Cachedauer automatisch auf 0 Sekunden gesetzt und kann nicht geändert werden.

## <a name="custom-caching-rules-match-conditions"></a>Benutzerdefinierte Cacheregeln basierend auf Bedingungen

Für benutzerdefinierte Cacheregeln sind zwei Übereinstimmungsbedingungen verfügbar:
 
- **Pfad**: Diese Bedingung entspricht dem Pfad der URL, ausgenommen des Domänennamens, und unterstützt das Platzhaltersymbol (\*). Beispiel: _/myfile.html_, _/my/folder/*_ und _/my/images/*.jpg_. Die maximale Länge beträgt 260 Zeichen.

- **Erweiterung**: Diese Bedingung entspricht der Dateierweiterung der angeforderten Datei. Sie können eine Liste von durch Trennzeichen getrennten Dateierweiterungen angeben. Beispiel: _.jpg_, _.mp3_ oder _.png_. Die maximale Anzahl der Erweiterungen beträgt 50 und die maximale Anzahl der Zeichen pro Erweiterung 16. 

## <a name="global-and-custom-rule-processing-order"></a>Reihenfolge der Verarbeitung von globalen und benutzerdefinierten Regeln
Globale und benutzerdefinierte Cacheregeln werden in der folgenden Reihenfolge verarbeitet:

- Globale Cacheregeln haben gegenüber dem Standardverhalten beim Zwischenspeichern im CDN Vorrang (Einstellungen für HTTP-Header mit Cacheanweisungen). 

- Benutzerdefinierte Cacheregeln haben gegenüber globalen Cacheregeln Vorrang, sofern diese anwendbar sind. Benutzerdefinierte Cacheregeln werden in der Reihenfolge von oben nach unten verarbeitet. Dies bedeutet, wenn eine Anforderung beide Bedingungen erfüllt, haben Regeln ganz unten in der Liste Vorrang gegenüber Regeln am Anfang der Liste. Aus diesem Grund sollten Sie spezifischere Regeln weiter unten in der Liste platzieren.

**Beispiel:**
- Globale Cacheregel: 
   - Verhalten beim Zwischenspeichern: **Überschreiben**
   - Dauer bis Cacheablauf: 1 Tag

- Benutzerdefinierte Cacheregel 1:
   - Übereinstimmungsbedingung: **Pfad**
   - Übereinstimmungswert: _/home/*_
   - Verhalten beim Zwischenspeichern: **Überschreiben**
   - Dauer bis Cacheablauf: 2 Tage

- Benutzerdefinierte Cacheregel 2:
   - Übereinstimmungsbedingung: **Erweiterung**
   - Übereinstimmungswert: _.html_
   - Verhalten beim Zwischenspeichern: **Bei Fehlen festlegen**
   - Dauer bis Cacheablauf: 3 Tage

Wenn diese Regeln festgelegt sind, löst eine Anforderung für „ _&lt;Endpunkthostname&gt;_ .azureedge.net/home/index.html“ die benutzerdefinierte Cacheregel 2 aus, die auf **Bei Fehlen festlegen** und „3 Tage“ festgelegt ist. Enthält die Datei *index.html* daher den HTTP-Header `Cache-Control` oder `Expires`, werden sie berücksichtigt. Sind diese Header nicht festgelegt, wird die Datei drei Tage lang zwischengespeichert.

> [!NOTE] 
> Bei Dateien, die vor einer Regeländerung zwischengespeichert werden, wird die Cachedauereinstellung des Ursprungs beibehalten. Um die Cachedauer zurückzusetzen, müssen Sie [die Datei bereinigen](cdn-purge-endpoint.md). 
>
> Es dauert eine gewisse Zeit, bis Änderungen an der Azure CDN-Konfiguration im gesamten Netzwerk verteilt wurden: 
> - Bei **Azure CDN Standard von Akamai**-Profilen ist die Weitergabe in der Regel in einer Minute abgeschlossen. 
> - Bei Profilen vom Typ **Azure CDN Standard von Verizon** ist die Weitergabe in der Regel in zehn Minuten abgeschlossen.  
>

## <a name="see-also"></a>Weitere Informationen

- [Funktionsweise der Zwischenspeicherung](cdn-how-caching-works.md)
- [Tutorial: Festlegen der Azure CDN-Cacheregeln](cdn-caching-rules-tutorial.md)