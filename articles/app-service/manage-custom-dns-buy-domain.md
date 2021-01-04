---
title: Erwerben eines benutzerdefinierten Domänennamens
description: Erfahren Sie, wie Sie eine App Service-Domäne erwerben und als benutzerdefinierte Domäne für Ihre App in Azure App Service verwenden können.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/30/2020
ms.custom: seodec18
ms.openlocfilehash: 6f0ff7a54c2ad1fa1af649c8082498b442783c7e
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608076"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Kaufen eines benutzerdefinierten Domänennamens für Azure App Service

App Service-Domänen sind benutzerdefinierte Domänen, die direkt in Azure verwaltet werden. Sie erleichtern die Verwaltung von benutzerdefinierten Domänen für [Azure App Service](overview.md). In diesem Tutorial wird gezeigt, wie Sie eine App Service-Domäne kaufen und Azure App Service einen DNS-Namen zuweisen.

Informationen zu Azure VMs und Azure Storage finden Sie unter [Assign App Service domain to Azure VM or Azure Storage (Zuweisen einer App Service-Domäne zu einer Azure-VM oder Azure Storage)](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage). Informationen zu Clouddiensten finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für einen Azure-Clouddienst](../cloud-services/cloud-services-custom-domain-name-portal.md)

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* [Erstellen Sie eine App Service-App](./index.yml), oder verwenden Sie eine App, die Sie für ein anderes Tutorial erstellt haben. Die App muss sich in einer öffentlichen Azure-Region befinden. Nationale Azure-Clouds werden derzeit nicht unterstützt.
* [Entfernen Sie das Ausgabenlimit für Ihr Abonnement.](../cost-management-billing/manage/spending-limit.md#remove) Sie können keine App Service-Domänen mit kostenlosem Abonnementguthaben erwerben.

## <a name="buy-an-app-service-domain"></a>Erwerben einer App Service-Domäne

Preisinformationen zu App Service-Domänen finden Sie auf der Seite [App Service – Preise](https://azure.microsoft.com/pricing/details/app-service/windows/) im Abschnitt „App Service-Domäne“.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und melden Sie sich mit Ihrem Azure-Konto an.

1. Suchen Sie auf der Suchleiste nach **App Service-Domänen**, und wählen Sie die Option aus.

    ![Navigation im Portal zu Azure App Service-Domänen](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Klicken Sie in der Ansicht **App Service-Domänen** auf **Hinzufügen**.

    ![Klicken auf „Hinzufügen“ in „App Service-Domänen“](./media/app-service-web-tutorial-custom-domain/add-app-service-domain.png)

1. Wählen Sie **Klicken Sie, um die neuere Version der Erstellungsoberfläche für App Service-Domänen auszuprobieren** aus.

    ![Erstellen einer App Service-Domäne mit neuer Oberfläche](./media/app-service-web-tutorial-custom-domain/select-new-create-experience.png)

### <a name="basics-tab"></a>Registerkarte „Grundlagen“

1. Konfigurieren Sie auf der Registerkarte **Grundlagen** die Einstellungen anhand der folgenden Tabelle:  

   | Einstellung  | BESCHREIBUNG |
   | -------- | ----------- |
   | **Abonnement** | Das Abonnement, das für den Kauf der Domäne verwendet werden soll. |
   | **Ressourcengruppe** | Die Ressourcengruppe, in der die Domäne eingefügt werden soll. Beispielsweise die Ressourcengruppe, in der sich Ihre App befindet. |
   | **Domäne** | Geben Sie die gewünschte Domäne ein. Beispiel: **contoso.com**. Wenn die gewünschte Domäne nicht verfügbar ist, können Sie eine Domäne in einer Liste mit Vorschlägen für verfügbare Domänen auswählen oder eine andere Domäne angeben. |

    > [!NOTE]
    > Die folgenden [Domänen der obersten Ebene](https://wikipedia.org/wiki/Top-level_domain) werden von App Service-Domänen unterstützt: _com_, _net_, _co.uk_, _org_, _nl_, _in_, _biz_, _org.uk_ und _co.in_.
    >
    >
    
2. Klicken Sie abschließend auf **Weiter: Kontaktinformationen**.

### <a name="contact-information-tab"></a>Registerkarte „Kontaktinformationen“

1. Geben Sie Ihre Informationen entsprechend den Anforderungen von [ICANN](https://go.microsoft.com/fwlink/?linkid=2116641) für die Domänenregistrierung an. 

    Es ist wichtig, dass Sie alle erforderlichen Felder so genau wie möglich ausfüllen. Fehlerhafte Daten in den Kontaktinformationen können dazu führen, dass der Domänenkauf nicht zustande kommt.

1. Klicken Sie abschließend auf **Weiter: Erweitert** aus.

### <a name="advanced-tab"></a>Registerkarte „Erweitert“

1. Konfigurieren Sie auf der Registerkarte **Erweitert** die optionalen Einstellungen:  

   | Einstellung  | BESCHREIBUNG |
   | -------- | ----------- |
   | **Automatische Verlängerung** | Standardmäßig aktiviert. Ihre App Service-Domäne wird jeweils für ein Jahr für Sie registriert. Durch „Automatische Verlängerung“ wird sichergestellt, dass die Domänenregistrierung nicht abläuft und Sie Besitzer der Domäne bleiben. Für Ihr Azure-Abonnement wird zum Zeitpunkt der Verlängerung automatisch die Jahresgebühr für die Domänenregistrierung in Rechnung gestellt. Wählen Sie **Deaktivieren** aus, um die Option abzuwählen. Wenn die automatische Verlängerung deaktiviert ist, können Sie [die Domäne manuell verlängern](#renew-the-domain). |
   | **Datenschutz** | Standardmäßig aktiviert. Über „Datenschutz“ werden Ihre Kontaktinformationen für die Domänenregistrierung in der WHOIS-Datenbank ausgeblendet. Der Datenschutz ist bereits in der Jahresgebühr für die Domänenregistrierung enthalten. Wählen Sie **Deaktivieren** aus, um die Option abzuwählen. |

2. Klicken Sie abschließend auf **Weiter: Tags**.

### <a name="finish"></a>Finish

1. Legen Sie auf der Registerkarte **Tags** die für die App Service-Domäne gewünschten Tags fest. Tagging ist zur Verwendung von App Service-Domänen nicht erforderlich, bietet jedoch eine [Funktion in Azure zum Verwalten von Ressourcen](../azure-resource-manager/management/tag-resources.md).

1. Klicken Sie auf **Weiter: Überprüfen + erstellen**.

1. Überprüfen Sie auf der Registerkarte **Überprüfen + erstellen** die Domänenbestellung. Klicken Sie auf **Erstellen**, wenn Sie fertig sind.

    > [!NOTE]
    > App Service-Domänen verwenden GoDaddy zur Domänenregistrierung und Azure DNS zum Hosten der Domänen. Zusätzlich zu der Jahresgebühr für die Domänenregistrierung fallen Nutzungsgebühren für Azure DNS an. Informationen finden Sie unter [Azure DNS Pricing (Azure DNS-Preisübersicht)](https://azure.microsoft.com/pricing/details/dns/).
    >
    >

1. Wenn die Domänenregistrierung abgeschlossen ist, wird die Schaltfläche **Zu Ressource wechseln** angezeigt. Wählen Sie sie aus, um die Verwaltungsseite anzuzeigen.

    ![App Service-Domäne erstellt Zu Ressource wechseln](./media/app-service-web-tutorial-custom-domain/deployment-complete.png)

Nun können Sie dieser benutzerdefinierten Domäne eine App Service-App zuweisen.

## <a name="prepare-the-app"></a>Vorbereiten der App

Um einer Web-App einen benutzerdefinierten DNS-Namen zuzuordnen, muss der [App Service-Plan](https://azure.microsoft.com/pricing/details/app-service/) der Web-App einen kostenpflichtigen Tarif („Shared“, „Basic“, „Standard“, „Premium“ oder „Consumption“ für Azure Functions) aufweisen. In diesem Schritt stellen Sie sicher, dass sich die App Service-App innerhalb des unterstützten Tarifs befindet.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navigieren zur App im Azure-Portal

1. Suchen Sie oben auf der Suchleiste nach **App Services**, und wählen Sie die Option aus.

    ![Suchen nach App Services](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Wählen Sie den Namen der App aus.

    ![Portalnavigation zur Azure-App](./media/app-service-web-tutorial-custom-domain/select-app.png)

    Die Verwaltungsseite der App Service-App wird angezeigt.  

### <a name="check-the-pricing-tier"></a>Überprüfen des Tarifs

1. Scrollen Sie im linken Navigationsbereich der App-Seite zum Abschnitt **Einstellungen**, und wählen Sie **Hochskalieren (App Service-Plan)** .

    ![Menü „Zentral hochskalieren“](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Der aktuelle Tarif der App wird durch einen blauen Rahmen hervorgehoben. Vergewissern Sie sich, dass sich die App nicht im Tarif **F1** befindet. Benutzerdefiniertes DNS wird im Tarif **F1** nicht unterstützt. 

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png" alt-text="Screenshot: Linkes Navigationsmenü der App-Seite, auf der „Zentral hochskalieren (App Service-Plan)“ hervorgehoben ist":::

1. Wenn sich der App Service-Plan nicht im Tarif **F1** befindet, schließen Sie die Seite **Hochskalieren**, und fahren Sie mit [Domäne kaufen](#buy-an-app-service-domain) fort.

### <a name="scale-up-the-app-service-plan"></a>Hochskalieren des App Service-Plans

1. Wählen Sie einen der kostenpflichtigen Tarife aus (**D1**, **B1**, **B2**, **B3** oder einen beliebigen Tarif aus der Kategorie **Produktion**). Klicken Sie auf **Alle Optionen anzeigen**, um weitere Optionen anzuzeigen.

1. Klicken Sie auf **Anwenden**.

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png" alt-text="Screenshot: Tarife für benutzerdefinierte Domäne in der Kategorie „Produktion“, in der die Registerkarte „Produktion“, der Plan „B1“ und die Schaltfläche „Anwenden“ hervorgehoben sind":::

    Wenn die unten angegebene Benachrichtigung angezeigt wird, ist der Skalierungsvorgang abgeschlossen.

    ![Bestätigung des Skalierungsvorgangs](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="map-app-service-domain-to-your-app"></a>Zuordnen der App Service-Domäne zu Ihrer App

Ein Hostname in Ihrer App Service-Domäne lässt sich einfach zu einer App Service-App zuordnen, solange er sich im gleichen Abonnement befindet. Sie ordnen die App Service-Domäne oder eine ihrer Unterdomänen direkt in Ihrer App zu. In Azure werden dann die erforderlichen DNS-Einträge erstellt.

> [!NOTE]
> Wenn sich die Domäne und die App in unterschiedlichen Abonnements befinden, ordnen Sie die App Service-Domäne genauso der App zu wie Sie [eine extern erworbene Domäne zuordnen](app-service-web-tutorial-custom-domain.md). In diesem Fall ist Azure DNS der externe Domänenanbieter. Sie müssen daher [die erforderlichen DNS-Einträge manuell hinzufügen](#manage-custom-dns-records).
>

### <a name="map-the-domain"></a>Zuordnen der Domäne

1. Scrollen Sie im linken Navigationsbereich der App-Seite zum Abschnitt **Einstellungen**, und wählen Sie **Benutzerdefinierte Domänen** aus.

    ![Screenshot: Menü „Benutzerdefinierte Domänen“](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Wählen Sie **Benutzerdefinierte Domäne hinzufügen**.

    ![Screenshot: Element zum Hinzufügen des Hostnamens](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Geben Sie die App Service-Domäne (z. B. **contoso.com**) oder eine Unterdomäne (z. B. **www.contoso.com**) ein, und klicken Sie auf **Überprüfen**.

    > [!NOTE]
    > Bei einem Tippfehler im Namen der App Service-Domäne wird unten auf der Seite ein Überprüfungsfehler angezeigt, in dem angegeben wird, dass einige DNS-Einträge fehlen. Sie müssen diese Einträge für eine App Service-Domäne nicht manuell hinzufügen. Geben Sie den Namen der Domäne richtig ein, und klicken Sie dann erneut auf **Überprüfen**.
    >
    > ![Screenshot: Überprüfungsfehler](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

1. Übernehmen Sie den **Typ des Hostnamenseintrags**, und klicken Sie auf **Benutzerdefinierte Domäne hinzufügen**.

    ![Screenshot: Schaltfläche „Benutzerdefinierte Domäne hinzufügen“](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

1. Unter Umständen dauert es eine Weile, bis die neue benutzerdefinierte Domäne auf der Seite **Benutzerdefinierte Domänen** der App angezeigt wird. Aktualisieren Sie den Browser, um die Daten zu aktualisieren.

    ![Screenshot: Hinzufügen des CNAME-Eintrags](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > Wenn Ihre benutzerdefinierte Domäne mit **Nicht sicher** gekennzeichnet ist, wurde sie noch nicht an ein TLS/SSL-Zertifikat gebunden. Für alle HTTPS-Anforderungen von einem Browser an Ihre benutzerdefinierte Domäne wird abhängig vom Browser ein Fehler oder eine Warnung angezeigt. Weitere Informationen zum Hinzufügen einer TLS-/SSL-Bindung finden Sie unter [Schützen eines benutzerdefinierten DNS-Namens mit einer TLS-/SSL-Bindung in Azure App Service](configure-ssl-bindings.md).
    
### <a name="test-the-custom-domain"></a>Testen der benutzerdefinierten Domäne

Navigieren Sie im Browser zu der benutzerdefinierten Domäne, um sie zu testen.

## <a name="renew-the-domain"></a>Verlängern der Domäne

Die erworbene App Service-Domäne ist ab dem Kaufdatum ein Jahr lang gültig. Die Domäne ist standardmäßig so konfiguriert, dass sie automatisch verlängert wird, indem Ihre Zahlungsmethode für das nächste Jahr belastet wird. Sie können die Gültigkeit Ihres Domänennamens manuell verlängern.

Wenn Sie die automatische Verlängerung deaktivieren oder die Domäne manuell verlängern möchten, führen Sie die nachfolgenden Schritte aus.

1. Suchen Sie auf der Suchleiste nach **App Service-Domänen**, und wählen Sie die Option aus.

    ![Navigation im Portal zu Azure App Service-Domänen](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Wählen Sie im Abschnitt **App Service-Domänen** die Domäne aus, die Sie konfigurieren möchten.

1. Klicken Sie im linken Navigationsbereich der Domäne auf **Domänenverlängerung**. Wenn die Domäne nicht automatisch verlängert werden soll, wählen Sie **Aus** aus. Die Einstellung wird sofort wirksam.

    ![Screenshot der Option zum automatischen Verlängern Ihrer Domäne](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

    > [!NOTE]
    > Ignorieren Sie beim Verlassen der Seite den Fehler „Nicht gespeicherte Änderungen werden verworfen“, indem Sie auf **OK** klicken.
    >

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

In Azure werden DNS-Datensätze für eine App Service-Domäne mithilfe von [Azure DNS](https://azure.microsoft.com/services/dns/) verwaltet. Sie können DNS-Datensätze hinzufügen, entfernen und aktualisieren, auch für eine extern erworbene Domäne. So verwalten Sie benutzerdefinierte DNS-Einträge

1. Suchen Sie auf der Suchleiste nach **App Service-Domänen**, und wählen Sie die Option aus.

    ![Navigation im Portal zu Azure App Service-Domänen](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Wählen Sie im Abschnitt **App Service-Domänen** die Domäne aus, die Sie konfigurieren möchten.

1. Wählen Sie auf der Seite **Übersicht** die Option **DNS-Einträge verwalten** aus.

    ![Screenshot des Zugriffs auf DNS-Einträge](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Informationen zum Bearbeiten von DNS-Datensätzen finden Sie unter [How to manage DNS Zones in the Azure portal (Vorgehensweise: Verwalten von DNS-Zonen im Azure-Portal)](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Abbrechen des Kaufs (Domäne löschen)

Nach dem Kauf der App Service-Domäne haben Sie fünf Tage Zeit, um Ihren Kauf zu stornieren und eine vollständige Rückerstattung zu erhalten. Nach fünf Tagen können Sie die App Service-Domäne zwar löschen, erhalten jedoch keine Rückerstattung.

1. Suchen Sie auf der Suchleiste nach **App Service-Domänen**, und wählen Sie die Option aus.

    ![Navigation im Portal zu Azure App Service-Domänen](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Wählen Sie im Abschnitt **App Service-Domänen** die Domäne aus, die Sie konfigurieren möchten.

1. Wählen Sie im linken Navigationsbereich der Domäne die Option **Hostnamenbindungen** aus. Die Hostnamenbindungen aus allen Azure-Diensten werden hier aufgelistet.

    ![Screenshot der Seite „Hostnamenbindungen“](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

1. Löschen Sie jede Hostnamenbindung, indem Sie auf **...**  > **Delete** (Löschen) klicken. Nachdem alle Bindungen gelöscht worden sind, klicken Sie auf **Save** (Speichern).

    <!-- ![Screenshot that shows where to delete the hostname bindings.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png) -->

1. Wählen Sie im linken Navigationsbereich die Option **Übersicht** aus. 

1. Wenn die Kündigungsfrist für die erworbene Domäne nicht abgelaufen ist, klicken Sie auf **Cancel purchase** (Kauf stornieren). Andernfalls sehen Sie stattdessen die Schaltfläche **Delete** (Löschen). Klicken Sie zum Löschen der Domäne ohne Rückerstattung auf **Delete** (Löschen).

    ![Screenshot, der zeigt, wo eine erworbene Domäne gelöscht oder der Kauf abgebrochen werden kann](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

1. Wählen Sie **Ja** aus, um den Vorgang zu bestätigen.

    Nachdem der Vorgang abgeschlossen ist, ist die Domäne aus Ihrem Abonnement freigegeben und steht wieder für jeden zum Kauf zur Verfügung. 

## <a name="direct-default-url-to-a-custom-directory"></a>Weiterleiten der Standard-URL an ein benutzerdefiniertes Verzeichnis

App Service leitet Webanforderungen standardmäßig an das Stammverzeichnis des App-Codes weiter. Informationen zur Weiterleitung an ein Unterverzeichnis wie `public` finden Sie unter [Umleitung zu einem benutzerdefinierten Verzeichnis](app-service-web-tutorial-custom-domain.md#redirect-to-a-custom-directory).

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich darüber, wie Sie ein benutzerdefiniertes TLS/SSL-Zertifikat an App Service binden.

> [!div class="nextstepaction"]
> [Schützen eines benutzerdefinierten DNS-Namens mit einer TLS-Bindung in Azure App Service](configure-ssl-bindings.md)
