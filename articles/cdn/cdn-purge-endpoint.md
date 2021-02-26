---
title: Löschen eines Azure CDN-Endpunkts | Microsoft Docs
description: Erfahren Sie, wie Sie alle zwischengespeicherten Inhalte in einem Azure Content Delivery Network-Endpunkt bereinigen. Edgeknoten speichern Ressourcen zwischen, bis ihre Gültigkeitsdauer abläuft.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: sohamnchatterjee
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/17/2019
ms.author: allensu
ms.openlocfilehash: 4fe4b99f9635ff254f1a75e03f13d7e6ffcb3c49
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366516"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Löschen eines Azure CDN-Endpunkts
## <a name="overview"></a>Übersicht
Azure CDN-Edgeknoten speichern Assets zwischen, bis die Gültigkeitsdauer (Time-to-live, TTL) des Assets abläuft.  Wenn ein Client nach Ablauf der TTL des Assets das Asset aus dem Edgeknoten anfordert, ruft der Edgeknoten eine neue aktualisierte Kopie des Assets ab, um die Clientanforderung zu erfüllen und den Cache zu aktualisieren.

Die bewährte Methode, um sicherzustellen, dass Ihre Benutzer immer die neueste Kopie Ihrer Assets abrufen, besteht darin, Ihre Assets für jedes Update mit einer Version zu versehen und sie als neue URLs zu veröffentlichen.  CDN ruft sofort die neuen Assets für die nächsten Clientanforderungen ab.  Manchmal möchten Sie möglicherweise zwischengespeicherten Inhalt aus allen Edgeknoten löschen und sie zwingen, neue aktualisierte Assets abzurufen.  Als Gründe hierfür kommen z. B. Updates Ihrer Webanwendung oder schnelle Aktualisierung von Assets, die falsche Informationen enthalten, infrage.

> [!TIP]
> Beachten Sie, dass das Löschen nur den zwischengespeicherten Inhalt auf den CDN-Edgeservern entfernt.  Alle Downstreamcaches, z.B. Proxyserver und lokale Browsercaches, können weiterhin eine zwischengespeicherte Kopie der Datei enthalten.  Denken Sie daran, wenn Sie die Gültigkeitsdauer einer Datei festlegen.  Sie können erzwingen, dass ein Downstreamclient die neueste Version der Datei anfordert, indem Sie ihr bei jeder Aktualisierung einen eindeutigen Namen geben, oder indem Sie das [Zwischenspeichern von Zeichenfolgen](cdn-query-string.md) nutzen.  
> 
> 

Dieses Lernprogramm führt Sie durch das Löschen von Assets aus allen Edgeknoten eines Endpunkts.

## <a name="walkthrough"></a>Exemplarische Vorgehensweise
1. Navigieren Sie im [Azure-Portal](https://portal.azure.com)zu dem CDN-Profil mit dem Endpunkt, den Sie löschen möchten.
2. Klicken Sie auf dem CDN-Profilblatt auf die Schaltfläche zum Löschen.
   
    ![CDN-Profilblatt](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    Das Blatt „Löschen“ wird geöffnet.
   
    ![CDN-Löschblatt](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. Wählen Sie auf dem Blatt „Löschen“ die Dienstadresse, die Sie in der URL-Dropdownliste löschen möchten.
   
    ![Löschformular](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > Um das Blatt „Löschen“ aufzurufen, können Sie auch auf dem Blatt des CDN-Endpunkts auf die Schaltfläche **Löschen** klicken.  In diesem Fall ist im **URL** -Feld die Adresse des Diensts dieses Endpunkts vorgegeben.
   > 
   > 
4. Wählen Sie, welche Assets Sie aus dem Edgeknoten löschen möchten.  Wenn Sie alle Assets löschen möchten, klicken Sie auf das Kontrollkästchen **Alles löschen**.  Geben Sie andernfalls den vollständigen Pfad jedes Assets, das Sie löschen möchten, im Textfeld **Pfad** ein. Folgende Formate werden im Pfad unterstützt.
    1. **Einzelne URL löschen**: Löschen Sie einzelne Ressourcen, indem Sie die vollständige URL mit oder ohne Dateiendung angeben. Beispiele: `/pictures/strasbourg.png`; `/pictures/strasbourg`.
    2. **Mit Platzhalter löschen**: Das Sternchen (\*) kann als Platzhalterzeichen verwendet werden. Löschen Sie alle Ordner, Unterordner und Dateien unter einem Endpunkt, indem Sie `/*` im Pfad angeben, oder löschen Sie alle Unterordner und Dateien unter einem bestimmten Ordner, indem Sie den Ordner gefolgt von `/*` angeben. Beispiel: `/pictures/*`.  Beachten Sie, dass das Löschen mit Platzhalter derzeit nicht vom Azure-CDN von Akamai unterstützt wird. 
    3. **Stammdomäne löschen**: Löschen Sie den Stamm des Endpunkts, indem Sie „/“ im Pfad angeben.
   
   > [!TIP]
   > Zum Löschen müssen Pfade als relative URL angegeben werden, die dem folgenden [regulären Ausdruck](/dotnet/standard/base-types/regular-expression-language-quick-reference) entspricht. **Alles löschen** und das **Löschen mit Platzhalter** wird derzeit nicht vom **Azure-CDN von Akamai** unterstützt.
   > > Einzelne URL löschen `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Abfragezeichenfolge `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Mit Platzhalter löschen `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";` 
   > 
   > Nachdem Sie Text eingegeben haben, werden weitere **Pfad** -Textfelder angezeigt, damit Sie eine Liste mit mehreren Assets erstellen können.  Sie können Assets aus der Liste löschen, indem Sie auf die Schaltfläche mit den Auslassungspunkten (...) klicken.
   > 
5. Klicken Sie auf die Schaltfläche **Löschen** .
   
    ![Löschschaltfläche](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Die Bearbeitung von Löschanforderungen dauert mit **Azure CDN von Microsoft** ca. 10 Minuten, mit **Azure CDN von Verizon** (Standard oder Premium) etwa zwei Minuten und mit **Azure CDN von Akamai** ca. 10 Sekunden.  Die Anzahl gleichzeitiger Löschanforderungen auf Profilebene ist bei Azure CDN auf 100 begrenzt. 
> 
> 

## <a name="see-also"></a>Siehe auch
* [Vorabladen von Assets auf einen Azure CDN-Endpunkt](cdn-preload-endpoint.md)
* [Azure CDN-REST-API-Referenz – Löschen oder Vorabladen eines Endpunkts](/rest/api/cdn/cdn/endpoints)

