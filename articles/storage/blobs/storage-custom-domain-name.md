---
title: Zuordnen einer benutzerdefinierten Domäne zu einem Azure Blob Storage-Endpunkt
titleSuffix: Azure Storage
description: Ordnen Sie einem Blob Storage- oder Webendpunkt in einem Azure-Speicherkonto eine benutzerdefinierte Domäne zu.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 01/23/2020
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: dcc6f3bca80cb5860679327226d3e034c3e9b14a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95996864"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Zuordnen einer benutzerdefinierten Domäne zu einem Azure Blob Storage-Endpunkt

Sie können einem Blob-Dienstendpunkt oder einem Endpunkt einer [statischen Website](storage-blob-static-website.md) eine benutzerdefinierte Domäne zuordnen. 

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE] 
> Diese Zuordnung funktioniert nur für Unterdomänen (z. B. `www.contoso.com`). Wenn Ihr Webendpunkt für die Stammdomäne (z. B. `contoso.com`) verfügbar sein soll, müssen Sie Azure CDN verwenden. Eine entsprechende Anleitung finden Sie im Abschnitt [Zuordnen einer benutzerdefinierten Domäne mit aktiviertem HTTPS](#enable-https) in diesem Artikel. Weil Sie in diesem Abschnitt dieses Artikels die Stammdomäne Ihrer benutzerdefinierten Domäne aktivieren, ist der Schritt innerhalb dieses Abschnitts zum Aktivieren von HTTPS optional. 

<a id="enable-http"></a>

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Zuordnen einer benutzerdefinierten Domäne nur mit aktiviertem HTTP

Dieser Ansatz ist einfacher, dabei wird jedoch nur HTTP-Zugriff aktiviert. Wenn das Speicherkonto für die [sichere Übertragung](../common/storage-require-secure-transfer.md) über HTTPS konfiguriert ist, müssen Sie HTTPS-Zugriff für Ihre benutzerdefinierte Domäne aktivieren. 

Informationen zum Aktivieren des HTTPS-Zugriffs finden Sie im Abschnitt [Zuordnen einer benutzerdefinierten Domäne mit aktiviertem HTTPS](#enable-https) in diesem Artikel. 

<a id="map-a-domain"></a>

### <a name="map-a-custom-domain"></a>Zuordnen einer benutzerdefinierten Domäne

> [!IMPORTANT]
> Während Sie die Konfiguration abschließen, ist Ihre benutzerdefinierte Domäne für Benutzer kurzzeitig nicht verfügbar. Wenn Ihre Domäne aktuell eine Anwendung mit einer Vereinbarung zum Service Level (Service-Level Agreement, SLA) unterstützt, für die keine Ausfallzeiten anfallen dürfen, führen Sie die in diesem Artikel im Abschnitt [Zuordnen einer benutzerdefinierten Domäne ohne Ausfallzeit](#zero-down-time) aufgeführten Schritte aus, um sicherzustellen, dass Benutzer auf Ihre Domäne zugreifen können, während die DNS-Zuordnung erfolgt.

Wenn es keine Rolle spielt, dass die Domäne für Ihre Benutzer kurzzeitig nicht verfügbar ist, führen Sie die folgenden Schritte aus.

:heavy_check_mark: Schritt 1: Rufen Sie den Hostnamen Ihres Speicherendpunkts ab.

:heavy_check_mark: Schritt 2: Erstellen Sie einen kanonischen Namenseintrag (CNAME-Eintrag) bei Ihrem Domänenanbieter.

:heavy_check_mark: Schritt 3: Registrieren Sie die benutzerdefinierte Domäne bei Azure. 

:heavy_check_mark: Schritt 4: Testen Sie Ihre benutzerdefinierte Domäne.

<a id="endpoint"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Schritt 1: Abrufen des Hostnamens Ihres Speicherendpunkts 

Der Hostname entspricht der Speicherendpunkt-URL ohne die Protokoll-ID und den nachgestellten Schrägstrich. 

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Speicherkonto.

2. Wählen Sie im Menübereich unter **Einstellungen** die Option **Eigenschaften** aus.  

3. Kopieren Sie den Wert von **Primärer Blob-Dienstendpunkt** oder **Primärer statischer Websiteendpunkt** in eine Textdatei. 

4. Entfernen Sie die Protokoll-ID (*z. B.* HTTPS) und den nachgestellten Schrägstrich aus dieser Zeichenfolge. Die folgende Tabelle enthält einige Beispiele.

   | Endpunkttyp |  endpoint | Hostname |
   |------------|-----------------|-------------------|
   |Blobdienst  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |Statische Website  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Sie können diesen Wert später festlegen.

<a id="create-cname-record"></a>

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>Schritt 2: Erstellen eines kanonischen Namenseintrags (CNAME-Eintrags) bei Ihrem Domänenanbieter

Erstellen Sie einen CNAME-Eintrag, der auf Ihren Hostnamen verweist. Ein CNAME-Eintrag ist eine Art von DNS-Eintrag, mit dem ein Quelldomänenname einem Zieldomänennamen zugeordnet wird.

1. Melden Sie sich bei der Website Ihrer Domänenregistrierungsstelle an, und navigieren Sie dann zur Seite zum Verwalten von DNS-Einstellungen.

   Diese Seite finden Sie beispielsweise in einem Abschnitt mit der Bezeichnung **Domänenname**, **DNS** oder **Namenserververwaltung**.

2. Suchen Sie den Abschnitt zum Verwalten von CNAME-Einträgen. 

   Möglicherweise müssen Sie eine Seite mit erweiterten Einstellungen aufrufen und nach **CNAME**, **Alias** oder **Unterdomänen** suchen.

3. Erstellen Sie einen CNAME-Eintrag. Geben Sie als Teil dieses Eintrags die folgenden Elemente an: 

   - Den Unterdomänenalias, z. B. `www` oder `photos`. Die Unterdomäne ist erforderlich, Stammdomänen werden nicht unterstützt. 
      
   - Den Hostnamen, den Sie weiter oben in diesem Artikel im Abschnitt [Abrufen des Hostnamens Ihres Speicherendpunkts](#endpoint) erhalten haben. 

<a id="register"></a>

#### <a name="step-3-register-your-custom-domain-with-azure"></a>Schritt 3: Registrieren Ihrer benutzerdefinierten Domäne bei Azure

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Speicherkonto.

2. Wählen Sie im Menübereich unter **Blob-Dienst** die Option **Benutzerdefinierte Domäne** aus.  

   ![Option „Benutzerdefinierte Domäne“](./media/storage-custom-domain-name/custom-domain-button.png "Benutzerdefinierte Domäne")

   Der Bereich **Benutzerdefinierte Domäne** wird geöffnet.

3. Geben Sie im Textfeld **Domänenname** den Namen Ihrer benutzerdefinierten Domäne einschließlich der Unterdomäne ein.  
   
   Wenn Ihre Domäne beispielsweise *contoso.com* und Ihr Unterdomänenalias *www* lautet, geben Sie `www.contoso.com` ein. Lautet Ihre Unterdomäne *photos*, geben Sie `photos.contoso.com` ein.

4. Wählen Sie zum Registrieren der benutzerdefinierten Domäne die Schaltfläche **Speichern** aus.

   Nachdem der CNAME-Eintrag über die Domänennamenserver (DNS) weitergegeben wurde, können Ihre Benutzer Blobdaten mithilfe der benutzerdefinierten Domäne anzeigen, sofern sie über die entsprechenden Berechtigungen verfügen.

#### <a name="step-4-test-your-custom-domain"></a>Schritt 4: Testen Ihrer benutzerdefinierten Domäne

Um zu bestätigen, dass Ihre benutzerdefinierte Domäne dem Endpunkt Ihres Blobdiensts zugeordnet ist, erstellen Sie einen Blob in einem öffentlichen Container in Ihrem Speicherkonto. Greifen Sie anschließend in einem Webbrowser mit einem URI im folgenden Format auf den Blob zu: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Sie können beispielsweise den folgenden URI verwenden, um auf ein Webformular im Container *myforms* in der benutzerdefinierten Unterdomäne *photos.contoso.com* zuzugreifen: `http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time"></a>

### <a name="map-a-custom-domain-with-zero-downtime"></a>Zuordnen einer benutzerdefinierten Domäne ohne Ausfallzeit

> [!NOTE]
> Wenn es keine Rolle spielt, dass die Domäne für Ihre Benutzer kurzzeitig nicht verfügbar ist, können Sie die in diesem Artikel im Abschnitt [Zuordnen einer benutzerdefinierten Domäne](#map-a-domain) aufgeführten Schritte ausführen. Dies ist ein einfacherer Ansatz mit weniger Schritten.  

Wenn Ihre Domäne aktuell eine Anwendung mit einer Vereinbarung zum Service Level (Service-Level Agreement, SLA) unterstützt, für die keine Ausfallzeiten anfallen dürfen, führen Sie die folgenden Schritte aus, um sicherzustellen, dass Benutzer auf Ihre Domäne zugreifen können, während die DNS-Zuordnung erfolgt. 

:heavy_check_mark: Schritt 1: Rufen Sie den Hostnamen Ihres Speicherendpunkts ab.

:heavy_check_mark: Schritt 2: Erstellen Sie einen temporären kanonischen Namenseintrag (CNAME-Eintrag) bei Ihrem Domänenanbieter.

:heavy_check_mark: Schritt 3: Registrieren Sie die benutzerdefinierte Domäne vorab bei Azure.

:heavy_check_mark: Schritt 4: Erstellen Sie einen CNAME-Eintrag bei Ihrem Domänenanbieter.

:heavy_check_mark: Schritt 5: Testen Sie Ihre benutzerdefinierte Domäne.

<a id="endpoint-2"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Schritt 1: Abrufen des Hostnamens Ihres Speicherendpunkts 

Der Hostname entspricht der Speicherendpunkt-URL ohne die Protokoll-ID und den nachgestellten Schrägstrich. 

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Speicherkonto.

2. Wählen Sie im Menübereich unter **Einstellungen** die Option **Eigenschaften** aus.  

3. Kopieren Sie den Wert von **Primärer Blob-Dienstendpunkt** oder **Primärer statischer Websiteendpunkt** in eine Textdatei. 

4. Entfernen Sie die Protokoll-ID (*z. B.* HTTPS) und den nachgestellten Schrägstrich aus dieser Zeichenfolge. Die folgende Tabelle enthält einige Beispiele.

   | Endpunkttyp |  endpoint | Hostname |
   |------------|-----------------|-------------------|
   |Blobdienst  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |Statische Website  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Sie können diesen Wert später festlegen.

#### <a name="step-2-create-a-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>Schritt 2: Erstellen eines temporären kanonischen Namenseintrags (CNAME-Eintrags) bei Ihrem Domänenanbieter

Erstellen Sie einen temporären CNAME-Eintrag, der auf Ihren Hostnamen verweist. Ein CNAME-Eintrag ist eine Art von DNS-Eintrag, mit dem ein Quelldomänenname einem Zieldomänennamen zugeordnet wird.

1. Melden Sie sich bei der Website Ihrer Domänenregistrierungsstelle an, und navigieren Sie dann zur Seite zum Verwalten von DNS-Einstellungen.

   Diese Seite finden Sie beispielsweise in einem Abschnitt mit der Bezeichnung **Domänenname**, **DNS** oder **Namenserververwaltung**.

2. Suchen Sie den Abschnitt zum Verwalten von CNAME-Einträgen. 

   Möglicherweise müssen Sie eine Seite mit erweiterten Einstellungen aufrufen und nach **CNAME**, **Alias** oder **Unterdomänen** suchen.

3. Erstellen Sie einen CNAME-Eintrag. Geben Sie als Teil dieses Eintrags die folgenden Elemente an: 

   - Den Unterdomänenalias, z. B. `www` oder `photos`. Die Unterdomäne ist erforderlich, Stammdomänen werden nicht unterstützt.

     Fügen Sie dem Alias die Unterdomäne `asverify` hinzu. Zum Beispiel: `asverify.www` oder `asverify.photos`.
       
   - Den Hostnamen, den Sie weiter oben in diesem Artikel im Abschnitt [Abrufen des Hostnamens Ihres Speicherendpunkts](#endpoint) erhalten haben. 

     Fügen Sie dem Hostnamen die Unterdomäne `asverify` hinzu. Beispiel: `asverify.mystorageaccount.blob.core.windows.net`.

4. Wählen Sie zum Registrieren der benutzerdefinierten Domäne die Schaltfläche **Speichern** aus.

   Wenn die Registrierung erfolgreich ist, werden Sie im Portal benachrichtigt, dass Ihr Speicherkonto erfolgreich aktualisiert wurde. Ihre benutzerdefinierte Domäne wurde von Azure überprüft, allerdings wird der Datenverkehr zu Ihrer Domäne noch nicht zu Ihrem Speicherkonto geleitet.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>Schritt 3: Vorabregistrieren Ihrer benutzerdefinierten Domäne bei Azure

Wenn Sie Ihre benutzerdefinierte Domäne vorab bei Azure registrieren, kann Azure Ihre benutzerdefinierte Domäne erkennen, ohne dass der DNS-Eintrag für die Domäne geändert werden muss. Auf diese Weise wird die Domäne dem Blob-Endpunkt ohne Ausfallzeit zugeordnet, wenn Sie den DNS-Eintrag für die Domäne ändern.

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Speicherkonto.

2. Wählen Sie im Menübereich unter **Blob-Dienst** die Option **Benutzerdefinierte Domäne** aus.  

   ![Option „Benutzerdefinierte Domäne“](./media/storage-custom-domain-name/custom-domain-button.png "Benutzerdefinierte Domäne")

   Der Bereich **Benutzerdefinierte Domäne** wird geöffnet.

3. Geben Sie im Textfeld **Domänenname** den Namen Ihrer benutzerdefinierten Domäne einschließlich der Unterdomäne ein.  
   
   Wenn Ihre Domäne beispielsweise *contoso.com* und Ihr Unterdomänenalias *www* lautet, geben Sie `www.contoso.com` ein. Lautet Ihre Unterdomäne *photos*, geben Sie `photos.contoso.com` ein.

4. Aktivieren Sie das Kontrollkästchen **Indirekte CNAME-Überprüfung verwenden**.

5. Wählen Sie zum Registrieren der benutzerdefinierten Domäne die Schaltfläche **Speichern** aus.
  
   Nachdem der CNAME-Eintrag über die Domänennamenserver (DNS) weitergegeben wurde, können Ihre Benutzer Blobdaten mithilfe der benutzerdefinierten Domäne anzeigen, sofern sie über die entsprechenden Berechtigungen verfügen.

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>Schritt 4: Erstellen eines CNAME-Eintrags bei Ihrem Domänenanbieter

Erstellen Sie einen temporären CNAME-Eintrag, der auf Ihren Hostnamen verweist.

1. Melden Sie sich bei der Website Ihrer Domänenregistrierungsstelle an, und navigieren Sie dann zur Seite zum Verwalten von DNS-Einstellungen.

   Diese Seite finden Sie beispielsweise in einem Abschnitt mit der Bezeichnung **Domänenname**, **DNS** oder **Namenserververwaltung**.

2. Suchen Sie den Abschnitt zum Verwalten von CNAME-Einträgen. 

   Möglicherweise müssen Sie eine Seite mit erweiterten Einstellungen aufrufen und nach **CNAME**, **Alias** oder **Unterdomänen** suchen.

3. Erstellen Sie einen CNAME-Eintrag. Geben Sie als Teil dieses Eintrags die folgenden Elemente an: 

   - Den Unterdomänenalias, z. B. `www` oder `photos`. Die Unterdomäne ist erforderlich, Stammdomänen werden nicht unterstützt.
      
   - Den Hostnamen, den Sie weiter oben in diesem Artikel im Abschnitt [Abrufen des Hostnamens Ihres Speicherendpunkts](#endpoint-2) erhalten haben. 

#### <a name="step-5-test-your-custom-domain"></a>Schritt 5: Testen Ihrer benutzerdefinierten Domäne

Um zu bestätigen, dass Ihre benutzerdefinierte Domäne dem Endpunkt Ihres Blobdiensts zugeordnet ist, erstellen Sie einen Blob in einem öffentlichen Container in Ihrem Speicherkonto. Greifen Sie anschließend in einem Webbrowser mit einem URI im folgenden Format auf den Blob zu: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Sie können beispielsweise den folgenden URI verwenden, um auf ein Webformular im Container *myforms* in der benutzerdefinierten Unterdomäne *photos.contoso.com* zuzugreifen: `http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Entfernen der Zuordnung einer benutzerdefinierten Domäne

Wenn Sie die Zuordnung einer benutzerdefinierten Domäne entfernen möchten, müssen Sie die Registrierung der benutzerdefinierten Domäne aufheben. Wenden Sie eines der folgenden Verfahren an.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie zum Entfernen der Einstellung für die benutzerdefinierte Domäne die folgenden Schritte aus:

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Speicherkonto.

2. Wählen Sie im Menübereich unter **Blob-Dienst** die Option **Benutzerdefinierte Domäne** aus.  
   Der Bereich **Benutzerdefinierte Domäne** wird geöffnet.

3. Löschen Sie den Inhalt des Textfelds, das den Namen der benutzerdefinierten Domäne enthält.

4. Wählen Sie die Schaltfläche **Speichern** aus.

Nachdem die benutzerdefinierte Domäne erfolgreich entfernt wurde, wird in einer Portalbenachrichtigung angezeigt, dass Ihr Speicherkonto erfolgreich aktualisiert wurde.

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den CLI-Befehl [az storage account update](/cli/azure/storage/account), und geben Sie eine leere Zeichenfolge (`""`) als Wert für das `--custom-domain`-Argument an, um die Registrierung einer benutzerdefinierten Domäne zu entfernen.

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

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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
---

<a id="enable-https"></a>

## <a name="map-a-custom-domain-with-https-enabled"></a>Zuordnen einer benutzerdefinierten Domäne mit aktiviertem HTTPS

Bei diesem Ansatz müssen mehr Schritte ausgeführt werden, aber dabei wird HTTPS-Zugriff aktiviert. 

Wenn die Benutzer nicht über HTTPS auf Ihren Blob- oder Webinhalt zugreifen müssen, lesen Sie den Abschnitt [Zuordnen einer benutzerdefinierten Domäne nur mit aktiviertem HTTP](#enable-http) in diesem Artikel. 

Führen Sie die folgenden Schritte aus, um eine benutzerdefinierte Domäne zuzuordnen und HTTPS-Zugriff zu aktivieren:

1. Aktivieren Sie [Azure CDN](../../cdn/cdn-overview.md) für Ihren Blob- oder Webendpunkt. 

   Lesen Sie bei Verwendung eines Blob Storage-Endpunkts den Artikel [Integrieren eines Azure-Speicherkontos in Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md). 

   Lesen Sie bei Verwendung eines Endpunkts einer statischen Website den Artikel [Integrieren einer statischen Website in Azure CDN](static-website-content-delivery-network.md).

2. [Zuordnen von CDN-Inhalt (Content Delivery Network) zu einer benutzerdefinierten Domäne](../../cdn/cdn-map-content-to-custom-domain.md).

3. [Aktivieren von HTTPS für eine benutzerdefinierte Azure CDN-Domäne](../../cdn/cdn-custom-ssl.md).

   > [!NOTE] 
   > Wenn Sie Ihre statische Website aktualisieren, stellen Sie sicher, dass Sie zwischengespeicherte Inhalte auf den CDN-Edge-Servern löschen, indem Sie den CDN-Endpunkt bereinigen. Weitere Informationen finden Sie unter [Löschen eines Azure CDN-Endpunkts](../../cdn/cdn-purge-endpoint.md).

4. (Optional) Lesen Sie die folgenden Anleitungen:

   * [Shared Access Signature-Token (SAS-Token) – Azure CDN](../../cdn/cdn-storage-custom-domain-https.md#shared-access-signatures)

   * [HTTP-zu-HTTPS-Umleitung – Azure CDN](../../cdn/cdn-storage-custom-domain-https.md#http-to-https-redirection)

   * [Preise und Abrechnung bei Verwendung von Blob Storage – Azure CDN](../../cdn/cdn-storage-custom-domain-https.md#http-to-https-redirection)

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über das [Hosten von statischen Websites in Azure Blob Storage](storage-blob-static-website.md).