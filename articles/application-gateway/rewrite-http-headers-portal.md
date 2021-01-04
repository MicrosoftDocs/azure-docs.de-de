---
title: Erneutes Generieren von HTTP-Anforderungs- und -Antwortheadern im Portal – Azure Application Gateway
description: Informationen zum Verwenden des Azure-Portals zur Konfiguration eines Azure Application Gateway, um die HTTP-Header in den Anforderungen und Antworten erneut zu generieren, die das Gateway durchlaufen
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 79314db13531f1fcf518c7931d4a1aa9158a172b
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397194"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Erneutes Generieren von HTTP-Anforderungs- und Antwortheadern mit Azure Application Gateway – Azure-Portal

In diesem Artikel erfahren Sie, wie Sie über das Azure-Portal eine [Application Gateway v2-SKU](./application-gateway-autoscaling-zone-redundant.md)-Instanz konfigurieren, um die HTTP-Header in Anforderungen und Antworten erneut zu generieren.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="before-you-begin"></a>Voraussetzungen

Sie benötigen eine Application Gateway v2-SKU-Instanz, um die Schritte in diesem Artikel durchzuführen. Das erneute Generieren von Headern wird in der v1-SKU nicht unterstützt. Wenn Sie nicht über die v2-SKU verfügen, erstellen Sie eine [Application Gateway v2-SKU](./tutorial-autoscale-ps.md)-Instanz, bevor Sie beginnen.

## <a name="create-required-objects"></a>Erstellen der erforderlichen Objekte

Um das erneute Generieren von HTTP-Headern zu konfigurieren, müssen Sie diese Schritte durchführen.

1. Erstellen Sie die Objekte, die zum erneuten Generieren von HTTP-Headern erforderlich sind:

   - **Aktion für das erneute Generieren** : Dient dazu, die Felder für Anforderung und Anforderungsheader, die Sie erneut generieren möchten, und den neuen Wert für die Header anzugeben. Sie können eine oder mehrere Bedingungen für das erneute Generieren mit einer Aktion für das erneute Generieren verknüpfen.

   - **Bedingung für das erneute Generieren** : Eine optionale Konfiguration. Bedingungen für das erneute Generieren werten den Inhalt von HTTP(S)-Anforderungen und -Antworten aus. Die Aktion für das erneute Generieren wird ausgeführt, wenn die HTTP(S)-Anforderung oder -Antwort die Bedingung für das erneute Generieren erfüllt.

     Wenn Sie der Aktion mehr als eine Bedingung zuordnen, erfolgt die Aktion nur, wenn alle Bedingungen erfüllt sind. Das heißt also, dass der Vorgang ein logischer UND-Vorgang ist.

   - **Regel zum erneuten Generieren** : Enthält mehrere Kombinationen aus Aktion und Bedingung für das erneute Generieren.

   - **Regelsequenz** : Hilft, die Reihenfolge zu bestimmen, in der die Regeln zum erneuten Generieren ausgeführt werden. Diese Konfiguration ist hilfreich, wenn Sie mehrere Regeln zum erneuten Generieren in einem Satz zum erneuten Generieren haben. Eine Regel zum erneuten Generieren, die eine niedrigere Regelsequenz besitzt, wird zuerst ausgeführt. Wenn Sie denselben Regelsequenzwert zwei Regeln zum erneuten Generieren zuweisen, ist die Reihenfolge der Ausführung unbestimmt.

   - **Satz zum erneuten Generieren** : Enthält mehrere Regeln zum erneuten Generieren, die einer Anforderungsroutingregel zugeordnet werden.

2. Fügen Sie den Satz für das erneute Generieren an eine Routingregel an. Die Konfiguration für das erneute Generieren wird dem Quelllistener über die Routingregel angefügt. Bei Verwendung einer einfachen Routingregel wird die Konfiguration der erneuten Generierung eines Headers einem Quelllistener zugeordnet und fungiert als erneute Generierung eines globalen Headers. Wenn eine pfadbasierte Routingregel verwendet wird, wird die Konfiguration der erneuten Generierung eines Headers in der URL-Pfadzuordnung definiert. In diesem Fall gilt sie nur für den bestimmten Pfadbereich einer Site.

Sie können mehrere solche Sätze für das erneute Generieren eines HTTP-Headers erstellen, und jeder dieser Sätze kann auf mehrere Listener angewendet werden. Allerdings können Sie auf einen bestimmten Listener nur einen Satz für das erneute Generieren anwenden.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="configure-header-rewrite"></a>Konfigurieren des erneuten Generierens für einen Header

In diesem Beispiel werden wir eine Umleitungs-URL ändern, indem der Adressheader in der HTTP-Antwort erneut generiert wird, die von einer Back-End-Anwendung gesendet wurde.

1. Wählen Sie die Option **Alle Ressourcen** und dann Ihr Anwendungsgateway aus.

2. Wählen Sie im linken Bereich **Umschreibungen** aus.

3. Wählen Sie **Umschreibungssatz** aus:

   ![Hinzufügen eines Satzes zum erneuten Generieren](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Geben Sie einen Namen für den Satz zum erneuten Generieren an, und ordnen Sie ihm eine Routingregel zu:

   - Geben Sie den Namen für den Satz zum erneuten Generieren in das Feld **Name** ein.
   - Wählen Sie eine oder mehrere Regeln aus, die in der Liste **Zugeordnete Routingregeln** aufgeführt sind. Sie können nur Regeln auswählen, die keinen anderen Sätzen zum erneuten Generieren zugeordnet sind. Die Regeln, die bereits anderen Sätzen zum erneuten Generieren zugeordnet sind, sind deaktiviert (abgeblendet).
   - Wählen Sie **Weiter** aus.
   
     ![Hinzufügen von Name und Zuordnung](media/rewrite-http-headers-portal/name-and-association.png)

5. Erstellen einer Regel zum erneuten Generieren:

   - Wählen Sie **Neuschreibungsregel hinzufügen** aus.

     ![Hinzufügen einer Regel zum erneuten Generieren](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Geben Sie einen Namen für die Regel zum erneuten Generieren in das Feld **Umschreibungsregelname** ein. Geben Sie eine Zahl in das Feld **Regelsequenz** ein.

     ![Hinzufügen eines Namens für eine Regel zum erneuten Generieren](media/rewrite-http-headers-portal/rule-name.png)

6. In diesem Beispiel wird der Adressheader nur dann erneut generiert, wenn er einen Verweis auf „azurewebsites.net“ enthält. Fügen Sie dazu eine Bedingung hinzu, um zu prüfen, ob der Adressheader in der Antwort „azurewebsites.net“ enthält:

   - Wählen Sie auf **Bedingung hinzufügen** und dann das Feld aus, das die **If** -Anweisungen enthält, um es zu erweitern.

     ![Bedingung hinzufügen](media/rewrite-http-headers-portal/add-condition.png)

   - Wählen Sie in der Liste **Typ der zu überprüfenden Variablen** den Eintrag **HTTP-Header** aus.

   - Wählen Sie in der Liste **Headertyp** den Eintrag **Antwort** aus.

   - Da in diesem Beispiel der Adressheader, der ein gängiger Header ist, ausgewertet wird, wählen Sie **Allgemeiner Header** unter **Headername** aus.

   - Wählen Sie in der Liste **Allgemeiner Header** den Eintrag **Adresse** aus.

   - Wählen Sie unter **Groß-/Kleinschreibung** den Eintrag **Keine** aus.

   - Wählen Sie in der Liste **Operator** den Eintrag **gleich (=)** aus.

   - Geben Sie ein reguläres Ausdrucksmuster ein. In diesem Beispiel verwenden wir das Muster `(https?):\/\/.*azurewebsites\.net(.*)$`.

   - Klicken Sie auf **OK**.

     ![Konfigurieren einer If-Bedingung](media/rewrite-http-headers-portal/condition.png)

7. Fügen Sie eine Aktion hinzu, um den Adressheader erneut zu generieren:

   - Wählen Sie in der Liste **Aktionstyp** den Eintrag **Satz** aus.

   - Wählen Sie in der Liste **Headertyp** den Eintrag **Antwort** aus.

   - Wählen Sie unter **Headername** den Eintrag **Allgemeiner Header** aus.

   - Wählen Sie in der Liste **Allgemeiner Header** den Eintrag **Adresse** aus.

   - Geben Sie den Headerwert ein. In diesem Beispiel verwenden wir `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` als Headerwert. Dieser Wert ersetzt im Adressheader *azurewebsites.net* durch *contoso.com*.

   - Klicken Sie auf **OK**.

     ![Hinzufügen einer Aktion](media/rewrite-http-headers-portal/action.png)

8. Wählen Sie **Erstellen** aus, um den Satz zum erneuten Generieren zu erstellen:

   ![Wählen Sie „Erstellen“ aus.](media/rewrite-http-headers-portal/create.png)

9. Die Umschreibungssatzansicht wird geöffnet. Vergewissern Sie sich, dass der von Ihnen erstellte Satz zum erneuten Generieren in der Liste der Sätze zum erneuten Generieren vorhanden ist:

   ![Umschreibungssatzansicht](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Einrichten einiger gängiger Anwendungsfälle finden Sie unter [Allgemeine Szenarien zum erneuten Generieren von Headern](./rewrite-http-headers.md).