---
title: Steuern des Azure CDN-Zwischenspeicherverhaltens mit Abfragezeichenfolgen – Standard-Tarif
description: Das Zwischenspeichern von Azure CDN-Abfragezeichenfolgen steuert, wie Dateien zwischengespeichert werden, wenn eine Webanforderung eine Abfragezeichenfolge enthält. In diesem Artikel wird das Zwischenspeichern von Abfragezeichenfolgen in Azure CDN-Standardprodukten beschrieben.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: 1521d08ef9d431bbe8b3fd3a578297d440ed56b3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018578"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Steuern des Azure CDN-Zwischenspeicherverhaltens mit Abfragezeichenfolgen – Standard-Tarif
> [!div class="op_single_selector"]
> * [Standard-Tarif](cdn-query-string.md)
> * [Premium-Tarif](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Übersicht
Mit Azure Content Delivery Network (CDN) können Sie steuern, wie Dateien für eine Webanforderung, die eine Abfragezeichenfolge enthält, zwischengespeichert werden. In einer Webanforderung mit einer Abfragezeichenfolge ist die Abfragezeichenfolge der Teil der Anforderung, der auf das Fragezeichen („?“) folgt. Eine Abfragezeichenfolge kann ein oder mehrere Schlüssel-Wert-Paare enthalten, wobei der Feldname und sein Wert durch ein Gleichheitszeichen („=“) getrennt sind. Die einzelnen Schlüssel-Wert-Paare sind durch ein kaufmännisches Und-Zeichen („&“) voneinander getrennt. Bespiel: http:\//www.contoso.com/content.mov?field1=value1&field2=value2. Falls mehrere Schlüssel-Wert-Paare in einer Abfragezeichenfolge derselben Anforderung vorhanden sind, spielt die Reihenfolge keine Rolle. 

> [!IMPORTANT]
> Die Azure CDN-Produkte „Standard“ und „Premium“ bieten die gleiche Funktionalität zum Zwischenspeichern von Abfragezeichenfolgen, jedoch mit einer anderen Benutzeroberfläche. Dieser Artikel beschreibt die Benutzeroberfläche für **Azure CDN Standard von Microsoft**, **Azure CDN Standard von Akamai** und **Azure CDN Standard von Verizon**. Informationen zum Zwischenspeichern von Abfragezeichenfolgen mit **Azure CDN Premium von Verizon** finden Sie unter [Steuern des Azure Content Delivery Network-Zwischenspeicherverhaltens mit Abfragezeichenfolgen – Premium](cdn-query-string-premium.md).

Es sind drei Abfragezeichenfolgenmodi verfügbar:

- **Abfragezeichenfolgen ignorieren**: Standardmodus. In diesem Modus übergibt der CDN-POP-Knoten (Point of Presence) die Abfragezeichenfolgen bei der ersten Anforderung vom Anforderer an den Ursprungsserver und speichert das Objekt im Cache zwischen. Für alle nachfolgenden Anforderungen des Objekts, die vom POP verarbeitet werden, werden die Abfragezeichenfolgen bis zum Ablauf des zwischengespeicherten Objekts ignoriert.

- **Zwischenspeicherung für Abfragezeichenfolgen umgehen**: In diesem Modus werden Anforderungen mit Abfragezeichenfolgen nicht auf dem CDN-POP-Knoten zwischengespeichert. Der POP-Knoten ruft das Objekt direkt vom Ursprungsserver ab und übergibt es bei jeder Anforderung an den Anforderer.

- **Jede eindeutige URL zwischenspeichern**: In diesem Modus wird jede Anforderung mit einer eindeutigen URL, einschließlich der Abfragezeichenfolge, als eindeutiges Objekt mit eigenem Cache behandelt. So wird beispielsweise die Antwort vom Ursprungsserver für eine Anforderung von „example.ashx?q=test1“ auf dem POP-Knoten zwischengespeichert und für nachfolgende Caches mit der gleichen Abfragezeichenfolge zurückgegeben. Eine Anforderung von „example.ashx?q=test2“ wird als separates Objekt mit eigener Einstellung für die Gültigkeitsdauer zwischengespeichert.
   
    >[!IMPORTANT] 
    > Verwenden Sie diesen Modus nicht, wenn die Abfragezeichenfolge Parameter enthält, die sich bei jeder Anforderung ändern, z.B. eine Sitzungs-ID oder einen Benutzernamen, da dies zu einer niedrigen Cachetrefferquote führt.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Ändern der Einstellungen für das Zwischenspeichern von Abfragezeichenfolgen für CDN-Standardprofile
1. Öffnen Sie ein CDN-Profil, und wählen Sie den zu verwaltenden CDN-Endpunkt aus.
   
   ![CDN-Profilendpunkte](./media/cdn-query-string/cdn-endpoints.png)
   
2. Klicken Sie im linken Bereich unter „Einstellungen“ auf **Cacheregeln**.
   
    ![Schaltfläche für CDN-Cacheregeln](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. Wählen Sie in der Liste **Verhalten für das Zwischenspeichern von Abfragezeichenfolgen** einen Abfragezeichenfolgenmodus, und klicken Sie auf **Speichern**.
   
   ![Zwischenspeicherungsoptionen für CDN-Abfragezeichenfolgen](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Da die Verteilung der Registrierung über das Azure CDN eine Weile dauern kann, sind Einstellungsänderungen für die Zwischenspeicherung von Zeichenfolgen unter Umständen nicht sofort sichtbar:
> - Bei Profilen vom Typ **Azure CDN Standard von Microsoft** ist die Weitergabe in der Regel in zehn Minuten abgeschlossen. 
> - Bei **Azure CDN Standard von Akamai**-Profilen ist die Weitergabe in der Regel in einer Minute abgeschlossen. 
> - Bei Profilen vom Typ **Azure CDN Standard von Verizon** und **Azure CDN Premium von Verizon** ist die Weitergabe in der Regel in zehn Minuten abgeschlossen. 



