---
title: Vorbereiten auf eine Änderung der SSL-IP-Adresse – Azure App Service
description: Erfahren Sie, wie Sie vorgehen, wenn Ihre SSL-IP-Adresse geändert werden soll, damit Ihre App nach der Änderung weiterhin funktioniert.
services: app-service\web
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 6c8c86ff6212acc31e961d6ae62836ca2b7b7380
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61268899"
---
# <a name="how-to-prepare-for-an-ssl-ip-address-change"></a>Vorbereitung auf eine Änderung der SSL-IP-Adresse

Wenn Sie eine Benachrichtigung erhalten haben, dass die SSL-IP-Adresse Ihrer Azure App Service-App geändert wird, befolgen Sie die Anweisungen in diesem Artikel, um die vorhandene SSL-IP-Adresse freizugeben und eine neue zuzuweisen.

## <a name="release-ssl-ip-addresses-and-assign-new-ones"></a>Freigeben von SSL-IP-Adressen und Zuweisen neuer SSL-IP-Adressen

1.  Öffnen Sie das [Azure-Portal](https://portal.azure.com).

2.  Wählen Sie im linken Navigationsmenü die Option **App Services** aus.

3.  Wählen Sie Ihre App Service-App in der Liste aus.

4.  Klicken Sie unter der Überschrift **Einstellungen** im linken Navigationsbereich auf **SSL-Einstellungen**.

1. Wählen Sie im Abschnitt „SSL-Bindungen“ den Eintrag für den Hostnamen aus. Wählen Sie in dem geöffneten Editor im Dropdownmenü **SSL-Typ** die Option **SNI SSL** aus, und klicken Sie auf **Bindung hinzufügen**. Wenn die Meldung zum erfolgreich durchgeführten Vorgang angezeigt wird, wurde die vorhandene IP-Adresse freigegeben.

6.  Wählen Sie im Abschnitt **SSL-Bindungen** wieder den gleichen Eintrag für den Hostnamen mit dem Zertifikat aus. Wählen Sie nun im Editor im Dropdownmenü **SSL-Typ** die Option **IP-basierte SSL** aus, und klicken Sie auf **Bindung hinzufügen**. Wenn die Meldung zum erfolgreich durchgeführten Vorgang angezeigt wird, haben Sie eine neue IP-Adresse abgerufen.

7.  Wenn in Ihrem Domänenregistrierungsportal (DNS-Drittanbieter oder Azure DNS) ein A-Datensatz (DNS-Eintrag, der direkt auf Ihre IP-Adresse verweist) konfiguriert ist, ersetzen Sie die vorhandene IP-Adresse durch die neu generierte IP-Adresse. Die neue IP-Adresse können Sie anhand der Anweisungen im nächsten Abschnitt suchen.

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Suchen der neuen SSL-IP-Adresse im Azure-Portal

1.  Warten Sie einige Minuten, und öffnen Sie dann das [Azure-Portal](https://portal.azure.com).

2.  Wählen Sie im linken Navigationsmenü die Option **App Services** aus.

3.  Wählen Sie Ihre App Service-App in der Liste aus.

4.  Klicken Sie unter der Überschrift **Einstellungen** im linken Navigationsbereich auf **Eigenschaften**, und suchen Sie den Abschnitt mit der Bezeichnung **Virtuelle IP-Adresse**.

5. Kopieren Sie die IP-Adresse, und kopieren Sie den Domäneneintrag oder IP-Mechanismus neu.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde erläutert, wie Sie sich auf eine Änderung der IP-Adresse vorbereiten, die von Azure veranlasst wurde. Weitere Informationen zu IP-Adressen in Azure App Service finden Sie unter [SSL und SSL-IP-Adressen in Azure App Service](overview-inbound-outbound-ips.md).
