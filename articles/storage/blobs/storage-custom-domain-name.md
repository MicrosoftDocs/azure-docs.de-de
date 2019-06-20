---
title: Konfigurieren eines benutzerdefinierten Domänennamens für Ihr Azure-Speicherkonto | Microsoft-Dokumentation
description: Verwenden Sie das Azure-Portal, um dem Blob Storage- oder Webendpunkt in einem Azure-Speicherkonto Ihren eigenen kanonischen Namen (CNAME) zuzuordnen.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: normesta
ms.reviewer: seguler
ms.subservice: blobs
ms.openlocfilehash: 4f6776a5f15cf391f3a65aceb6e9e783d87a2078
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148932"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>Konfigurieren eines benutzerdefinierten Domänennamens für Ihr Azure-Speicherkonto

Sie können eine benutzerdefinierte Domäne für den Zugriff auf Blob-Daten in Ihrem Azure-Speicherkonto konfigurieren. Der Standardendpunkt für Azure Blob Storage lautet *\<Speicherkontoname>.blob.core.windows.net*. Sie können auch den Webendpunkt verwenden, der im Rahmen des Artikels [Hosten von statischen Websites in Azure Storage](storage-blob-static-website.md) generiert wurde. Wenn Sie dem Blob- oder Webendpunkt für Ihr Speicherkonto eine benutzerdefinierte Domäne und eine Unterdomäne wie etwa *www\.contoso.com* zuordnen, können Benutzer über diese Domäne auf Blobdaten in Ihrem Speicherkonto zugreifen.

> [!IMPORTANT]
> Azure Storage bietet noch keine native Unterstützung von HTTPS für benutzerdefinierte Domänen. Derzeit können Sie [Azure CDN zum Zugreifen auf Blobs mit benutzerdefinierten Domänen über HTTPS verwenden](storage-https-custom-domain-cdn.md).
> 
> 
> [!NOTE]
> Von Speicherkonten wird derzeit nur ein einzelner benutzerdefinierter Domänenname pro Konto unterstützt. Ein benutzerdefinierter Domänenname kann nicht dem Webdienstendpunkt und zugleich dem Blobdienstendpunkt zugeordnet werden.
> 
> [!NOTE]
> Die Zuordnung funktioniert nur für Unterdomänen (z.B. www\.contoso.com). Wenn Ihr Webendpunkt in der Stammdomäne (z.B. contoso.com) verfügbar sein soll, müssen Sie [das Azure CDN mit benutzerdefinierten Domänen](storage-https-custom-domain-cdn.md) verwenden.

Die folgende Tabelle enthält einige Beispiel-URLs für Blobdaten, die sich in einem Speicherkonto namens *mystorageaccount* befinden. Die für das Speicherkonto registrierte benutzerdefinierte Unterdomäne ist *www\.contoso.com*:

| Ressourcentyp | Standard-URL | Benutzerdefinierte Domänen-URL |
| --- | --- | --- |
| Speicherkonto | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| Blob |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| Stammcontainer | http://mystorageaccount.blob.core.windows.net/myblob oder http://mystorageaccount.blob.core.windows.net/ $root/myblob| http://www.contoso.com/myblob oder http://www.contoso.com/ $root/myblob |
| Web |  http://mystorageaccount.[zone].web.core.windows.net/$web/[indexdoc] oder http://mystorageaccount.[zone].web.core.windows.net/[indexdoc] oder http://mystorageaccount.[zone].web.core.windows.net/$web oder http://mystorageaccount.[zone].web.core.windows.net/ | http://www.contoso.com/ $web oder http://www.contoso.com/ oder http://www.contoso.com/ $web/[indexdoc] oder http://www.contoso.com/ [indexdoc] |

> [!NOTE]  
> Wie in den folgenden Abschnitten gezeigt gelten alle Beispiele für den Blobdienstendpunkt auch für den Webdienstendpunkt.

## <a name="direct-vs-intermediary-cname-mapping"></a>Direkte und zwischengeschaltete CNAME-Zuordnung

Es gibt zwei Methoden, um Ihre mit einer Unterdomäne als Präfix versehene benutzerdefinierte Domäne (z.B. www\.contoso.com) auf den Blobendpunkt für Ihr Speicherkonto zu verweisen: 
* Verwenden der direkten CNAME-Zuordnung
* Verwenden der Unterdomäne *asverify* als Zwischenstufe.

### <a name="direct-cname-mapping"></a>Direkte CNAME-Zuordnung

Die erste und einfachste Methode besteht darin, einen CNAME-Eintrag (kanonischer Name) zu erstellen, in dem Ihre benutzerdefinierte Domäne und Unterdomäne direkt dem Blobendpunkt zugeordnet wird. Ein CNAME-Eintrag ist ein DNS-Feature (Domain Name System), das eine Quelldomäne zu einer Zieldomäne zuordnet. In unserem Beispiel ist die Quelldomäne Ihre eigene benutzerdefinierte Domäne und Unterdomäne (z.B. *www\.contoso.com*). Die Zieldomäne ist Ihr Blobdienstendpunkt (beispielsweise *mystorageaccount.blob.core.windows.net*).

Die direkte Methode ist im Abschnitt „Registrieren einer benutzerdefinierten Domäne“ beschrieben.

### <a name="intermediary-mapping-with-asverify"></a>Zwischengeschaltete Zuordnung mit *asverify*

Die zweite Methode verwendet ebenfalls CNAME-Einträge. Zur Vermeidung von Downtime kommt jedoch zunächst die spezielle Unterdomäne *asverify* zum Einsatz, die von Azure erkannt wird.

Die Zuordnung Ihrer benutzerdefinierten Domäne zu einem Blobendpunkt kann zu einer kurzzeitigen Downtime führen, während Sie die Domäne im [Azure-Portal](https://portal.azure.com) registrieren. Wenn die Domäne derzeit eine Anwendung mit einer Vereinbarung zum Servicelevel (Service-Level Agreement, SLA) unterstützt, die keine Downtime zulässt, verwenden Sie die Azure-Unterdomäne *asverify* als zwischengeschalteten Registrierungsschritt. Dieser Schritt stellt sicher, dass Benutzer auf Ihre Domäne zugreifen können, während die DNS-Zuordnung vorgenommen wird.

Die Zwischenschaltungsmethode ist unter Registrieren einer benutzerdefinierten Domäne mithilfe der Unterdomäne *asverify* beschrieben.

## <a name="register-a-custom-domain"></a>Registrieren einer benutzerdefinierten Domäne
Registrieren Sie die Domäne mithilfe des Verfahrens in diesem Abschnitt, wenn die folgenden Aussagen gelten:
* Es spielt keine Rolle, dass die Domäne kurzzeitig nicht für Benutzer verfügbar ist.
* Ihre benutzerdefinierte Domäne hostet zurzeit keine Anwendung. 

Mithilfe von Azure-DNS können Sie einen benutzerdefinierten DNS-Namen für Ihren Azure-Blob-Speicher erstellen. Weitere Informationen finden Sie unter [Bereitstellen von benutzerdefinierten Domäneneinstellungen für einen Azure-Dienst mit Azure DNS](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage).

Wenn Ihre benutzerdefinierte Domäne derzeit eine Anwendung unterstützt, die keine Downtime zulässt, gehen Sie nach dem Verfahren unter Registrieren einer benutzerdefinierten Domäne mithilfe der Unterdomäne *asverify* vor.

Erstellen Sie zum Konfigurieren eines benutzerdefinierten Domänennamens einen neuen CNAME-Eintrag in DNS. Der CNAME-Eintrag gibt einen Alias für einen Domänennamen an. In unserem Beispiel ordnet er die Adresse Ihrer benutzerdefinierten Domäne dem Blob Storage-Endpunkt Ihres Speicherkontos zu.

In der Regel können Sie die DNS-Einstellungen Ihrer Domäne auf der Website Ihrer Domänenregistrierungsstelle verwalten. Die Methoden zum Festlegen eines CNAME-Datensatzes der verschiedenen Registrierungen sind zwar ähnlich, jedoch unterscheiden sie sich auch in einigen Punkten. Das Konzept ist allerdings gleich. Da einige Basispakete für die Domänenregistrierung keine DNS-Konfiguration bieten, müssen Sie möglicherweise Ihr Domänenregistrierungspaket aktualisieren, bevor Sie den CNAME-Eintrag erstellen können.

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Speicherkonto.

1. Wählen Sie im Menübereich unter **Blob-Dienst** die Option **Benutzerdefinierte Domäne** aus.  
   Der Bereich **Benutzerdefinierte Domäne** wird geöffnet.

1. Melden Sie sich bei der Website Ihrer Domänenregistrierungsstelle an, und öffnen Sie die Seite für die DNS-Verwaltung.  
   Diese Seite finden Sie beispielsweise in einem Abschnitt mit der Bezeichnung **Domänenname**, **DNS** oder **Namenserververwaltung**.

1. Suchen Sie den Abschnitt zur Verwaltung von CNAMEs.  
   Möglicherweise müssen Sie eine Seite mit erweiterten Einstellungen aufrufen und nach **CNAME**, **Alias** oder **Unterdomänen** suchen.

1. Erstellen Sie einen neuen CNAME-Eintrag, geben Sie einen Unterdomänenalias wie etwa **www** oder **photos** (die Unterdomäne ist erforderlich, Stammdomänen werden nicht unterstützt) und anschließend einen Hostnamen an.  
   Der Hostname ist der Endpunkt Ihres Blobdiensts. Das Format ist *\<mystorageaccount>.blob.core.windows.net*. Dabei steht *mystorageaccount* für den Namen Ihres Speicherkontos. Der zu verwendende Hostname wird im [Azure-Portal](https://portal.azure.com) im ersten Element des Bereichs **Benutzerdefinierte Domäne** angezeigt. 

1. Geben Sie im Bereich **Benutzerdefinierte Domäne** in das Textfeld den Namen Ihrer benutzerdefinierten Domäne einschließlich der Unterdomäne ein.  
   Wenn Ihre Domäne beispielsweise *contoso.com* und Ihr Unterdomänenalias *www* lautet, geben Sie **www\.contoso.com** ein. Lautet Ihre Unterdomäne *photos*, geben Sie **photos.contoso.com** ein.

1. Wählen Sie zum Registrieren Ihrer benutzerdefinierten Domäne **Speichern** aus.  
   Wenn die Registrierung erfolgreich ist, werden Sie im Portal benachrichtigt, dass Ihr Speicherkonto erfolgreich aktualisiert wurde.

Sobald Ihr neuer CNAME-Eintrag über DNS weitergegeben wurde, können Ihre Benutzer Blobdaten mithilfe Ihrer benutzerdefinierten Domäne anzeigen, wenn sie die entsprechenden Berechtigungen haben.

## <a name="register-a-custom-domain-by-using-the-asverify-subdomain"></a>Registrieren einer benutzerdefinierten Domäne mithilfe der Unterdomäne *asverify*
Unterstützt Ihre benutzerdefinierte Domäne derzeit eine Anwendung mit einer SLA, die keine Downtime zulässt, gehen Sie zum Registrieren Ihrer benutzerdefinierten Domäne nach dem Verfahren in diesem Abschnitt vor. Indem Sie einen CNAME-Eintrag erstellen, der von *asverify.\<Unterdomäne.\<benutzerdefinierteDomäne>* auf *asverify.\<Speicherkonto>.blob.core.windows.net* verweist, können Sie Ihre Domäne bei Azure vorab registrieren. Anschließend können Sie einen zweiten CNAME-Eintrag erstellen, der von *\<Unterdomäne>.\<benutzerdefinierteDomäne>* auf *\<Speicherkonto>.blob.core.windows.net* verweist. Datenverkehr zu Ihrer benutzerdefinierten Domäne wird dann an Ihren Blobendpunkt geleitet.

Die Unterdomäne *asverify* ist eine spezielle Unterdomäne, die von Azure erkannt wird. Indem Sie Ihrer eigenen Unterdomäne *asverify* voranstellen, erlauben Sie Azure, Ihre benutzerdefinierte Domäne zu erkennen, ohne dass der DNS-Datensatz für die Domäne geändert werden muss. Wenn Sie den DNS-Eintrag für die Domäne ändern, wird diese dem Blobendpunkt ohne Downtime zugeordnet.

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Speicherkonto.

1. Wählen Sie im Menübereich unter **Blob-Dienst** die Option **Benutzerdefinierte Domäne** aus.  
   Der Bereich **Benutzerdefinierte Domäne** wird geöffnet.

1. Melden Sie sich bei der Website Ihres DNS-Anbieters an, und öffnen Sie die Seite für die DNS-Verwaltung.  
   Diese Seite finden Sie beispielsweise in einem Abschnitt mit der Bezeichnung **Domänenname**, **DNS** oder **Namenserververwaltung**.

1. Suchen Sie den Abschnitt zur Verwaltung von CNAMEs.  
   Möglicherweise müssen Sie eine Seite mit erweiterten Einstellungen aufrufen und nach **CNAME**, **Alias** oder **Unterdomänen** suchen.

1. Erstellen Sie einen neuen CNAME-Eintrag, und geben Sie einen Unterdomänenalias an, der die Unterdomäne *asverify* enthält, etwa **asverify.www** oder **asverify.photos**. Geben Sie anschließend einen Hostnamen ein.  
   Der Hostname ist der Endpunkt Ihres Blobdiensts. Das Format ist *asverify.\<mystorageaccount>.blob.core.windows.net*. Dabei steht *mystorageaccount* für den Namen Ihres Speicherkontos. Der zu verwendende Hostname wird im [Azure-Portal](https://portal.azure.com) im zweiten Element des Bereichs *Benutzerdefinierte Domäne* angezeigt.

1. Geben Sie im Bereich **Benutzerdefinierte Domäne** in das Textfeld den Namen Ihrer benutzerdefinierten Domäne einschließlich der Unterdomäne ein.  
   Fügen Sie *asverify* nicht ein. Wenn Ihre Domäne beispielsweise *contoso.com* und Ihr Unterdomänenalias *www* lautet, geben Sie **www\.contoso.com** ein. Lautet Ihre Unterdomäne *photos*, geben Sie **photos.contoso.com** ein.

1. Aktivieren Sie das Kontrollkästchen **Indirekte CNAME-Überprüfung verwenden**.

1. Wählen Sie zum Registrieren Ihrer benutzerdefinierten Domäne **Speichern** aus.  
   Wenn die Registrierung erfolgreich ist, werden Sie im Portal benachrichtigt, dass Ihr Speicherkonto erfolgreich aktualisiert wurde. Ihre benutzerdefinierte Domäne wurde von Azure überprüft, allerdings wird der Datenverkehr zu Ihrer Domäne noch nicht zu Ihrem Speicherkonto geleitet.

1. Kehren Sie zur Website Ihres DNS-Anbieters zurück, und erstellen Sie einen weiteren CNAME-Eintrag, in dem Ihre Unterdomäne dem Endpunkt Ihres Blobdiensts zugeordnet wird.  
   Geben Sie etwa die Unterdomäne *www* oder *photos* (ohne *asverify*) und den Hostnamen *\<mystorageaccount>.blob.core.windows.net* an (wobei *mystorageaccount* der Name Ihres Speicherkontos ist). Mit diesem Schritt ist die Registrierung Ihrer benutzerdefinierten Domäne abgeschlossen.

1. Abschließend können Sie den neu erstellten CNAME-Eintrag löschen, der die Unterdomäne *asverify* enthält und nur als zwischengeschalteter Schritt erforderlich war.

Sobald Ihr neuer CNAME-Eintrag über DNS weitergegeben wurde, können Ihre Benutzer Blobdaten mithilfe Ihrer benutzerdefinierten Domäne anzeigen, wenn sie die entsprechenden Berechtigungen haben.

## <a name="test-your-custom-domain"></a>Testen Ihrer benutzerdefinierten Domäne

Um zu bestätigen, dass Ihre benutzerdefinierte Domäne dem Endpunkt Ihres Blobdiensts zugeordnet ist, erstellen Sie einen Blob in einem öffentlichen Container in Ihrem Speicherkonto. Greifen Sie anschließend in einem Webbrowser mit einem URI im folgenden Format auf den Blob zu: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Sie können beispielsweise den folgenden URI verwenden, um auf ein Webformular im Container *myforms* in der benutzerdefinierten Unterdomäne *photos.contoso.com* zuzugreifen: `http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Aufheben der Registrierung einer benutzerdefinierten Domäne

Um die Registrierung einer benutzerdefinierten Domäne für Ihren Blob Storage-Endpunkt aufzuheben, verwenden Sie eines der folgenden Verfahren.

### <a name="azure-portal"></a>Azure-Portal

Führen Sie zum Entfernen der Einstellung für die benutzerdefinierte Domäne die folgenden Schritte aus:

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Speicherkonto.

1. Wählen Sie im Menübereich unter **Blob-Dienst** die Option **Benutzerdefinierte Domäne** aus.  
   Der Bereich **Benutzerdefinierte Domäne** wird geöffnet.

1. Löschen Sie den Inhalt des Textfelds, das den Namen der benutzerdefinierten Domäne enthält.

1. Wählen Sie die Schaltfläche **Speichern** aus.

Wenn die benutzerdefinierte Domäne erfolgreich entfernt wurde, wird in einer Portalbenachrichtigung angezeigt, dass Ihr Speicherkonto erfolgreich aktualisiert wurde.

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Verwenden Sie den CLI-Befehl [az storage account update](https://docs.microsoft.com/cli/azure/storage/account), und geben Sie eine leere Zeichenfolge (`""`) als Wert für das `--custom-domain`-Argument an, um die Registrierung einer benutzerdefinierten Domäne zu entfernen.

* Befehlsformat:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Befehlsbeispiel:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Verwenden Sie das PowerShell-Cmdlet [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount), und geben Sie eine leere Zeichenfolge (`""`) als Wert für das Argument `-CustomDomainName` an, um die Registrierung einer benutzerdefinierten Domäne zu entfernen.

* Befehlsformat:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Befehlsbeispiel:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>Nächste Schritte
* [Zuordnen von CDN-Inhalt (Content Delivery Network) zu einer benutzerdefinierten Domäne](../../cdn/cdn-map-content-to-custom-domain.md)
* [Verwenden von Azure CDN zum Zugreifen auf Blobs mit benutzerdefinierten Domänen über HTTPS](storage-https-custom-domain-cdn.md)
* [Hosten von statischen Websites in Azure Blob Storage (Vorschauversion)](storage-blob-static-website.md)
