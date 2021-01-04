---
title: Testen der technischen Konfiguration, kommerzieller Microsoft-Marketplace
description: Erfahren Sie mehr über Testversionen. Testversionen ermöglichen es neuen Kunden, Ihr Angebot zu testen, bevor sie sich zum Kauf verpflichten.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/13/2019
author: trkeya
ms.author: trkeya
ms.openlocfilehash: 553eba3898a5ea42e5d478603e35e82c68abcab1
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462898"
---
# <a name="test-drive-technical-configuration"></a>Technische Konfiguration der Testversion

Mit der Option „Testversion“ im kommerziellen Microsoft-Marketplace können Sie eine praktische, interaktive Tour durch die wichtigsten Funktionen Ihres Produkts konfigurieren. Mit einer Testversion können neue Kunden Ihr Angebot ausprobieren, bevor sie sich zum Kauf verpflichten. Weitere Informationen finden Sie unter [Was ist die Testversion?](what-is-test-drive.md)

Wenn Sie keine Testversion mehr für Ihr Angebot bereitstellen möchten, kehren Sie zur Seite **Angebotseinrichtung** zurück, und deaktivieren Sie das Kontrollkästchen **Testversion aktivieren**. Nicht für alle Angebotstypen ist eine Testversion verfügbar.

## <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager-Testversion

Dies ist die einzige Testversionsoption für VM- oder Azure-App-Angebote, die zudem eine recht detaillierte Einrichtung erfordert. Lesen Sie die folgenden Abschnitte mit [Details zur Bereitstellung von Abonnements](#deployment-subscription-details) und [Listungen von Testversionen](#test-drive-listings). Fahren Sie dann mit dem separaten Thema zur [Konfiguration einer Testversion von Azure Resource Manager](azure-resource-manager-test-drive.md) fort.

## <a name="hosted-test-drive"></a>Gehostete Testversion

Microsoft kann die Dienstbereitstellung und die Aufhebung der Bereitstellung hosten und verwalten, um das Einrichten einer Testversion zu vereinfachen. Die Konfiguration für diese Art von Testversion ist unabhängig davon gleich, ob die Testversion für Dynamics 365 Customer Engagement oder Dynamics 365 Operation vorgesehen ist.

- **Maximale Anzahl gleichzeitig ausgeführter Testversionen:** Legen Sie die maximale Anzahl von Kunden fest, die Ihre Testversion gleichzeitig verwenden können. Jeder gleichzeitige Benutzer nutzt eine Dynamics 365-Lizenz, solange die Testversion aktiv ist. Stellen sie daher sicher, dass genügend Lizenzen für die festgelegte maximale Anzahl verfügbar sind. Es wird ein Wert zwischen 3 und 5 empfohlen.

- **Dauer der Testversion** (erforderlich): Geben Sie die Anzahl von Stunden ein, für die die Testversion für Kunden aktiv bleiben soll. Nach Ablauf dieses Zeitraums wird die Sitzung beendet und nutzt keine Ihrer Lizenzen mehr. Der empfohlene Wert beträgt 2 bis 24 Stunden, abhängig von der Komplexität Ihres Angebots. Diese Dauer kann nur in ganzen Stunden angegeben werden („2 Stunden“ ist beispielsweise ein gültiger Wert, „1,5 Stunden“ jedoch nicht). Der Benutzer kann eine neue Sitzung anfordern, wenn die Zeit abgelaufen ist und er erneut auf die Testversion zugreifen möchte.

- **Instanz-URL** (erforderlich): Geben Sie die URL an, über die der Kunde die Testversion startet. Dies ist in der Regel die URL Ihrer Dynamics 365-Instanz, auf der Ihre App mit installierten Beispieldaten ausgeführt wird (z. B. `https://testdrive.crm.dynamics.com`).

- **Web-API-URL der Instanz** (erforderlich): Rufen Sie die Web-API-URL für Ihre Dynamics 365-Instanz ab, indem Sie sich bei Ihrem Microsoft 365-Konto anmelden, zu **Einstellungen**  > **Anpassung** > **Entwicklerressourcen** > **Instance Web API (Service Root URL)** (Instanz-Web-API (Stamm-URL des Diensts)) navigieren und die dort angegebene URL kopieren (z. B. `https://testdrive.crm.dynamics.com/api/data/v9.0`).

- **Rollenname** (erforderlich): Geben Sie den Namen der Sicherheitsrolle an, den Sie in der benutzerdefinierten Dynamics 365-Testversion definiert haben. Dieser wird dem Benutzer für die Dauer der Testversion zugewiesen (z. B. „Testversionsrolle“).

Wenn Sie Hilfe beim Einrichten Ihrer Dynamics 365-Umgebung für Testversionen und bei der Erteilung der AppSource-Genehmigung zum Bereitstellen bzw. Aufheben der Bereitstellung von Testversionsbenutzern in Ihrem Mandanten benötigen, befolgen Sie [diese Anweisungen](./test-drive-azure-subscription-setup.md).

Eine Schrittanleitung zum Auflisten und Konfigurieren der gehosteten Testversion finden Sie auf der Seite [Ausführliche Konfiguration für gehostete Testversionen](./test-drive-hosted-detailed-config.md).

## <a name="logic-app-test-drive"></a>Testversion für Logik-Apps

Diese Art von Testversion wird nicht von Microsoft gehostet. Nutzen Sie sie, um sich mit einem Dynamics 365-Angebot oder einer anderen benutzerdefinierten Ressource zu verbinden, die eine Vielzahl komplexer Lösungsarchitekturen umfasst. Um weitere Informationen über das Einrichten von Logik-App-Testversionen zu erhalten, besuchen Sie [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) und [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) auf GitHub (in englischer Sprache).

- **Region** (erforderlich; Dropdownliste für Einfachauswahl): Es gibt derzeit 26 von Azure unterstützte Regionen, in denen Ihre Testversion zur Verfügung gestellt werden kann. Die Ressourcen für Ihre Logik-App werden in der von Ihnen gewählten Region bereitgestellt. Wenn die Logik-App benutzerdefinierte Ressourcen umfasst, die in einer bestimmten Region gespeichert sind, müssen Sie diese Region hier auswählen. Dazu empfiehlt es sich, die Logik-App lokal in Ihrem Azure-Abonnement im Portal vollständig bereitzustellen und zu überprüfen, ob sie ordnungsgemäß ausgeführt wird, bevor Sie diese Auswahl vornehmen.

- **Maximale Anzahl gleichzeitig ausgeführter Testversionen** (erforderlich): Legen Sie die maximale Anzahl von Kunden fest, die Ihre Testversion gleichzeitig verwenden können. Diese Testversionen sind bereits bereitgestellt, sodass Kunden sofort auf sie zugreifen können, ohne auf eine Bereitstellung warten zu müssen.

- **Dauer der Testversion** (erforderlich): Geben Sie die Dauer in Stunden ein, für die die Testversion aktiv bleiben soll. Nach Ablauf dieses Zeitraums wird die Testversion automatisch beendet.

- **Name der Azure-Ressourcengruppe** (erforderlich): Geben Sie den Namen der [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md#resource-groups) ein, in der die Testversion für die Logik-App gespeichert ist.

- **Azure logic app name** (Name der Azure-Logik-App) (erforderlich): Geben Sie den Namen der Logik-App ein, von der die Testversion dem Benutzer zugewiesen wird. Diese Logik-App muss in der oben angegebenen Azure-Ressourcengruppe gespeichert werden.

- **Zuweisung des Namens der Logik-App rückgängig machen** (erforderlich): Geben Sie den Namen der Logik-App ein, mit der die Bereitstellung der Testversion aufgehoben wird, wenn sie vom Kunden nicht mehr verwendet wird. Diese Logik-App muss in der oben angegebenen Azure-Ressourcengruppe gespeichert werden.

## <a name="power-bi-test-drive"></a>Power BI-Testversion

Für Produkte, mit denen ein interaktives Power BI-Visual veranschaulicht werden soll, kann ein eingebetteter Link zum Freigeben eines benutzerdefinierten Dashboards als Testversion verwendet werden. Deshalb ist keine technische Konfiguration erforderlich. Sie müssen hier lediglich Ihre eingebettete Power BI-URL hochladen.

Weitere Informationen zum Einrichten von Power BI-Apps finden Sie unter [Was sind Power BI-Apps?](/power-bi/service-template-apps-overview).

## <a name="deployment-subscription-details"></a>Abonnementdetails für Bereitstellung der Testversion

Erstellen Sie ein separates eindeutiges Azure-Abonnement, und stellen Sie es bereit (für Power BI-Testversionen nicht erforderlich), damit Microsoft die Testversion in Ihrem Auftrag bereitstellen kann.

- **Azure-Abonnement-ID** (erforderlich für den Azure Resource Manager und Logic Apps): Geben Sie die Abonnement-ID ein, um für Ressourcennutzungsberichte und Abrechnungszwecke Zugriff auf die Dienste Ihres Azure-Kontos zu gewähren. Es wird empfohlen, [ein eigenes Azure-Abonnement zu erstellen](../cost-management-billing/manage/create-subscription.md), das für Testversionen verwendet wird, falls Sie es noch nicht getan haben. Sie finden die Azure-Abonnement-IDs, indem Sie sich beim [Azure-Portal](https://portal.azure.com/) anmelden und im Menü auf der linken Seite zu den **Abonnements** navigieren. Durch Auswahl der Registerkarte wird Ihre Abonnement-ID angezeigt (z. B. a83645ac-1234-5ab6-6789-1h234g764ghty).

- **ID des Azure AD-Mandanten** (erforderlich): Geben Sie Ihre [Mandanten-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) für Azure Active Directory (AD) ein. Um diese ID zu ermitteln, melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, wählen im linken Menü die Registerkarte „Active Directory“ und dann **Eigenschaften** aus und suchen die unter **Verzeichnis-ID** aufgeführte Nummer (z. B. 50c464d3-4930-494c-963c-1e951d15360e). Sie können auch unter [https://www.whatismytenantid.com](https://www.whatismytenantid.com) die Mandanten-ID Ihrer Organisation mithilfe der Adresse eines Domänennamens nachschlagen.

- **Azure AD tenant name** (Name des Azure AD-Mandanten) (erforderlich für Dynamics 365): Geben Sie den Namen Ihrer Azure Active Directory-Instanz (AD) ein. Um diesen Namen zu suchen, melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an. Oben rechts wird der Mandantenname unter Ihrem Kontonamen angegeben.

- **Azure AD-App-ID** (erforderlich): Geben Sie Ihre [Anwendungs-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) für Azure Active Directory (AD) ein. Um diese ID zu ermitteln, melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, wählen Sie im linken Menü die Registerkarte „Active Directory“ und dann **App-Registrierungen** aus, und suchen Sie die unter **Anwendungs-ID** aufgeführte Nummer (z. B. `50c464d3-4930-494c-963c-1e951d15360e`).

- **Azure AD app client secret** (Geheimer Clientschlüssel der Azure AD-App) (erforderlich): Geben Sie den [geheimen Clientschlüssel](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret) Ihrer Azure AD-Anwendung ein. Um diesen Wert zu finden, melden Sie sich beim [Azur-Portal](https://portal.azure.com/) an. Wählen Sie im linken Menü die Registerkarte **Azure Active Directory** aus, klicken Sie auf **App-Registrierungen**, und wählen Sie dann Ihre Testversion-App aus. Wählen Sie als Nächstes **Zertifikate und Geheimnisse** und dann **Neuer geheimer Clientschlüssel** aus, geben Sie eine Beschreibung ein, wählen Sie **Nie** unter **Läuft ab** aus, und klicken Sie dann auf **Hinzufügen**. Stellen Sie sicher, dass Sie den Wert kopieren. Navigieren Sie erst von der Seite weg, nachdem Sie den Wert kopiert haben.

## <a name="test-drive-listings"></a>Listungen von Testversionen

Auf der Partner Center-Registerkarte **Test drive listings** (Listungen von Testversionen) unter **Testversion** werden die Sprachen (und Märkte) angezeigt, in denen Ihre Testversion verfügbar ist. Derzeit ist nur das Gebietsschema „Englisch (USA)“ verfügbar. Auf dieser Seite werden zudem der Status der sprachspezifischen Liste und das Datum/die Uhrzeit der Hinzufügung angezeigt. Sie müssen für jede Sprache/jeden Markt die Testversionsdetails (Beschreibung, Benutzerhandbuch, Videos usw.) definieren.

- **Beschreibung:** (erforderlich): Beschreiben Sie Ihre Testversion. Geben Sie an, was vorgeführt wird, nennen Sie die Ziele, mit denen der Benutzer experimentieren soll, sowie die zu erkundenden Funktionen, und geben Sie relevante Informationen an, die dem Benutzer helfen zu entscheiden, ob er Ihr Angebot erwerben soll. In diesem Feld können bis zu 3.000 Zeichen Text eingegeben werden.

- **Zugriffsinformationen** (für Azure Resource Manager und Logik-Testversionen erforderlich): Erläutern Sie, was ein Kunde wissen muss, um auf die Testversion zuzugreifen und sie zu verwenden. Beschreiben Sie ein Szenario für die Verwendung Ihres Angebots, und teilen Sie genau mit, was der Kunde wissen sollte, um auf die Funktionen in der gesamten Testversion zuzugreifen. In diesem Feld können bis zu 10.000 Zeichen Text eingegeben werden.

- **Benutzerhandbuch** (erforderlich): Eine ausführliche Beschreibung der Verwendung Ihrer Testversion. Das Benutzerhandbuch sollte genau beschreiben, was der Kunde durch die Erkundung der Testversion erlangen soll, und als Referenz für die Fragen des Kunden dienen. Die Datei muss das PDF-Format aufweisen und nach dem Hochladen mit einem Namen (max. 255 Zeichen) versehen werden.

- **Videos: Videos hinzufügen** (optional): Auf anderswo gehostete Videos kann hier mit einem Link und einer Miniaturansicht (533 x 324 Pixel) verwiesen werden. So erhalten Kunden Informationen zu einzelnen Schritten, um sich besser mit der Testversion vertraut zu machen. Unter anderem können Sie ihnen zeigen, wie sie die Funktionen Ihres Angebots erfolgreich verwenden, und Szenarien erläutern, in denen die Vorteile hervorgehoben werden.
  - **Name** (erforderlich)
  - **URL (nur YouTube oder Vimeo)** (erforderlich)
  - **Vorschauminiatur** (533 × 324 Pixel ): (Bilddatei muss im PNG-Format vorliegen)

Wenn Sie Ihre Testversion derzeit im Partner Center erstellen, wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

Eine Schrittanleitung zum Auflisten und Konfigurieren der gehosteten Testversion finden Sie auf der Seite [Ausführliche Konfiguration für gehostete Testversionen](./test-drive-hosted-detailed-config.md).

## <a name="additional-resources"></a>Weitere Ressourcen

## <a name="next-steps"></a>Nächste Schritte

- [Bewährte Methoden für Testversionen](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Übersicht](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF-Datei; stellen Sie sicher, dass Ihr Popupblocker deaktiviert ist.)
- [Aktualisieren eines bestehenden Commercial Marketplace-Angebots](partner-center-portal/update-existing-offer.md)