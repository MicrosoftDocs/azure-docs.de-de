---
title: Testversion für Logik-Apps | Azure Marketplace
description: In diesem Artikel wird erläutert, wie eine Testversion erstellt wird, die mit einer Dynamics AX/CRM-Instanz oder einer anderen Ressource über Azure hinaus verbunden wird.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 3dc5642541f411acc93301f6bb9828e901aba449
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64940002"
---
<a name="logic-app-test-drive"></a>Testversion für Logik-Apps
====================

Dieser Artikel richtet sich an Herausgeber, die ihr Angebot in AppSource veröffentlichen und eine Testversion erstellen möchten, die mit einer Dynamics AX/CRM-Instanz oder einer anderen Ressource über Azure hinaus verbunden wird.

<a name="how-to-build-a-logic-app-test-drive"></a>Erstellen einer Testversion für Logik-Apps
-----------------------------------

Die Dokumentation für Testversionen für Logik-Apps befindet sich derzeit noch auf GitHub. Dort finden Sie weitere Informationen zu [Vorgängen](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) und [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app).

<a name="how-to-publish-a-test-drive"></a>Veröffentlichen einer Testversion
---------------------------

Nachdem Sie Ihre Testversion erstellt haben, werden in diesem Abschnitt die einzelnen Felder beschrieben, für die Sie Eingaben vornehmen müssen, damit Sie die Testversion erfolgreich veröffentlichen können.

![Aktivieren der Testversion](./media/azure-resource-manager-test-drive/howtopub1.png)

Im ersten und wichtigsten Feld müssen Sie festlegen, ob die Testversion für Ihr Angebot aktiviert werden soll. Wenn Sie **Nein** auswählen, wird das Formular deaktiviert. Wenn Sie die Veröffentlichung dann erneut mit der deaktivierten Testversion durchführen, wird die Testversion aus der Produktion entfernt.

*Hinweis*: Wenn Testversionen von Benutzern aktiv verwendet werden, werden diese Testversionen weiterhin ausgeführt, bis die Sitzung der Benutzer abgelaufen ist.

### <a name="details"></a>Details

Im nächsten Abschnitt werden die Details zu Ihrem Angebot der Testversion ausgefüllt.

![Details zur Testversion](./media/azure-resource-manager-test-drive/howtopub2.png)

**Beschreibung:** *[Pflichtfeld]* Hier geben Sie die Beschreibung der Inhalte der Testversion ein. Hier kann der Kunde nachlesen, welche Szenarien die Testversion für Ihr Produkt abdeckt. 

**Benutzerhandbuch:** *[Pflichtfeld]* Dies ist die ausführliche Beschreibung zur Verwendung Ihrer Testversion. Mit diesem Benutzerhandbuch kann der Kunde genau die vorgesehenen Schritte in der Testversion ausführen und durchlaufen. Es ist wichtig, dass die Inhalte einfach nachzuvollziehen und auszuführen sind. (Es muss sich um eine PDF-Datei handeln.)

**Test Drive Demo Video** (Demovideo für Testversion): \[Empfohlen\] Ähnlich dem Benutzerhandbuch empfiehlt es sich, ein Videotutorial für Ihre Testversion einzufügen. Der Kunde sieht sich dieses Tutorial vor oder während der Verwendung der Testversion an und kann genau die vorgesehenen Schritte in der Testversion ausführen und durchlaufen. Es ist wichtig, dass die Inhalte einfach nachzuvollziehen und auszuführen sind.

- **Name:** Titel Ihres Videos
- **Link:** muss eine eingebettete URL von YouTube oder Vimeo sein. Ein Beispiel zum Abrufen der eingebetteten URL ist nachfolgend aufgeführt:
- **Miniaturansicht:** muss ein hochwertiges Bild sein (533 x 324 Pixel). Es wird empfohlen, hier einen Screenshot eines Teils der Testversion einzufügen.

Unten wird gezeigt, wie diese Felder in der Testversion von Kunden angezeigt werden.

![Erscheinungsbild der Felder für die Testversion](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Technische Konfiguration

Im nächsten Abschnitt konfigurieren Sie die Testversion für die Logik-App und definieren die spezielle Ausführung der Testversioninstanzen.

![Technische Konfiguration der Testversion](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **Region:**  -  *[Pflichtfeld]* Hier wählen Sie die Region aus, in der die Ressourcen der Testversion für die Logik-App bereitgestellt werden.

    *Hinweis:* Wenn die Logik-App benutzerdefinierte Ressourcen umfasst, die in einer Region gespeichert sind, müssen Sie diese Region hier auswählen. Dazu empfiehlt es sich, **die Logik-App lokal in Ihrem Azure-Abonnement im Portal vollständig bereitzustellen und zu überprüfen, ob sie ausgeführt wird**, bevor Sie sie hier eingeben.

- **Maximum Concurrent Test Drives** (Max. zulässige gleichzeitige Testversionen): *[Pflichtfeld]* Anzahl der Testversioninstanzen, die pro ausgewählter Region bereits bereitgestellt sind und auf Zugriff warten. Kunden haben sofort Zugriff auf diese Testversionen und müssen nicht auf die Bereitstellung warten.

    *Hinweis:* Wenn Sie ein Webinar oder einen Kurs ausführen, bei dem alle N Teilnehmer eine Testversion nutzen können sollen, wird empfohlen, die Veröffentlichung mit N Instanzen des Typs „Heiß“ durchzuführen und nach Abschluss des Webinars oder Kurses die Veröffentlichung mit der normalen Anzahl der Instanzen des Typs „Heiß“ erneut durchzuführen.

- **Dauer der Testversion (Stunden):** *[Pflichtfeld]* Zeitraum, in dem die Testversion aktiv bleibt, angegeben in \# Stunden. Nach Ablauf dieses Zeitraums wird die Testversion automatisch beendet.

- **Name der Azure-Ressourcengruppe:** *[Pflichtfeld]* Geben Sie den Namen der Ressourcengruppe ein, in der die Testversionen für die Logik-App gespeichert werden.

- **Assign Logic App Name** (Logik-App-Name für Zuweisung): *[Pflichtfeld]* Hier wird die verwendete Logik-App eingegeben, um einen Benutzer in der Testversion zuzuweisen, bevor der Kunde sie erhält. Geben Sie den Namen der Logik-App hier ein. Stellen Sie sicher, dass diese Datei in der oben angegebenen Ressourcengruppe gespeichert wird.

- **Deprovision Logic App Name** (Logik-App-Name für Aufhebung der Bereitstellung): *[Pflichtfeld]* Geben Sie den Logik-App-Namen für die Aufhebung der Bereitstellung aller in der Testversion erstellten Ressourcen ein. Stellen Sie sicher, dass diese Datei in der oben angegebenen Ressourcengruppe gespeichert wird.

- **Zugriffsinformationen:** *[Pflichtfeld]* Nachdem ein Kunde seine Testversion erhalten hat, erhält er auch die Zugriffsinformationen. In diesen Anweisungen können die nützlichen Ausgabeparameter aus der Resource Manager-Vorlage für die Testversion angegeben werden. Verwenden Sie zum Einfügen von Ausgabeparametern doppelte geschweifte Klammern (z.B. **{{Ausgabename}}** ). So werden sie am Speicherort korrekt eingefügt. (Zur Darstellung im Front-End wird die HTML-Formatierung von Zeichenfolgen empfohlen.)

### <a name="test-drive-deployment-subscription-details"></a>Abonnementdetails für Bereitstellung der Testversion

Im letzten Abschnitt werden die Eingaben vorgenommen, um die Testversionen durch Herstellen einer Verbindung mit Ihrem Azure-Abonnement und mit Azure Active Directory (AD) automatisch bereitzustellen.

![Abonnementdetails für Bereitstellung der Testversion](./media/azure-resource-manager-test-drive/subdetails1.png)

**Azure-Abonnement-ID:** *[Pflichtfeld]* Diese gewährt Zugriff auf Azure-Dienste und das Azure-Portal. Im Abonnement werden die Ressourcenverwendung protokolliert und die Dienste abgerechnet. Wenn Sie noch nicht über ein **separates** Azure-Abonnement nur für Testversionen verfügen, erstellen Sie nun eines. Sie finden die Azure-Abonnement-IDs, indem Sie sich im Azure-Portal anmelden und im Menü auf der linken Seite zu den Abonnements navigieren.
(Beispiel: „a83645ac-1234-5ab6-6789-1h234g764ghty“)

![Azure-Abonnements](./media/azure-resource-manager-test-drive/subdetails2.png)

**Azure AD Tenant ID** (Azure AD-Mandanten-ID): *[Pflichtfeld]* Wenn Sie bereits über eine Mandanten-ID verfügen, finden Sie sie wie unten gezeigt unter „Eigenschaften“ -\> „Verzeichnis-ID“.

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

Erstellen Sie andernfalls einen neuen Mandanten in Azure Active Directory.

![Azure Active Directory-Fenster „Eigenschaften“](./media/azure-resource-manager-test-drive/subdetails4.png)

!Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails5.png)

![Azure Active Directory-Mandant](./media/azure-resource-manager-test-drive/subdetails6.png)

**Azure AD-Anwendungs-ID:** *[Pflichtfeld]* Im nächsten Schritt wird eine neue Anwendung erstellt und registriert. In dieser Anwendung werden Vorgänge in Ihrer Testversioninstanz ausgeführt.

1. Navigieren Sie zu dem neu erstellten oder einem bereits vorhandenen Verzeichnis, und wählen Sie Azure Active Directory im Filterbereich aus.
2. Suchen Sie „App-Registrierungen“, und klicken Sie auf „Hinzufügen“.
3. Geben Sie einen Anwendungsnamen an.
4. Wählen Sie den Typ „Web-App/API“ aus.
5. Geben Sie in „Anmelde-URL“ einen beliebigen Wert an. Dieses Feld wird nicht verwendet.
6. Klicken Sie auf "Erstellen".
7. Navigieren Sie nach der Erstellung der Anwendung zu „Eigenschaften“ -\> „Set the application as multi-tenant“ (Anwendung als mehrinstanzenfähig festlegen), und klicken Sie auf „Speichern“.

Klicken Sie auf Speichern. Im letzten Schritt wird die Anwendungs-ID für diese registrierte Anwendung abgerufen und im Feld der Testversion eingefügt.

![ID der Azure Active Directory-Anwendung](./media/azure-resource-manager-test-drive/subdetails7.png)

Wenn die Anwendung zum Durchführen der Bereitstellung im Abonnement verwendet wird, muss die Anwendung im Abonnement als Mitwirkender hinzugefügt werden. Dazu müssen folgende Schritte ausgeführt werden:

1. Navigieren Sie zum Blatt „Abonnements“, und wählen Sie das entsprechende Abonnement aus, das nur für die Testversion verwendet wird.
1. Klicken Sie auf **Zugriffssteuerung (IAM)** .
1. Klicken Sie auf die Registerkarte **Rollenzuweisungen**.  ![Azure Active Directory, Hinzufügen eines neuen Zugriffssteuerungsprinzipals](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Klicken Sie auf **Rollenzuweisung hinzufügen**.
1. Legen Sie die Rolle als **Mitwirkender** fest.
1. Geben Sie den Namen der Azure AD-Anwendung ein, und wählen Sie die Anwendung aus, um die Rolle zuzuweisen.
    ![Azure Active Directory-Berechtigungen](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Klicken Sie auf **Speichern**.

**Azure AD App Key** (Azure AD-App-Schlüssel): *[Pflichtfeld]* Im letzten Feld wird ein Authentifizierungsschlüssel generiert. Geben Sie unter „Schlüssel“ eine Beschreibung des Schlüssels an, legen Sie die Dauer so fest, dass er nie abläuft, und wählen Sie dann „Speichern“ aus. Es ist **wichtig**, dass der Schlüssel nicht abläuft, da dies andernfalls zu Fehlern der Testversion in der Produktion führt. Kopieren Sie diesen Wert, und fügen Sie ihn im entsprechenden Feld für die Testversion ein.

![Azure Active Directory-Schlüssel](./media/azure-resource-manager-test-drive/subdetails8.png)

> [!CAUTION]
> Sie können nicht die Vorschauversion der Azure-App-Registrierung verwenden, da sie derzeit keinen base64-codierten Schlüssel generiert.


<a name="next-steps"></a>Nächste Schritte
----------

Nachdem Sie alle Felder für die Testversion ausgefüllt haben, überprüfen Sie sie, und führen Sie dann die **erneute Veröffentlichung** des Angebots durch. Nachdem Ihre Testversion den Zertifizierungsvorgang bestanden hat, sollten Sie die **Vorschauversion** Ihres Angebots umfangreich testen. Starten Sie eine Testversion in der Benutzeroberfläche, und überprüfen Sie, ob Ihre Testversionen vollständig und korrekt bereitgestellt werden.

Es ist wichtig, dass Sie keinen Teil der Testversionen löschen, da diese für Kunden bereitgestellt werden. Der Dienst der Testversion entfernt diese Ressourcengruppen automatisch, nachdem Kunden die Testversion nicht mehr verwenden.

Wenn Sie mit Ihrer Vorschauversion zufrieden sind, können Sie sie **veröffentlichen**. Nach der Veröffentlichung des Angebots erfolgt eine letzte Überprüfung durch Microsoft, in der das gesamte Angebot erneut gründlich überprüft wird. Wenn Ihr Angebot aus einem bestimmten Grund abgelehnt wird, wird eine Benachrichtigung an den Engineering-Kontakt für Ihr Angebot gesendet, in dem erläutert wird, was korrigiert werden muss.

Wenn Sie weitere Fragen haben, Tipps zur Problembehandlung benötigen oder Ihre Testversion optimieren möchten, lesen Sie [Häufig gestellte Fragen, Problembehandlung und bewährte Methoden](./marketing-and-best-practices.md).
