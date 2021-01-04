---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 2f490a5b12484a91e963d068810b292d7761521a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95986541"
---
> [!NOTE]
> Mithilfe von Azure DNS können Sie einen benutzerdefinierten DNS-Namen für Azure App Service erstellen. Weitere Informationen finden Sie unter [Bereitstellen von benutzerdefinierten Domäneneinstellungen für einen Azure-Dienst mit Azure DNS](../articles/dns/dns-custom-domain.md#app-service-web-apps).
>
>

1. Melden Sie sich bei der Website Ihres Domänenanbieters an.

1. Suchen Sie die Seite für die Verwaltung von DNS-Einträgen. Da jeder Domänenanbieter eine eigene Benutzeroberfläche für DNS-Einträge hat, finden Sie Informationen dazu jeweils in der Dokumentation des Anbieters. Suchen Sie nach Bereichen der Website, die mit **Domänenname**, **DNS** oder **Namenserververwaltung** gekennzeichnet sind.

   Die Seite für DNS-Einträge erreichen Sie häufig in Ihren Kontoinformationen über einen Link wie **Eigene Domänen**. Navigieren Sie zu dieser Seite, und suchen Sie nach einem Link, der beispielsweise den Namen **Zonendatei**, **DNS-Einträge** oder **Erweiterte Konfiguration** hat.

   Der folgende Screenshot zeigt ein Beispiel für eine Seite mit DNS-Einträgen:

   ![Screenshot: Beispiel für eine Seite mit DNS-Einträgen](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. Auf dem Beispielscreenshot wählen Sie die Option **Add** (Hinzufügen) aus, um einen Eintrag zu erstellen. Einige Anbieter verfügen über unterschiedliche Links, um unterschiedliche Arten von Einträgen hinzuzufügen. Informationen hierzu finden Sie ebenfalls in der Dokumentation des Anbieters.

> [!NOTE]
> Bei einigen Anbietern wie GoDaddy werden Änderungen an DNS-Einträgen erst wirksam, wenn Sie einen gesonderten Link **Änderungen speichern** wählen.
