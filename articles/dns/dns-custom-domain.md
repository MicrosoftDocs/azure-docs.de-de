---
title: Integrieren von Azure DNS in Ihre Azure-Ressourcen – Azure DNS
description: In diesem Artikel erfahren Sie, wie Sie mit Azure DNS für Ihre Azure-Ressourcen DNS bereitstellen.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 7/13/2019
ms.author: rohink
ms.openlocfilehash: 4d8af5815e544698ab833001e5ce6d0f4a30a264
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487397"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Bereitstellen von benutzerdefinierten Domäneneinstellungen für einen Azure-Dienst mit Azure DNS

Azure DNS stellt DNS für eine benutzerdefinierte Domäne für Ihre Azure-Ressourcen bereit, die benutzerdefinierte Domänen unterstützen oder einen vollqualifizierten Domänennamen (FQDN) aufweisen. Dies ist beispielsweise der Fall, wenn Sie eine Azure-Web-App haben und möchten, dass Ihre Benutzer über contoso.com oder www\.contoso.com als FQDN Zugriff darauf haben sollen. Dieser Artikel führt Sie durch die Konfiguration Ihres Azure-Diensts mit Azure DNS für die Verwendung von benutzerdefinierten Domänen.

## <a name="prerequisites"></a>Voraussetzungen

Um Azure DNS für Ihre benutzerdefinierte Domäne verwenden zu können, müssen Sie zuerst Ihre Domäne an Azure DNS delegieren. Anweisungen zum Konfigurieren Ihrer Namenserver für die Delegierung finden Sie unter [Delegieren einer Domäne an Azure DNS](./dns-delegate-domain-azure-dns.md). Nachdem Ihre Domäne an Ihre Azure-DNS-Zone delegiert wurde, können Sie die erforderlichen DNS-Einträge konfigurieren.

Sie können eine Vanity oder eine benutzerdefinierte Domäne für [Azure-Funktionen-Apps](#azure-function-app), [öffentliche IP-Adressen](#public-ip-address), [App Service (Web-Apps)](#app-service-web-apps), [Blobspeicher](#blob-storage) und [Azure CDN](#azure-cdn) konfigurieren.

## <a name="azure-function-app"></a>Azure-Funktionen-App

Um eine benutzerdefinierte Domäne für Azure-Funktionen-Apps zu konfigurieren, wird ein CNAME-Datensatz sowie die Konfiguration für die Funktionen-App selbst erstellt.
 
Navigieren Sie zu **Funktionen-App** , und wählen Sie Ihre Funktionen-App aus. Klicken Sie auf **Plattformfeatures** und unter **Netzwerk** auf **Benutzerdefinierte Domänen**.

![Blatt „Funktionen-App“](./media/dns-custom-domain/functionapp.png)

Beachten Sie die aktuelle URL auf dem Blatt **Benutzerdefinierte Domänen**. Diese Adresse wird als Alias für den erstellten DNS-Datensatz verwendet.

![Blatt „Benutzerdefinierte Domäne“](./media/dns-custom-domain/functionshostname.png)

Navigieren Sie zu Ihrer DNS-Zone, und klicken Sie auf **+ Datensatzgruppe**. Füllen Sie auf dem Blatt **Datensatzgruppe hinzufügen** die folgenden Informationen aus, und klicken Sie zum Erstellen auf **OK**.

|Eigenschaft  |Wert  |BESCHREIBUNG  |
|---------|---------|---------|
|Name     | myfunctionapp        | Dieser Wert bildet zusammen mit der Domänennamenbezeichnung den FQDN für den Namen der benutzerdefinierten Domäne.        |
|type     | CNAME        | Verwenden Sie einen CNAME-Datensatz als Alias.        |
|TTL     | 1        | 1 steht für 1 Stunde.        |
|TTL-Einheit     | Stunden        | Stunden werden als Maßeinheit für die Zeit verwendet.         |
|Alias     | adatumfunction.azurewebsites.net        | Der DNS-Name, für den Sie den Alias erstellen, ist in diesem Beispiel der standardmäßig für die Funktionen-App bereitgestellte DNS-Name „adatumfunction.azurewebsites.net“.        |

Navigieren Sie wieder zu Ihrer Funktionen-App, klicken Sie auf **Plattformfeatures** , und klicken Sie unter **Netzwerk** auf **Benutzerdefinierte Domänen**. Klicken Sie anschließend unter **Benutzerdefinierte Hostnamen** auf **+ Hostnamen hinzufügen**.

Geben Sie auf dem Blatt **Hostnamen hinzufügen** den CNAME-Datensatz in das Textfeld **Hostname** ein, und klicken Sie auf **Überprüfen**. Wenn der Datensatz gefunden wird, wird die Schaltfläche **Hostnamen hinzufügen** angezeigt. Klicken Sie auf **Hostnamen hinzufügen** , um den Alias hinzuzufügen.

![Blatt „Hostnamen hinzufügen“ von Funktionen-Apps](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Öffentliche IP-Adresse

Um eine benutzerdefinierte Domäne für Dienste zu konfigurieren, die eine öffentliche IP-Adresse als Ressource verwenden (z.B. Application Gateway, Load Balancer, Clouddienst, Resource Manager-VMs und klassische VMs), wird ein A-Eintrag verwendet.

Navigieren Sie zu **Netzwerk** > **Öffentliche IP-Adresse** , wählen Sie als Ressource die öffentliche IP-Adresse aus, und klicken Sie auf **Konfiguration**. Notieren Sie sich die abgebildete IP-Adresse.

![Blatt „Öffentliche IP-Adresse“](./media/dns-custom-domain/publicip.png)

Navigieren Sie zu Ihrer DNS-Zone, und klicken Sie auf **+ Datensatzgruppe**. Füllen Sie auf dem Blatt **Datensatzgruppe hinzufügen** die folgenden Informationen aus, und klicken Sie zum Erstellen auf **OK**.


|Eigenschaft  |Wert  |BESCHREIBUNG  |
|---------|---------|---------|
|Name     | mywebserver        | Dieser Wert bildet zusammen mit der Domänennamenbezeichnung den FQDN für den Namen der benutzerdefinierten Domäne.        |
|type     | Ein        | Verwenden Sie einen A-Datensatz, da die Ressource eine IP-Adresse ist.        |
|TTL     | 1        | 1 steht für 1 Stunde.        |
|TTL-Einheit     | Stunden        | Stunden werden als Maßeinheit für die Zeit verwendet.         |
|IP-Adresse     | `<your ip address>`       | Öffentliche IP-Adresse|

![Erstellen eines A-Eintrags](./media/dns-custom-domain/arecord.png)

Nachdem der A-Datensatz erstellt wurde, führen Sie `nslookup` aus, um den Datensatz zu überprüfen.

![public ip dns lookup](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>App Service (Web-Apps)

Die folgenden Schritte führen Sie durch die Konfiguration einer benutzerdefinierten Domäne für eine App Service-Web-App.

Navigieren Sie zu **App Service** , und wählen Sie die Ressource aus, für die Sie einen benutzerdefinierten Domänennamen konfigurieren. Klicken Sie dann auf **Benutzerdefinierte Domänen**.

Beachten Sie die aktuelle URL auf dem Blatt **Benutzerdefinierte Domänen**. Diese Adresse wird als Alias für den erstellten DNS-Datensatz verwendet.

![Blatt „Benutzerdefinierte Domänen“](./media/dns-custom-domain/url.png)

Navigieren Sie zu Ihrer DNS-Zone, und klicken Sie auf **+ Datensatzgruppe**. Füllen Sie auf dem Blatt **Datensatzgruppe hinzufügen** die folgenden Informationen aus, und klicken Sie zum Erstellen auf **OK**.


|Eigenschaft  |Wert  |BESCHREIBUNG  |
|---------|---------|---------|
|Name     | mywebserver        | Dieser Wert bildet zusammen mit der Domänennamenbezeichnung den FQDN für den Namen der benutzerdefinierten Domäne.        |
|type     | CNAME        | Verwenden Sie einen CNAME-Datensatz als Alias. Wenn für die Ressource eine IP-Adresse verwendet wurde, würde ein A-Eintrag verwendet werden.        |
|TTL     | 1        | 1 steht für 1 Stunde.        |
|TTL-Einheit     | Stunden        | Stunden werden als Maßeinheit für die Zeit verwendet.         |
|Alias     | webserver.azurewebsites.net        | Der DNS-Name, für den Sie den Alias erstellen, ist in diesem Beispiel der standardmäßig für die Web-App bereitgestellte DNS-Name „webserver.azurewebsites.net“.        |


![Erstellen eines CNAME-Datensatzes](./media/dns-custom-domain/createcnamerecord.png)

Navigieren Sie wieder zum App-Dienst, der für den Namen der benutzerdefinierten Domäne konfiguriert ist. Klicken Sie auf **Benutzerdefinierte Domänen** und dann auf **Hostnamen**. Um den erstellten CNAME-Datensatz hinzuzufügen, klicken Sie auf **+ Hostnamen hinzufügen**.

![Screenshot, auf dem die Schaltfläche „+ Hostnamen hinzufügen“ hervorgehoben ist.](./media/dns-custom-domain/figure1.png)

Nachdem der Vorgang durchgeführt wurde, führen Sie **nslookup** aus, um sicherzustellen, dass die Namensauflösung funktioniert.

![Abbildung 1](./media/dns-custom-domain/finalnslookup.png)

Weitere Informationen, wie Sie eine benutzerdefinierte Domäne zu App Service zuordnen, finden Sie unter [Zuordnen eines vorhandenen benutzerdefinierten DNS-Namens zu Azure-Web-Apps](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Weitere Informationen zum Migrieren eines aktiven DNS-Namens finden Sie unter [Migrieren eines aktiven DNS-Namens zu Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).

Wenn Sie eine benutzerdefinierte Domäne erwerben möchten, lesen Sie die Informationen zu App Service-Domänen unter [Erwerben eines Namens für eine benutzerdefinierte Domäne für Azure-Web-Apps](../app-service/manage-custom-dns-buy-domain.md).

## <a name="blob-storage"></a>Blob Storage

Die folgenden Schritte führen Sie durch die Konfiguration eines CNAME-Datensatzes für ein Blob Storage-Konto mit der asverify-Methode. Durch diese Methode wird sichergestellt, dass keine Ausfallzeiten auftreten.

Navigieren Sie zu **Speicher** > **Speicherkonten** , wählen Sie Ihr Speicherkonto aus, und klicken Sie auf **Benutzerdefinierte Domäne**. Notieren Sie sich den FQDN unter Schritt 2. Dieser Wert wird verwendet, um den ersten CNAME-Datensatz zu erstellen.

![Benutzerdefinierte Blob Storage-Domäne](./media/dns-custom-domain/blobcustomdomain.png)

Navigieren Sie zu Ihrer DNS-Zone, und klicken Sie auf **+ Datensatzgruppe**. Füllen Sie auf dem Blatt **Datensatzgruppe hinzufügen** die folgenden Informationen aus, und klicken Sie zum Erstellen auf **OK**.


|Eigenschaft  |Wert  |BESCHREIBUNG  |
|---------|---------|---------|
|Name     | asverify.mystorageaccount        | Dieser Wert bildet zusammen mit der Domänennamenbezeichnung den FQDN für den Namen der benutzerdefinierten Domäne.        |
|type     | CNAME        | Verwenden Sie einen CNAME-Datensatz als Alias.        |
|TTL     | 1        | 1 steht für 1 Stunde.        |
|TTL-Einheit     | Stunden        | Stunden werden als Maßeinheit für die Zeit verwendet.         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | Der DNS-Name, für den Sie den Alias erstellen, ist in diesem Beispiel der standardmäßig für das Speicherkonto bereitgestellte DNS-Name „asverify.adatumfunctiona9ed.blob.core.windows.net“.        |

Navigieren Sie wieder zu Ihrem Speicherkonto, indem Sie auf **Speicher** > **Speicherkonten** klicken, Ihr Speicherkonto auswählen und auf **Benutzerdefinierte Domäne** klicken. Geben Sie den von Ihnen erstellten Alias ohne das Präfix „asverify“ in das Textfeld ein, aktivieren Sie das Kontrollkästchen **Indirekte CNAME-Überprüfung verwenden** , und klicken Sie auf **Speichern**. Kehren Sie nach diesem Schritt zu Ihrer DNS-Zone zurück, und erstellen Sie einen CNAME-Datensatz ohne das Präfix „asverify“.  Nach diesem Schritt können Sie den CNAME-Datensatz mit dem Präfix „cdnverify“ bedenkenlos löschen.

![Screenshot der Seite „Benutzerdefinierte Domänen“.](./media/dns-custom-domain/indirectvalidate.png)

Überprüfen Sie die DNS-Auflösung, indem Sie `nslookup` ausführen.

Weitere Informationen zum Zuordnen einer benutzerdefinierten Domäne zu einem Blob Storage-Endpunkt finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für Ihren Blob Storage-Endpunkt](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json).

## <a name="azure-cdn"></a>Azure CDN

Die folgenden Schritte führen Sie durch die Konfiguration eines CNAME-Datensatzes für einen CDN-Endpunkt mit der cdnverify-Methode. Durch diese Methode wird sichergestellt, dass keine Ausfallzeiten auftreten.

Navigieren Sie zu **Netzwerk** > **CDN-Profile** , und wählen Sie Ihr CDN-Profil aus.

Wählen Sie den Endpunkt aus, mit dem Sie arbeiten, und klicken Sie auf **+ Benutzerdefinierte Domäne**. Beachten Sie den **Endpunkthostnamen** , da dieser Wert der Datensatz ist, auf den der CNAME-Datensatz zeigt.

![Benutzerdefinierte CDN-Domäne](./media/dns-custom-domain/endpointcustomdomain.png)

Navigieren Sie zu Ihrer DNS-Zone, und klicken Sie auf **+ Datensatzgruppe**. Füllen Sie auf dem Blatt **Datensatzgruppe hinzufügen** die folgenden Informationen aus, und klicken Sie zum Erstellen auf **OK**.

|Eigenschaft  |Wert  |BESCHREIBUNG  |
|---------|---------|---------|
|Name     | cdnverify.mycdnendpoint        | Dieser Wert bildet zusammen mit der Domänennamenbezeichnung den FQDN für den Namen der benutzerdefinierten Domäne.        |
|type     | CNAME        | Verwenden Sie einen CNAME-Datensatz als Alias.        |
|TTL     | 1        | 1 steht für 1 Stunde.        |
|TTL-Einheit     | Stunden        | Stunden werden als Maßeinheit für die Zeit verwendet.         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | Der DNS-Name, für den Sie den Alias erstellen, ist in diesem Beispiel der standardmäßig für das Speicherkonto bereitgestellte DNS-Name „cdnverify.adatumcdnendpoint.azureedge.net“.        |

Navigieren Sie wieder zum CDN-Endpunkt, indem Sie auf **Netzwerk** > **CDN-Profil** klicken, und wählen Sie Ihr CDN-Profil aus. Klicken Sie auf **+ Benutzerdefinierte Domäne** , und geben Sie den Alias des CNAME-Datensatzes ohne das Präfix „cdnverify“. Klicken Sie dann auf **Hinzufügen**.

Kehren Sie nach diesem Schritt zu Ihrer DNS-Zone zurück, und erstellen Sie einen CNAME-Datensatz ohne das Präfix „cdnverify“.  Nach diesem Schritt können Sie den CNAME-Datensatz mit dem Präfix „cdnverify“ bedenkenlos löschen. Weitere Informationen über CDN und die Konfiguration einer benutzerdefinierten Domäne ohne den zwischengeschalteten Registrierungsschritt finden Sie unter [Zuordnen von Azure CDN-Inhalten zu einer benutzerdefinierten Domäne](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Reverse-DNS-Lookups für in Azure gehostete Dienste konfigurieren](dns-reverse-dns-for-azure-services.md).
