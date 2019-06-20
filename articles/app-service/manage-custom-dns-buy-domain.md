---
title: Kaufen eines benutzerdefinierten Domänennamens in Azure – App Service
description: Erfahren Sie, wie Sie einen benutzerdefinierten Domänennamen für eine Web-App in Azure App Service kaufen.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 4b26446845d004b4dd92807c0b5ec0ada9ed181f
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475222"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Kaufen eines benutzerdefinierten Domänennamens für Azure App Service

App Service-Domänen sind Domänen der obersten Ebene, die direkt in Azure verwaltet werden. Sie erleichtern die Verwaltung von benutzerdefinierten Domänen für [Azure App Service](overview.md). In diesem Tutorial wird gezeigt, wie Sie eine App Service-Domäne kaufen und Azure App Service einen DNS-Namen zuweisen.

Informationen zu Azure VMs und Azure Storage finden Sie unter [Assign App Service domain to Azure VM or Azure Storage (Zuweisen einer App Service-Domäne zu einer Azure-VM oder Azure Storage)](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/31/assign-app-service-domain-to-azure-vm-or-azure-storage/). Informationen zu Clouddiensten finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für einen Azure-Clouddienst](../cloud-services/cloud-services-custom-domain-name-portal.md)

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* [Erstellen Sie eine App Service-App](/azure/app-service/), oder verwenden Sie eine App, die Sie für ein anderes Tutorial erstellt haben.
* [Entfernen Sie das Ausgabenlimit für Ihr Abonnement.](../billing/billing-spending-limit.md#remove) Sie können keine App Service-Domänen mit kostenlosem Abonnementguthaben erwerben.

## <a name="prepare-the-app"></a>Vorbereiten der App

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Um benutzerdefinierte Domänen in Azure App Service zu verwenden, muss der [App Service-Plan](https://azure.microsoft.com/pricing/details/app-service/) Ihrer App einen kostenpflichtigen Tarif (**Shared**, **Basic**, **Standard** oder **Premium**) aufweisen. Stellen Sie in diesem Schritt sicher, dass sich die App im richtigen Tarif befindet.

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

Öffnen Sie das [Azure-Portal](https://portal.azure.com), und melden Sie sich mit Ihrem Azure-Konto an.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navigieren zur App im Azure-Portal

Wählen Sie im linken Menü **App Services** und anschließend den Namen der App aus.

![Portalnavigation zur Azure-App](./media/app-service-web-tutorial-custom-domain/select-app.png)

Die Verwaltungsseite der App Service-App wird angezeigt.  

### <a name="check-the-pricing-tier"></a>Überprüfen des Tarifs

Scrollen Sie im linken Navigationsbereich der App-Seite zum Abschnitt **Einstellungen**, und wählen Sie **Zentral hochskalieren (App Service-Plan)** .

![Menü „Zentral hochskalieren“](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Der aktuelle Tarif der App wird durch einen blauen Rahmen hervorgehoben. Vergewissern Sie sich, dass sich die App nicht im Tarif **F1** befindet. Benutzerdefiniertes DNS wird im Tarif **F1** nicht unterstützt. 

![Überprüfen des Tarifs](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Wenn sich der App Service-Plan nicht im Tarif **F1** befindet, schließen Sie die Seite **Zentral hochskalieren**, und fahren Sie mit [Domäne kaufen](#buy-the-domain) fort.

### <a name="scale-up-the-app-service-plan"></a>Zentrales Hochskalieren des App Service-Plans

Wählen Sie einen der kostenpflichtigen Tarife aus (**D1**, **B1**, **B2**, **B3** oder einen beliebigen Tarif aus der Kategorie **Produktion**). Klicken Sie auf **Alle Optionen anzeigen**, um weitere Optionen anzuzeigen.

Klicken Sie auf **Anwenden**.

![Überprüfen des Tarifs](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Wenn die unten angegebene Benachrichtigung angezeigt wird, ist der Skalierungsvorgang abgeschlossen.

![Bestätigung des Skalierungsvorgangs](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Kaufen der Domäne

### <a name="pricing-information"></a>Preisinformationen
Preisinformationen zu Azure App Service-Domänen finden Sie auf der Seite [App Service – Preise](https://azure.microsoft.com/pricing/details/app-service/windows/) im Abschnitt „App Service-Domäne“.

### <a name="sign-in-to-azure"></a>Anmelden bei Azure
Öffnen Sie das [Azure-Portal](https://portal.azure.com/), und melden Sie sich mit Ihrem Azure-Konto an.

### <a name="launch-buy-domains"></a>Starten Sie „Domänen kaufen“
Klicken Sie auf der Registerkarte **App Services** auf den Namen der App, wählen Sie **Einstellungen** aus, und klicken Sie anschließend auf **Benutzerdefinierte Domänen**.
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Klicken Sie auf der Seite **Benutzerdefinierte Domänen** auf **Domäne erwerben**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Wenn der Abschnitt **App Service-Domänen** nicht angezeigt wird, müssen Sie das Ausgabenlimit für Ihr Azure-Konto entfernen (siehe [Voraussetzungen](#prerequisites)).
>
>

### <a name="configure-the-domain-purchase"></a>Konfigurieren des Domänenkaufs

Tippen Sie den Domänennamen auf der Seite **App Service Domain** (App Service-Domäne) in das Suchfeld **Search for domain** (Domäne suchen) ein und drücken Sie `Enter`. Die vorgeschlagenen verfügbaren Domänen werden direkt unter dem Textfeld angezeigt. Klicken Sie mindestens eine Domäne an, die Sie kaufen möchten.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> Die folgenden [Domänen der obersten Ebene](https://wikipedia.org/wiki/Top-level_domain) werden von App Service-Domänen unterstützt: _com_, _net_, _co.uk_, _org_, _nl_, _in_, _biz_, _org.uk_ und _co.in_.
>
>

Klicken Sie auf **Kontaktinformationen**, und füllen Sie das Formular mit den Kontaktinformationen für die Domäne aus. Wenn Sie fertig sind, klicken Sie auf **OK**, um zur Seite „App Service-Domäne“ zurückzukehren.

Es ist wichtig, dass Sie alle erforderlichen Felder so genau wie möglich ausfüllen. Fehlerhafte Daten in den Kontaktinformationen können dazu führen, dass der Domänenkauf fehlschlägt.

Wählen Sie als nächstes die gewünschten Optionen für Ihre Domäne aus. Die folgende Tabelle enthält einige Erläuterungen:

| Einstellung | Empfohlener Wert | BESCHREIBUNG |
|-|-|-|
|Datenschutz | Aktivieren | Abonnieren Sie „Datenschutz“. Diese Option ist _kostenlos_ im Kaufpreis enthalten. Manche Domänen der obersten Ebene werden von Registrierungsstellen verwaltet, die den Datenschutz nicht unterstützen. Sie sind auf der Seite **Datenschutz** aufgeführt. |
| Zuweisen von Standardhostnamen | **www** und **\@** | Wählen Sie bei Bedarf die gewünschten Hostnamenbindungen. Wenn der Vorgang des Domänenkaufs abgeschlossen ist, kann auf Ihre App über die ausgewählten Hostnamen zugegriffen werden. Wenn Ihre App sich hinter dem [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) befindet, wird Ihnen die Option nicht angezeigt, durch die die Stammdomäne (@) zugewiesen werden kann, da der Traffic Manager keine A-Datensätze unterstützt. Sie können Änderungen an den Zuweisungen der Hostnamen vornehmen, nachdem der Domänenkauf abgeschlossen ist. |

### <a name="accept-terms-and-purchase"></a>Akzeptieren der Bedingungen und Erwerben

Klicken Sie auf **Legal Terms** (Rechtliche Bedingungen), um die Bedingungen und die Gebühren anzuzeigen, und klicken Sie dann auf **Buy** (Kaufen).

> [!NOTE]
> App Service-Domänen verwenden GoDaddy zur Domänenregistrierung und Azure DNS zum Hosten der Domänen. Zusätzlich zu den Gebühren für die Domänenregistrierung fallen Nutzungsgebühren für Azure DNS an. Informationen finden Sie unter [Azure DNS Pricing (Azure DNS-Preisübersicht)](https://azure.microsoft.com/pricing/details/dns/).
>
>

Klicken Sie auf der Seite **App Service Domain** (App Service-Domäne) auf **OK**. Während der Vorgang ausgeführt wird, sehen Sie folgende Benachrichtigungen:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Testen des Hostnamens

Wenn Sie Ihrer App Standardhostnamen zugewiesen haben, wird Ihnen für jeden ausgewählten Hostnamen eine Erfolgsbenachrichtigung angezeigt.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

Auf der Seite **Benutzerdefinierte Domänen** werden Ihnen die ausgewählten Hostnamen im Abschnitt **Benutzerdefinierte Hostnamen** angezeigt.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

> [!NOTE]
> Die Bezeichnung **Nicht sicher** für Ihre benutzerdefinierte Domäne bedeutet, dass diese noch nicht an ein SSL-Zertifikat gebunden ist und dass für alle HTTPS-Anforderungen von einem Browser an Ihre benutzerdefinierte Domäne abhängig vom Browser eine Warnung oder ein Fehler angezeigt wird. Informationen zum Konfigurieren der SSL-Bindung finden Sie unter [Kaufen und Konfigurieren eines SSL-Zertifikats für Azure App Service](web-sites-purchase-ssl-web-site.md).
>

Um den Hostnamen zu testen, navigieren Sie zu den aufgelisteten Hostnamen im Browser. Versuchen Sie wie im Beispiel im vorstehenden Screenshot, zu den Seiten _kontoso.net_ und _www\.kontoso.net_ zu navigieren.

## <a name="assign-hostnames-to-app"></a>Zuweisen von Hostnamen zur App

Wenn Sie Ihrer App während des Kaufvorgangs nicht einen oder mehrere Standardhostnamen zuweisen möchten oder wenn Sie einen Hostnamen zuweisen müssen, der nicht aufgeführt ist, können Sie die Zuweisung auch zu jedem anderen Zeitpunkt durchführen.

In der App Service-Domäne können Sie auch jeder anderen App einen Hostnamen zuweisen. Die Schritte hängen davon ab, ob die App Service-Domäne und die App zum selben Abonnement gehören.

- Anderes Abonnement: Zuordnen benutzerdefinierter DNS-Datensätze (z.B. eine extern erworbene Domäne) von der App Service-Domäne zur App. Weitere Informationen zum Hinzufügen von benutzerdefinierten DNS-Namen zu einer App Service-Domäne finden Sie unter [Manage custom DNS records (Verwalten von benutzerdefinierten DNS-Datensätzen)](#custom). Weitere Informationen zum Zuordnen einer extern erworbenen Domäne zu einer App finden Sie unter [Zuweisen eines bestehenden benutzerdefinierten DNS-Namens zu Azure App Service](app-service-web-tutorial-custom-domain.md). 
- Gleiches Abonnement: Führen Sie die folgenden Schritte aus:

### <a name="launch-add-hostname"></a>Starten Sie „Hostname hinzufügen“
Wählen Sie auf der Seite **App Services** (App-Dienste) den Namen Ihrer App aus, der Sie Hostnamen zuweisen möchten, klicken Sie auf **Settings** (Einstellungen), und dann auf **Custom domains** (Benutzerdefinierte Domänen).

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Stellen Sie sicher, dass Ihre erworbene Domäne im Abschnitt**App Service Domains** (App Service-Domänen) aufgelistet ist, aber klicken Sie sie nicht an. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Alle App Service-Domänen, die zum selben Abonnement gehören, werden auf der Seite **Custom domains** (Benutzerdefinierte Domänen) in der App angezeigt. Wenn Ihre Domäne sich im Abonnement der App befindet, jedoch nicht auf der Seite **Custom domains** (Benutzerdefinierte Domänen) der App angezeigt wird, öffnen Sie die Seite **Custom domains** (Benutzerdefinierte Domänen) erneut oder aktualisieren Sie die Webseite. Überprüfen Sie auch die Anzeige der Benachrichtigungsglocke am oberen Rand des Azure-Portals auf Fortschritts- oder Erstellungsfehler.
>
>

Wählen Sie **Hostnamen hinzufügen**.

### <a name="configure-hostname"></a>Konfigurieren des Hostnamens
Tippen Sie im Dialogfeld**Add hostname** (Hostname hinzufügen) den vollqualifizierten Domänennamen Ihrer App Service-Domäne oder einer Unterdomäne ein. Beispiel:

- kontoso.net
- www\.kontoso.net
- abc.kontoso.net

Klicken Sie anschließend auf **Validate** (Überprüfen). Der Datensatztyp für Ihren Hostnamen wird automatisch für Sie ausgewählt.

Wählen Sie **Hostnamen hinzufügen**.

Wenn der Vorgang abgeschlossen ist, sehen Sie eine Erfolgsmeldung für den zugeordneten Hostnamen.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Schließen Sie „Hostname hinzufügen“
Auf der Seite **Add hostname** (Hostname hinzufügen) können Sie Ihrer App bei Bedarf einen beliebigen anderen Hostnamen zuweisen. Schließen Sie die Seite **Add hostname** (Hostname hinzufügen), wenn Sie fertig sind.

Der oder die neu zugewiesenen Hostname(n) sollten nun auf der Seite **Custom Domains** (Benutzerdefinierte Domänen) in Ihrer App angezeigt werden.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Testen des Hostnamens

Navigieren Sie zu den aufgelisteten Hostnamen im Browser. Versuchen Sie wie im Beispiel im obigen Screenshot, zu der Seite _abc.kontoso.net_ zu navigieren.

## <a name="renew-the-domain"></a>Verlängern der Domäne

Die erworbene App Service-Domäne ist ab dem Kaufdatum ein Jahr lang gültig. Die Domäne ist standardmäßig so konfiguriert, dass sie automatisch verlängert wird, indem Ihre Zahlungsmethode für das nächste Jahr belastet wird. Sie können die Gültigkeit Ihres Domänennamens manuell verlängern.

Wenn Sie die automatische Verlängerung deaktivieren oder die Domäne manuell verlängern möchten, führen Sie die nachfolgenden Schritte aus.

Klicken Sie auf der Registerkarte **App Services** auf den Namen der App, wählen Sie **Einstellungen** aus, und klicken Sie anschließend auf **Benutzerdefinierte Domänen**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Wählen Sie im Abschnitt **App Service-Domänen** die Domäne aus, die Sie konfigurieren möchten.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

Klicken Sie im linken Navigationsbereich der Domäne auf **Domänenverlängerung**. Wenn die Domäne nicht automatisch verlängert werden soll, klicken Sie auf **Aus** und dann auf **Speichern**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Wenn Sie Ihre Domäne manuell verlängern möchten, klicken Sie auf **Domäne verlängern**. Diese Schaltfläche wird aber erst [90 Tage vor Ablauf der Domäne](#when-domain-expires) aktiviert.

Wenn die Verlängerung Ihrer Domäne erfolgreich ist, erhalten Sie innerhalb von 24 Stunden eine E-Mail-Benachrichtigung.

## <a name="when-domain-expires"></a>Ablauf der Domäne

In Azure werden ablaufende bzw. abgelaufene App Service-Domänen wie folgt verarbeitet:

* Bei deaktivierter automatischer Verlängerung: 90 Tage vor Ablauf der Domäne erhalten Sie eine E-Mail mit einer Benachrichtigung über die Verlängerung, und die Schaltfläche **Domäne verlängern** ist im Portal aktiviert.
* Bei aktivierter automatischer Verlängerung: Am Tag nach dem Ablaufdatum Ihrer Domäne versucht Azure, Ihnen die Verlängerung des Domänennamens in Rechnung zu stellen.
* Wenn während der automatischen Verlängerung ein Fehler auftritt (z. B. bei Ablauf Ihrer hinterlegten Karte) oder die automatische Verlängerung deaktiviert ist und Sie das Ablaufen der Domäne zulassen, werden Sie von Azure über den Domänenablauf informiert, und Ihr Domänenname wird „geparkt“. Sie können Ihre Domäne [manuell verlängern](#renew-the-domain).
* Am 4. und 12. Tag nach dem Ablauf erhalten Sie von Azure weitere Benachrichtigungs-E-Mails. Sie können Ihre Domäne [manuell verlängern](#renew-the-domain).
* Am 19. Tag nach dem Ablauf bleibt Ihre Domäne weiterhin angehalten, unterliegt dann aber einer Einlösegebühr. Sie können sich telefonisch an den Kundensupport wenden, um Ihren Domänennamen zu verlängern. Hierfür gelten die jeweiligen Gebühren für die Verlängerung und Einlösung.
* Am 25. Tag nach dem Ablauf wird Ihre Domäne von Azure über einen Branchenauktionsdienst zur Auktion freigegeben. Sie können sich telefonisch an den Kundensupport wenden, um Ihren Domänennamen zu verlängern. Hierfür gelten die jeweiligen Gebühren für die Verlängerung und Einlösung.
* Ab dem 30. Tag nach dem Ablauf können Sie Ihre Domäne nicht mehr einlösen.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Verwalten von benutzerdefinierten DNS-Datensätzen

In Azure werden DNS-Datensätze für eine App Service-Domäne mithilfe von [Azure DNS](https://azure.microsoft.com/services/dns/) verwaltet. Sie können DNS-Datensätze hinzufügen, entfernen und aktualisieren, auch für eine extern erworbene Domäne.

### <a name="open-app-service-domain"></a>Open-App Service-Domäne

Klicken Sie im linken Menü des Azure-Portals auf **Alle Dienste** > **App Service-Domänen**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Wählen Sie die Domäne, die verwaltet werden soll. 

### <a name="access-dns-zone"></a>Zugreifen auf die DNS-Zone

Klicken Sie im linken Menü der Domäne auf **DNS zone** (DNS-Zone).

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Diese Aktion öffnet die Seite [DNS zone](../dns/dns-zones-records.md) (DNS-Zone) Ihrer App Service-Domäne in Azure DNS. Informationen zum Bearbeiten von DNS-Datensätzen finden Sie unter [How to manage DNS Zones in the Azure portal (Vorgehensweise: Verwalten von DNS-Zonen im Azure-Portal)](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Abbrechen des Kaufs (Domäne löschen)

Nach dem Kauf der App Service-Domäne haben Sie fünf Tage Zeit, um Ihren Kauf zu stornieren und eine vollständige Rückerstattung zu erhalten. Nach fünf Tagen können Sie die App Service-Domäne zwar löschen, erhalten jedoch keine Rückerstattung.

### <a name="open-app-service-domain"></a>Open-App Service-Domäne

Klicken Sie im linken Menü des Azure-Portals auf **Alle Dienste** > **App Service-Domänen**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Wählen Sie die Domäne aus, die Sie stornieren oder löschen möchten. 

### <a name="delete-hostname-bindings"></a>Löschen von Hostnamenbindungen

Klicken Sie im linken Menü der Domäne auf **Hostname bindings** (Hostnamenbindungen). Die Hostnamenbindungen aus allen Azure-Diensten werden hier aufgelistet.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Die App Service-Domäne kann nicht gelöscht werden, bis alle Hostnamenbindungen gelöscht wurden.

Löschen Sie jede Hostnamenbindung, indem Sie auf **...**  > **Delete** (Löschen) klicken. Nachdem alle Bindungen gelöscht worden sind, klicken Sie auf **Save** (Speichern).

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Stornieren oder Löschen

Klicken Sie im linken Menü der Domäne auf **Overview** (Übersicht). 

Wenn die Kündigungsfrist für die erworbene Domäne nicht abgelaufen ist, klicken Sie auf **Cancel purchase** (Kauf stornieren). Andernfalls sehen Sie stattdessen die Schaltfläche **Delete** (Löschen). Klicken Sie zum Löschen der Domäne ohne Rückerstattung auf **Delete** (Löschen).

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

Klicken Sie auf **Ja**, um den Vorgang zu bestätigen.

Nachdem der Vorgang abgeschlossen ist, ist die Domäne aus Ihrem Abonnement freigegeben und steht wieder für jeden zum Kauf zur Verfügung. 

## <a name="direct-default-url-to-a-custom-directory"></a>Weiterleiten der Standard-URL an ein benutzerdefiniertes Verzeichnis

App Service leitet Webanforderungen standardmäßig an das Stammverzeichnis des App-Codes weiter. Informationen zum Weiterleiten an ein Unterverzeichnis wie `public` finden Sie unter [Zuordnen eines vorhandenen benutzerdefinierten DNS-Namens zu Azure-Web-Apps](app-service-web-tutorial-custom-domain.md#virtualdir).
