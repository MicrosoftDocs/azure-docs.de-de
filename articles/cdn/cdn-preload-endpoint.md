---
title: Vorabladen von Assets auf einen Azure CDN-Endpunkt | Microsoft Docs
description: Erfahren Sie, wie Sie zwischengespeicherten Inhalt für einen Azure Content Delivery Network-Endpunkt vorab laden. Diese Funktion ist für bestimmte Versionen des Produkts verfügbar.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/12/2018
ms.author: allensu
ms.openlocfilehash: 186ded90b504420a2f315d054551d97821cf8465
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385046"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Vorabladen von Assets auf einen Azure CDN-Endpunkt
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Assets werden standardmäßig nur zwischengespeichert, wenn sie angefordert werden. Da die Edgeserver den Inhalt noch nicht zwischengespeichert haben und die Anforderung an den Ursprungsserver weiterleiten müssen, kann die erste Anforderung aus einer Region länger als die nachfolgenden Anforderungen dauern. Laden Sie Ihre Assets vorab, um diese Wartezeit beim ersten Treffer zu vermeiden. Abgesehen von der Bereitstellung einer besseren Kundenerfahrung kann durch das Vorabladen zwischengespeicherter Inhalte der Netzwerkdatenverkehr auf dem Ursprungsserver reduziert werden.

> [!NOTE]
> Das Vorabladen von Assets ist nützlich für große Ereignisse oder Inhalte, die für zahlreiche Benutzer gleichzeitig zur Verfügung gestellt werden, wie z.B. bei Veröffentlichung eines neuen Films oder Softwareupdates.
> 
> 

In diesem Tutorial wird Schritt für Schritt erläutert, wie Sie zwischengespeicherten Inhalt auf alle Azure CDN-Edgeknoten vorab laden.

## <a name="to-pre-load-assets"></a>Vorabladen von Assets
1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zum CDN-Profil mit dem Endpunkt, auf den Sie Inhalt vorab laden möchten. Der Profilbereich wird geöffnet.
    
2. Klicken Sie in der Liste auf den Endpunkt. Der Endpunktbereich wird geöffnet.
3. Wählen Sie im CDN-Bereich „Endpunkt“ die Option **Laden** aus.
   
    ![CDN-Bereich „Endpunkt“](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    Der Bereich **Laden** wird geöffnet.
   
    ![Bereich „Laden“ für CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. Geben Sie als **Inhaltspfad** den vollständigen Pfad jedes Assets ein, das Sie laden möchten (z.B. `/pictures/kitten.png`).
   
   > [!TIP]
   > Nachdem Sie mit der Texteingabe begonnen haben, werden weitere **Inhaltspfad**-Textfelder angezeigt, damit Sie eine Liste mit mehreren Assets erstellen können. Zum Löschen von Assets aus der Liste wählen Sie die Schaltfläche mit den Auslassungspunkten (...) und dann **Löschen** aus.
   > 
   > Jeder Pfad muss eine relative URL sein, die den folgenden [regulären Ausdrücken](/dotnet/standard/base-types/regular-expression-language-quick-reference) entspricht:  
   > - Laden eines einzelnen Dateipfads: `^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Laden einer einzelnen Datei mit Abfragezeichenfolge: `^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Da jedes Asset einen eigenen Pfad benötigt, werden beim Vorabladen von Assets keine Platzhalter unterstützt.
   > 
   > 
   
    ![Schaltfläche „Laden“](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Wenn Sie mit der Eingabe von Inhaltspfaden fertig sind, wählen Sie **Laden**.
   

> [!NOTE]
> Die Ladeanforderungen sind pro Minute und CDN-Profil auf 10 begrenzt, und es können jeweils 50 Pfade gleichzeitig verarbeitet werden. Jeder Pfad hat eine Pfadlänge von maximal 1024 Zeichen.
> 
> 

## <a name="see-also"></a>Siehe auch
* [Löschen eines Azure CDN-Endpunkts](cdn-purge-endpoint.md)
* [Azure CDN-REST-API-Referenz: Vorabladen von Assets auf einen Azure CDN-Endpunkt](/rest/api/cdn/cdn/endpoints/loadcontent)
* [Azure CDN-REST-API-Referenz: Löschen von Inhalt von einem Endpunkt](/rest/api/cdn/cdn/endpoints/purgecontent)