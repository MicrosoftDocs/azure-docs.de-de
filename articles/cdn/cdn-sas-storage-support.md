---
title: Verwenden von Azure CDN mit SAS | Microsoft-Dokumentation
description: Azure CDN unterstützt die Verwendung von Shared Access Signature (SAS), um einen eingeschränkten Zugriff auf private Speichercontainer zu gewähren.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/21/2018
ms.author: allensu
ms.openlocfilehash: ff205069c31d50813a4fad71a3c9e2f8e2462844
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92778138"
---
# <a name="using-azure-cdn-with-sas"></a>Verwenden von Azure CDN mit SAS

Wenn Sie ein Speicherkonto für Azure CDN (Content Delivery Network) zum Zwischenspeichern von Inhalten einrichten, kann standardmäßig jeder Benutzer, der die URLs für Ihre Speichercontainer kennt, auf die hochgeladenen Dateien zugreifen. Um die Dateien in Ihrem Speicherkonto zu schützen, könnten Sie den Zugriff auf Ihre Speichercontainer von öffentlich auf privat ändern. Allerdings könnte in diesem Fall niemand auf Ihre Dateien zugreifen. 

Wenn Sie einen eingeschränkten Zugriff auf private Speichercontainer gewähren möchten, können Sie die Shared Access Signature (SAS)-Funktion Ihres Azure-Speicherkontos verwenden. Eine SAS ist ein URI, der eingeschränkte Zugriffsrechte auf Ihre Azure Storage-Ressourcen erteilt, ohne Ihren Kontoschlüssel verfügbar zu machen. Sie können eine SAS für Clients bereitstellen, denen Sie nicht Ihren Speicherkontoschlüssel anvertrauen möchten, aber denen Sie Zugriff auf bestimmte Speicherkontoressourcen gewähren möchten. Durch das Bereitstellen eines SAS-URI für diese Clients wird ihnen für einen bestimmten Zeitraum Zugriff auf eine Ressource gewährt.
 
Mit SAS können Sie verschiedene Parameter für den Zugriff auf ein Blob definieren, z.B. Start- und Ablaufzeiten, Berechtigungen (Lesen/Schreiben) und IP-Bereiche. In diesem Artikel wird beschrieben, wie Sie SAS in Kombination mit Azure CDN verwenden. Weitere Informationen zu SAS – einschließlich Erstellung und Parameteroptionen – finden Sie unter [Verwenden von Shared Access Signatures (SAS)](../storage/common/storage-sas-overview.md).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Einrichten von Azure CDN zur Verwendung mit Speicher-SAS
Für die Verwendung von SAS mit Azure CDN werden die folgenden drei Optionen empfohlen. Bei allen Optionen wird vorausgesetzt, dass Sie bereits eine funktionierende SAS erstellt haben (siehe Voraussetzungen). 
 
### <a name="prerequisites"></a>Voraussetzungen
Erstellen Sie zunächst ein Speicherkonto, und generieren Sie dann eine SAS für Ihr Asset. Sie können zwei Arten von SAS erstellen: eine Dienst-SAS oder eine Konto-SAS. Weitere Informationen finden Sie unter [Arten von Shared Access Signatures](../storage/common/storage-sas-overview.md#types-of-shared-access-signatures).

Nachdem Sie ein SAS-Token generiert haben, können Sie auf die BLOB-Speicherdatei zugreifen, indem Sie `?sv=<SAS token>` an die URL anfügen. Diese URL weist das folgende Format auf: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Beispiel:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Weitere Informationen zum Festlegen von Parametern finden Sie unter [Überlegungen zu SAS-Parametern](#sas-parameter-considerations) und [Shared Access Signature-Parameter](../storage/common/storage-sas-overview.md#how-a-shared-access-signature-works).

![CDN-SAS-Einstellungen](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Option 1: Verwenden von SAS mit Pass-Through zu Blob Storage aus dem Azure CDN

Diese Option ist die einfachste und verwendet nur ein einziges SAS-Token, das vom Azure CDN an den Ursprungsserver übergeben wird.
 
1. Wählen Sie einen Endpunkt aus, wählen Sie **Cacheregeln** aus, und wählen Sie dann in der Liste **Zwischenspeichern von Abfragezeichenfolgen** die Einstellung **Jede eindeutige URL zwischenspeichern** aus.

    ![CDN-Cacheregeln](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Nachdem Sie SAS für Ihr Speicherkonto eingerichtet haben, müssen Sie für den Zugriff auf die Datei das SAS-Token mit der CDN-Endpunkt-URL und der URL des Ursprungsservers verwenden. 
   
   Die resultierende CDN-Endpunkt-URL hat das folgende Format: `https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Beispiel:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Optimieren Sie die Dauer der Zwischenspeicherung entweder mithilfe von Cacheregeln oder durch das Hinzufügen von `Cache-Control`-Headern am Ursprungsserver. Da das Azure CDN das SAS-Token als einfache Abfragezeichenfolge betrachtet, sollten Sie als bewährte Methode eine Cachedauer festlegen, die mit oder vor dem SAS-Ablauf endet. Wenn eine Datei länger zwischengespeichert wird, als die SAS aktiv ist, ist die Datei vom Azure CDN-Ursprungsserver möglicherweise auch nach dem SAS-Ablauf weiter zugänglich. Wenn dieser Fall eintritt und Sie den Zugriff auf die Datei unterbinden möchten, müssen Sie einen Bereinigungsvorgang für die Datei durchführen, um sie aus dem Cache zu löschen. Informationen zum Festlegen der Aufbewahrungsdauer im Cache im Azure CDN finden Sie unter [Steuern des Verhaltens beim Zwischenspeichern im Azure CDN mit Cacheregeln](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Option 2: Ausgeblendetes CDN-SAS-Token mit Regel für das erneute Generieren
 
Diese Option ist nur für **Azure CDN Premium von Verizon** -Profile verfügbar. Mit dieser Option können Sie den Blobspeicher auf dem Ursprungsserver schützen. Diese Option kann nützlich sein, wenn Sie keine besonderen Zugriffsbeschränkungen für die Datei benötigen, aber Benutzer am direkten Zugriff auf den Speicherursprung hindern möchten, um die Azure CDN-Auslagerungszeiten zu verbessern. Das SAS-Token ist dem Benutzer nicht bekannt und für jeden erforderlich, der auf Dateien im angegebenen Container des Ursprungsservers zugreift. Aufgrund der URL-Rewriteregel wird das SAS-Token am CDN-Endpunkt jedoch nicht benötigt.
 
1. Verwenden Sie die [Regel-Engine](./cdn-verizon-premium-rules-engine.md), um eine URL-Rewriteregel zu erstellen. Das Verteilen von neuen Regeln kann bis zu 4 Stunden dauern.

   ![Schaltfläche zur CDN-Verwaltung](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![Schaltfläche für CDN-Regel-Engine](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   Im folgenden Beispiel für eine URL-Rewriteregel wird ein Muster für einen regulären Ausdruck mit einer Erfassungsgruppe und einem Endpunkt namens *sasstoragedemo* verwendet:
   
   Quelle:   
   `(container1\/.*)`


   Ziel:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN-URL-Rewriteregel – links](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![CDN-URL-Rewriteregel – rechts](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. Nach Aktivierung der neuen Regel können Benutzer auch ohne Verwendung des SAS-Tokens in der URL auf Dateien im angegebenen Container am CDN-Endpunkt zugreifen. Das Format sieht wie folgt aus: `https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Beispiel:   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. Optimieren Sie die Dauer der Zwischenspeicherung entweder mithilfe von Cacheregeln oder durch das Hinzufügen von `Cache-Control`-Headern am Ursprungsserver. Da das Azure CDN das SAS-Token als einfache Abfragezeichenfolge betrachtet, sollten Sie als bewährte Methode eine Cachedauer festlegen, die mit oder vor dem SAS-Ablauf endet. Wenn eine Datei länger zwischengespeichert wird, als die SAS aktiv ist, ist die Datei vom Azure CDN-Endpunkt möglicherweise auch nach dem SAS-Ablauf weiter zugänglich. Wenn dieser Fall eintritt und Sie den Zugriff auf die Datei unterbinden möchten, müssen Sie einen Bereinigungsvorgang für die Datei durchführen, um sie aus dem Cache zu löschen. Informationen zum Festlegen der Aufbewahrungsdauer im Cache im Azure CDN finden Sie unter [Steuern des Verhaltens beim Zwischenspeichern im Azure CDN mit Cacheregeln](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Option 3: Verwenden der CDN-Sicherheitstokenauthentifizierung mit einer Regel für das erneute Generieren

Um die Azure CDN-Sicherheitstokenauthentifizierung zu verwenden, müssen Sie über das Profil **Azure CDN Premium von Verizon** verfügen. Diese Option bietet die höchste Sicherheit und die meisten Anpassungsmöglichkeiten. Der Clientzugriff basiert auf den Sicherheitsparametern, die Sie für das CDN-Sicherheitstoken festlegen. Nachdem Sie das Sicherheitstoken erstellt und eingerichtet haben, ist es in allen CDN-Endpunkt-URLs erforderlich. Aufgrund der URL-Rewriteregel wird das SAS-Token am CDN-Endpunkt jedoch nicht benötigt. Falls das SAS-Token später ungültig wird, kann das Azure CDN den Inhalt vom Ursprungsserver nicht erneut validieren.

1. [Erstellen Sie ein Azure CDN-Sicherheitstoken](./cdn-token-auth.md#setting-up-token-authentication), und aktivieren Sie es mithilfe der Regel-Engine für den CDN-Endpunkt und -Pfad, über den Ihre Benutzer auf die Datei zugreifen können.

   Eine Endpunkt-URL mit Sicherheitstoken hat das folgende Format:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Beispiel:   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   Die Parameteroptionen für eine Sicherheitstokenauthentifizierung unterscheiden sich von den Parameteroptionen für ein SAS-Token. Wenn Sie beim Erstellen eines Sicherheitstokens eine Ablaufzeit festlegen, müssen Sie denselben Ablaufzeitpunkt verwenden wie für das SAS-Token. So ist sichergestellt, dass der Ablaufzeitpunkt vorhersehbar ist. 
 
2. Verwenden Sie die [Regel-Engine](./cdn-verizon-premium-rules-engine.md), um eine URL-Rewriteregel zu erstellen, mit welcher der Tokenzugriff auf alle Blobs im Container aktiviert wird. Das Verteilen von neuen Regeln kann bis zu 4 Stunden dauern.

   Im folgenden Beispiel für eine URL-Rewriteregel wird ein Muster für einen regulären Ausdruck mit einer Erfassungsgruppe und einem Endpunkt namens *sasstoragedemo* verwendet:
   
   Quelle:   
   `(container1\/.*)`
   
   Ziel:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN-URL-Rewriteregel – links](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![CDN-URL-Rewriteregel – rechts](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. Wenn Sie die SAS verlängern, müssen Sie die URL-Rewriteregel mit dem neuen SAS-Token aktualisieren. 

## <a name="sas-parameter-considerations"></a>Überlegungen zu SAS-Parametern

Da SAS-Parameter für das Azure CDN nicht sichtbar sind, kann das Azure CDN sein Übermittlungsverhalten nicht auf Grundlage der Parameter ändern. Die definierten Parametereinschränkungen gelten nur für Anforderungen, die das Azure CDN an den Ursprungsserver sendet, nicht für Anforderungen vom Client an das Azure CDN. Diese Unterscheidung muss beim Festlegen von SAS-Parametern berücksichtigt werden. Wenn diese erweiterten Funktionen benötigt werden und Sie [Option 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule) verwenden, legen Sie die geeigneten Einschränkungen für das Azure CDN-Sicherheitstoken fest.

| Name des SAS-Parameters | BESCHREIBUNG |
| --- | --- |
| Start | Zeitpunkt, ab dem das Azure CDN auf die Blobdatei zugreifen kann. Wählen Sie aufgrund von Uhrabweichungen (Signaleingang für verschiedene Komponenten zu unterschiedlichen Zeiten) einen 15 Minuten früheren Zeitpunkt, wenn Sie möchten, dass das Asset sofort verfügbar ist. |
| Ende | Der Zeitpunkt, ab dem das Azure CDN nicht mehr auf die Blobdatei zugreifen kann. Zuvor zwischengespeicherte Dateien im Azure CDN sind weiterhin zugänglich. Um die Dateiablaufzeit zu steuern, legen Sie entweder die geeignete Ablaufzeit im Azure CDN-Sicherheitstoken fest, oder bereinigen Sie das Asset. |
| Zulässige IP-Adressen | Optional. Wenn Sie **Azure CDN von Verizon** verwenden, können Sie diesen Parameter auf die Bereiche festlegen, die in [Edgeserver-IP-Adressen für Azure CDN von Verizon](./cdn-pop-list-api.md) definiert sind. Wenn Sie **Azure CDN von Akamai** verwenden, können Sie den Parameter für IP-Bereiche nicht festlegen, weil die IP-Adressen nicht statisch sind.|
| Zulässige Protokolle | Die zugelassenen Protokolle für eine Anforderung mit dem Konto-SAS. Als Einstellung wird HTTPS empfohlen.|

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur SAS finden Sie in den folgenden Artikeln:
- [Verwenden von Shared Access Signatures (SAS)](../storage/common/storage-sas-overview.md)
- [Shared Access Signatures, Teil 2: Erstellen und Verwenden einer SAS mit Blob Storage](../storage/common/storage-sas-overview.md)

Weitere Informationen zum Einrichten der Tokenauthentifizierung finden Sie unter [Schützen von Azure Content Delivery Network-Assets mit Tokenauthentifizierung](./cdn-token-auth.md).