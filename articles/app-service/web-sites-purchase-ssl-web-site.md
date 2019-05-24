---
title: Kaufen und Konfigurieren eines SSL-Zertifikats von Azure – Azure App Service | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein App Service-Zertifikat erwerben und es an Ihre App Service-App binden.
services: app-service
documentationcenter: .net
author: cephalin
manager: jpconnoc
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: apurvajo;cephalin
ms.custom: seodec18
ms.openlocfilehash: 0febb8fadd973b67ed232d6094d85894fb383d14
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65955710"
---
# <a name="buy-and-configure-an-ssl-certificate-for-azure-app-service"></a>Kaufen und Konfigurieren eines SSL-Zertifikats für Azure App Service

In diesem Tutorial erfahren Sie, wie Sie Ihre [App Service-App](https://docs.microsoft.com/azure/app-service/) oder [Funktions-App](https://docs.microsoft.com/azure/azure-functions/) schützen, indem Sie ein App Service-Zertifikat in [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) erstellen (kaufen) und es dann an eine App Service-App binden.

> [!TIP]
> App Service-Zertifikate können für alle Dienste – ob von Azure oder nicht – verwendet werden und sind nicht auf App Services beschränkt. Hierzu müssen Sie eine lokale PFX-Kopie eines App Service-Zertifikats erstellen, sodass Sie es beliebig verwenden können. Weitere Informationen finden Sie unter [Create a local PFX copy of App Service Certificate](https://blogs.msdn.microsoft.com/benjaminperkins/2017/04/12/export-an-azure-app-service-certificate-pfx-powershell/) (Erstellen einer lokalen PFX-Kopie eines App Service-Zertifikats).
>

## <a name="prerequisites"></a>Voraussetzungen

Im Rahmen dieser Schrittanleitung müssen Sie folgende Schritte durchführen:

- [Erstellen einer App Service-App](/azure/app-service/)
- [Zuordnen eines Domänennamens zu Ihrer App](app-service-web-tutorial-custom-domain.md) oder [Kaufen und Konfigurieren eines Domänennamens in Azure](manage-custom-dns-buy-domain.md)

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="start-certificate-order"></a>Starten einer Zertifikatreihenfolge

Starten Sie eine App Service-Zertifikatreihenfolge auf der <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">App Service Certificate-Seite zum Erstellen eines Zertifikats</a>.

![Zertifikaterstellung](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Die folgende Tabelle unterstützt Sie bei der Konfiguration des Zertifikats. Klicken Sie auf **Erstellen**, wenn Sie fertig sind.

| Einstellung | BESCHREIBUNG |
|-|-|
| NAME | Ein Anzeigename für Ihr App Service-Zertifikat. |
| Reiner Domänenhostname | Wenn Sie die Stammdomäne angeben, erhalten Sie ein Zertifikat, das *sowohl* die Stammdomäne als auch die `www`-Unterdomäne sichert. Um eine beliebige Unterdomäne zu sichern, geben Sie den vollqualifizierten Domänennamen der Unterdomäne hier an (z.B. `mysubdomain.contoso.com`). |
| Abonnement | Das Rechenzentrum, in dem die Web-App gehostet wird. |
| Ressourcengruppe | Die Ressourcengruppe mit dem Zertifikat. Sie können eine neue Ressourcengruppe verwenden oder z.B. die gleiche Ressourcengruppe wie die Ihrer App Service-App auswählen. |
| Zertifikat-SKU | Bestimmt den Typ des zu erstellenden Zertifikats, ganz gleich, ob es sich um ein Standardzertifikat oder ein [Platzhalterzertifikat](https://wikipedia.org/wiki/Wildcard_certificate) handelt. |
| Rechtliche Bedingungen | Klicken Sie auf diese Option, um zu bestätigen, dass Sie mit den rechtlichen Bedingungen einverstanden sind. Die Zertifikate werden von GoDaddy abgerufen. |

## <a name="store-in-azure-key-vault"></a>Speichern in Azure Key Vault

Sobald der Zertifikatkaufvorgang abgeschlossen ist, müssen Sie noch ein paar Schritte ausführen, bevor Sie das Zertifikat verwenden können. 

Wählen Sie das Zertifikat auf der Seite [App Service-Zertifikate](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) aus, und klicken Sie dann auf **Zertifikatkonfiguration** > **Schritt 1: Speichern**.

![Bild von Bereitschaft zum Speichern in KV einfügen](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) ist ein Azure-Dienst zum Schutz von kryptografischen Schlüsseln und Geheimnissen, die von Cloudanwendungen und -diensten verwendet werden. Dies ist der ideale Speicher für App Service-Zertifikate.

Klicken Sie auf der Seite **Key Vault-Status** auf **Key Vault-Repository**, um einen neuen Tresor zu erstellen oder einen vorhandenen Tresor auszuwählen. Wenn Sie einen neuen Tresor erstellen möchten, konfigurieren Sie mithilfe der folgende Tabelle den Tresor, und klicken Sie auf „Erstellen“. So wird ein neuer Schlüsseltresors im gleichen Abonnement und in der gleichen Ressourcengruppe erstellt.

| Einstellung | BESCHREIBUNG |
|-|-|
| NAME | Ein eindeutiger Name aus alphanumerischen Zeichen und Bindestrichen. |
| Ressourcengruppe | Es wird empfohlen, die gleiche Ressourcengruppe wie bei Ihrem App Service-Zertifikat auszuwählen. |
| Location | Wählen Sie denselben Speicherort wie bei Ihrer App Service-App aus. |
| Tarif | Weitere Informationen finden Sie unter [Key Vault – Preise](https://azure.microsoft.com/pricing/details/key-vault/). |
| Zugriffsrichtlinien| Definiert die Anwendungen und den zulässigen Zugriff auf die Tresorressourcen. Sie können dies später konfigurieren, indem Sie die Schritte unter [Erteilen von Zugriff für mehrere Anwendungen auf einen Schlüsseltresor](../key-vault/key-vault-group-permissions-for-apps.md) durchführen. |
| Zugriff über virtuelles Netzwerk | Beschränkt den Tresorzugriff auf bestimmte virtuelle Azure-Netzwerke. Sie können dies später konfigurieren, indem Sie die Schritte unter [Konfigurieren von Azure Key Vault-Firewalls und virtuellen Netzwerken](../key-vault/key-vault-network-security.md) durchführen. |

Wenn Sie den Tresor ausgewählt haben, schließen Sie die Seite **Key Vault-Repository**. Die Option **Speichern** sollte ein grünes Häkchen für eine erfolgreiche Ausführung anzeigen. Lassen Sie die Seite für den nächsten Schritt geöffnet.

## <a name="verify-domain-ownership"></a>Überprüfen des Domänenbesitzes

Klicken Sie auf der Seite **Zertifikatkonfiguration**, die Sie im letzten Schritt verwendet haben, auf **Schritt 2: Überprüfen**.

![](./media/app-service-web-purchase-ssl-web-site/verify-domain.png)

Klicken Sie auf **App Service-Überprüfung**. Da Sie die Domäne bereits Ihrer Web-App zugeordnet (siehe [Voraussetzungen](#prerequisites)), wurde sie bereits überprüft. Klicken Sie einfach auf **Überprüfen**, um diesen Schritt abzuschließen. Klicken Sie auf die Schaltfläche **Aktualisieren**, bis die Meldung **Zertifikatdomäne wurde überprüft.** angezeigt wird.

> [!NOTE]
> Vier Arten von Domänenüberprüfungsmethoden werden unterstützt: 
> 
> - **App Service**: Die einfachste Option, wenn die Domäne bereits einer App Service-App im gleichen Abonnement zugeordnet ist. Sie nutzt die Tatsache aus, dass die App Service-App den Domänenbesitz bereits überprüft hat.
> - **Domäne**: Mit dieser Option wird eine [App Service-Domäne überprüft, die Sie von Azure erworben haben](manage-custom-dns-buy-domain.md). Azure fügt die TXT-Überprüfungseinträge automatisch für Sie hinzu und schließt den Vorgang ab.
> - **E-Mail**: Mit dieser Option wird die Domäne überprüft, indem Sie eine E-Mail an den Domänenadministrator senden. Anweisungen werden bei Auswahl der Option bereitgestellt.
> - **Manuell**: Hiermit wird die Domäne entweder mit einer HTML-Seite (nur **Standard**-Zertifikat) oder einem DNS-TXT-Eintrag überprüft. Anweisungen werden bei Auswahl der Option bereitgestellt.

## <a name="bind-certificate-to-app"></a>Binden eines Zertifikats an eine App

Klicken Sie im linken Menü des **[Azure-Portals](https://portal.azure.com/)** auf **App Services** > **\<Ihre_App>**.

Wählen Sie im linken Navigationsbereich Ihrer App **SSL-Einstellungen** > **Private Zertifikate (.pfx)** > **App Service-Zertifikat importieren**.

![Bild von Zertifikatimport einfügen](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

Wählen Sie das Zertifikat aus, das Sie soeben gekauft haben.

Nachdem das Zertifikat importiert wurde, müssen Sie es an einen zugeordneten Domänennamen in Ihrer App binden. Klicken Sie auf **Bindungen** > **SSL-Bindung hinzufügen**. 

![Bild von Zertifikatimport einfügen](./media/app-service-web-purchase-ssl-web-site/AddBinding.png)

Konfigurieren Sie mithilfe der folgende Tabelle die Bindung im Dialogfeld **SSL-Bindungen**, und klicken Sie dann auf **Bindung hinzufügen**.

| Einstellung | BESCHREIBUNG |
|-|-|
| Hostname | Der Domänenname, der für die SSL-Bindung hinzugefügt werden soll. |
| Fingerabdruck des privaten Zertifikats | Das zu bindende Zertifikat. |
| SSL-Typ | <ul><li>**SNI SSL**: Es können mehrere SNI-basierte SSL-Bindungen hinzugefügt werden. Bei dieser Option können mehrere zur selben IP-Adresse zugehörige Domänen durch mehrere SSL-Zertifikate geschützt werden. Die meisten modernen Browser (einschließlich Internet Explorer, Chrome, Firefox und Opera) unterstützen SNI (ausführlichere Informationen zur Browserunterstützung finden Sie unter [Servernamensanzeige](https://wikipedia.org/wiki/Server_Name_Indication)).</li><li>**IP-basiertes SSL** : Möglicherweise kann nur eine IP-basierte SSL-Bindung hinzugefügt werden. Bei dieser Option kann eine dedizierte öffentliche IP-Adresse nur durch ein SSL-Zertifikat geschützt werden. Führen Sie nach dem Konfigurieren der Bindung die Schritte unter [Neuzuordnen eines Datensatzes für IP-SSL](app-service-web-tutorial-custom-ssl.md#remap-a-record-for-ip-ssl) durch. </li></ul> |

## <a name="verify-https-access"></a>Überprüfen des HTTPS-Zugriffs

Rufen Sie Ihre App über `HTTPS://<domain_name>` auf (anstelle von `HTTP://<domain_name>`), um zu überprüfen, ob das Zertifikat richtig konfiguriert wurde.

## <a name="rekey-certificate"></a>Neue Schlüssel für Zertifikat erstellen

Wenn Sie vermuten, das der private Schlüssel Ihres Zertifikats gefährdet ist, können Sie neue Schlüssel für das Zertifikat erstellen. Wählen Sie das Zertifikat auf der Seite [App Service Certificate](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) aus, und klicken Sie dann im linken Navigationsbereich auf **Neuen Schlüssel erstellen und synchronisieren**.

Klicken Sie auf **Erneute Schlüsselerstellung**, um den Prozess zu starten. Dieser Prozess kann 1 bis 10 Minuten in Anspruch nehmen.

![Bild von erneuter Schlüsselerstellung für SSL einfügen](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

Bei erneuter Schlüsselerstellung für Ihr Zertifikat wird von der Zertifizierungsstelle ein neues Zertifikat ausgestellt.

Wenn der Vorgang zur erneuten Schlüsselerstellung abgeschlossen ist, klicken Sie auf **Synchronisierung**. Der Synchronisierungsvorgang aktualisiert automatisch die Hostnamenbindungen für das Zertifikat in App Service, ohne dass es zu Downtime für Ihre Apps kommt.

> [!NOTE]
> Wenn Sie nicht auf **Synchronisierung** klicken, synchronisiert App Service Ihr Zertifikat innerhalb von 48 Stunden.

## <a name="renew-certificate"></a>Verlängern des Zertifikats

Um zu einem beliebigen Zeitpunkt die automatische Verlängerung Ihres Zertifikats zu aktivieren, wählen Sie das Zertifikat auf der Seite [App Service Certificate](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) aus, und klicken Sie dann im linken Navigationsbereich auf **Einstellungen für die automatische Verlängerung**.

Klicken Sie nacheinander auf **Ein** und **Speichern**. Zertifikate können vor Ablauf automatisch um 60 Tage verlängert werden, wenn Sie die automatische Verlängerung aktiviert haben.

![Zertifikat automatisch verlängern](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

Um das Zertifikat stattdessen manuell zu verlängern, klicken Sie auf **Manuelle Verlängerung**. Sie können anfordern, dass Ihr Zertifikat vor Ablauf manuell um 60 Tage verlängert wird.

Wenn der Verlängerungsvorgang abgeschlossen ist, klicken Sie auf **Synchronisierung**. Der Synchronisierungsvorgang aktualisiert automatisch die Hostnamenbindungen für das Zertifikat in App Service, ohne dass es zu Downtime für Ihre Apps kommt.

> [!NOTE]
> Wenn Sie nicht auf **Synchronisierung** klicken, synchronisiert App Service Ihr Zertifikat innerhalb von 48 Stunden.

## <a name="automate-with-scripts"></a>Automatisieren mit Skripts

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Weitere Ressourcen

* [Erzwingen von HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [Erzwingen von TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions)
* [Verwenden eines SSL-Zertifikats in Ihrem Anwendungscode in Azure App Service](app-service-web-ssl-cert-load.md)
* [Häufig gestellte Fragen: App Service-Zertifikate](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
