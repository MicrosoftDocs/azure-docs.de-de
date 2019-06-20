---
title: include file
description: include file
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 10/15/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: ecdd419331c88e712644851f9213861f882cf0f6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178251"
---
## <a name="prepare-your-web-app"></a>Vorbereiten Ihrer Web-App

Um ein benutzerdefiniertes SSL-Zertifikat (Drittanbieterzertifikat oder App Service-Zertifikat) an Ihre Web-App zu binden, muss Ihr [App Service-Plan](https://azure.microsoft.com/pricing/details/app-service/) den Tarif **Basic**, **Standard**, **Premium** oder **App Service (isoliert)** aufweisen. Stellen Sie in diesem Schritt sicher, dass sich Ihre Web-App im richtigen Tarif befindet.

### <a name="log-in-to-azure"></a>Anmelden an Azure

Öffnen Sie das [Azure-Portal](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Navigieren zu Ihrer Web-App

Klicken Sie im linken Menü auf **App Services** und anschließend auf den Namen Ihrer Web-App.

![Auswählen der Web-App](./media/app-service-ssl-prepare-app/select-app.png)

Sie befinden sich auf der Verwaltungsseite Ihrer Web-App.  

### <a name="check-the-pricing-tier"></a>Überprüfen des Tarifs

Scrollen Sie im linken Navigationsbereich auf der Seite Ihrer Web-App zum Abschnitt **Einstellungen**, und wählen Sie **Zentral hochskalieren (App Service-Plan)** aus.

![Menü „Zentral hochskalieren“](./media/app-service-ssl-prepare-app/scale-up-menu.png)

Vergewissern Sie sich, dass sich Ihre Web-App nicht im Tarif **F1** oder **D1** befindet. Der aktuelle Tarif Ihrer Web-App wird durch einen dunkelblauen Rahmen hervorgehoben.

![Überprüfen des Tarifs](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

Benutzerdefiniertes SSL wird im Tarif **F1** oder **D1** nicht unterstützt. Wenn Sie Ihren App Service-Plan zentral hochskalieren müssen, führen Sie die Schritte im nächsten Abschnitt aus. Schließen Sie andernfalls die Seite **Zentral hochskalieren**, und überspringen Sie den Abschnitt [Zentrales Hochskalieren Ihres App Service-Plans](#scale-up-your-app-service-plan).

### <a name="scale-up-your-app-service-plan"></a>Zentrales Hochskalieren Ihres App Service-Plans

Wählen Sie einen der kostenpflichtigen Tarife aus (**B1**, **B2**, **B3** oder einen beliebigen Tarif aus der Kategorie **Produktion**). Klicken Sie auf **Alle Optionen anzeigen**, um weitere Optionen anzuzeigen.

Klicken Sie auf **Anwenden**.

![Auswählen eines Tarifs](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

Wenn die unten stehende Benachrichtigung angezeigt wird, ist der Skalierungsvorgang abgeschlossen.

![Benachrichtigung zum Hochskalieren](./media/app-service-ssl-prepare-app/scale-notification.png)

