---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: d001d76bca5b9a0837349b6e05b3b0a271ea3a73
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177905"
---
> [!NOTE]
> Mithilfe von Azure DNS können Sie einen benutzerdefinierten DNS-Namen für Ihre Azure-Web-Apps erstellen. Weitere Informationen finden Sie unter [Bereitstellen von benutzerdefinierten Domäneneinstellungen für einen Azure-Dienst mit Azure DNS](../articles/dns/dns-custom-domain.md#app-service-web-apps).
>
>

Melden Sie sich bei der Website Ihres Domänenanbieters an.

Suchen Sie die Seite für die Verwaltung von DNS-Einträgen. Da jeder Domänenanbieter eine eigene Benutzeroberfläche für DNS-Einträge hat, finden Sie Informationen dazu jeweils in der Dokumentation des Anbieters. Suchen Sie nach Bereichen der Website, die mit **Domänenname**, **DNS** oder **Namenserververwaltung** gekennzeichnet sind. 

Die Seite für DNS-Einträge erreichen Sie häufig in Ihren Kontoinformationen über einen Link wie **Eigene Domänen**. Navigieren Sie auf diese Seite, und suchen Sie nach einem Link, der beispielsweise den Namen **Zonendatei**, **DNS-Einträge** oder **Erweiterte Konfiguration** hat.

Der folgende Screenshot zeigt ein Beispiel für eine Seite mit DNS-Einträgen:

![Beispielseite mit DNS-Einträgen](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

Im Beispielscreenshot würden Sie die Option **Add** (Hinzufügen) wählen, um einen Eintrag zu erstellen. Einige Anbieter verfügen über unterschiedliche Links, um unterschiedliche Arten von Einträgen hinzuzufügen. Informationen hierzu finden Sie ebenfalls in der Dokumentation des Anbieters.

> [!NOTE]
> Bei einigen Anbietern wie GoDaddy werden Änderungen an DNS-Einträgen erst wirksam, wenn Sie einen gesonderten Link **Änderungen speichern** wählen. 
