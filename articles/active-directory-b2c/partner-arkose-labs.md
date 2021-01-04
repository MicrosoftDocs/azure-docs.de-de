---
title: Verwenden von Arkose Labs mit Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Es wird beschrieben, wie Sie die Azure AD B2C-Authentifizierung mit Arkose Labs integrieren, um für den Schutz vor Botangriffen, Angriffen mit Kontoübernahmen und betrügerischen Kontoeröffnungen zu sorgen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cf0437b985865248393e9f68da264fcae5af7f12
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108551"
---
# <a name="tutorial-for-configuring-arkose-labs-with-azure-active-directory-b2c"></a>Tutorial zum Konfigurieren von Arkose Labs mit Azure Active Directory B2C

In diesem Tutorial wird beschrieben, wie Sie die Azure AD B2C-Authentifizierung mit Arkose Labs integrieren. Arkose Labs unterstützt Organisationen vor Botangriffen, Angriffen mit Kontoübernahmen und betrügerischen Kontoeröffnungen.  

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* [Einen Azure AD B2C-Mandanten](tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

Im folgenden Diagramm ist beschrieben, wie Arkose Labs mit Azure AD B2C integriert wird.

![Architekturdiagramm für Arkose Labs](media/partner-arkose-labs/arkose-architecture-diagram.png)

| Schritt  | BESCHREIBUNG |
|---|---|
|1     | Ein Benutzer meldet sich mit einem zuvor erstellten Konto an. Wenn der Benutzer die Option „Senden“ auswählt, wird eine Arkose Labs-Abfrage angezeigt. Nachdem der Benutzer richtig auf die Abfrage reagiert hat, wird der Status an Arkose Labs gesendet, um ein Token zu generieren.        |
|2     |  Arkose Labs sendet das Token zurück an Azure AD B2C.       |
|3     |  Bevor das Anmeldeformular übermittelt wird, wird das Token zur Überprüfung an Arkose Labs gesendet.       |
|4     |  Arkose sendet als Ergebnis der Abfrage „Erfolg“ oder „Fehler“ zurück.       |
|5     |  Wenn die Abfrage erfolgreich erfüllt wurde, wird ein Anmeldeformular an Azure AD B2C gesendet, und Azure AD B2C schließt die Authentifizierung ab.       |
|   |   |

## <a name="onboard-with-arkose-labs"></a>Durchführen des Onboardings für Arkose Labs

1. Wenden Sie sich zunächst an [Arkose Labs](https://www.arkoselabs.com/book-a-demo/), und erstellen Sie ein Konto.

2. Navigieren Sie nach der Kontoerstellung zu https://dashboard.arkoselabs.com/login.

3. Navigieren Sie im Dashboard zu den Websiteeinstellungen, um Ihren öffentlichen und privaten Schlüssel zu ermitteln. Sie benötigen diese Informationen später, um Azure AD B2C zu konfigurieren.

## <a name="integrate-with-azure-ad-b2c"></a>Integration in Azure AD B2C

### <a name="part-1--create-blob-storage-to-store-the-custom-html"></a>Teil 1: Erstellen von Blobspeicher zum Speichern des benutzerdefinierten HTML-Codes

Führen Sie diese Schritte aus, um ein Speicherkonto zu erstellen:  

1. Melden Sie sich am Azure-Portal an.

2. Achten Sie darauf, dass Sie das Verzeichnis verwenden, in dem Ihr Azure-Abonnement enthalten ist. Wählen Sie im Hauptmenü den Filter  **Verzeichnis + Abonnement** aus, und wählen Sie das Verzeichnis aus, in dem Ihr Abonnement enthalten ist. Dabei handelt es sich um ein anderes Verzeichnis als das, in dem Ihr Azure B2C-Mandant enthalten ist.

3. Wählen Sie oben links im Azure-Portal die Option **Alle Dienste** aus. Suchen Sie anschließend nach der Option  **Speicherkonten**, und wählen Sie sie aus.

4. Wählen Sie **Hinzufügen**.

5. Wählen Sie unter  **Ressourcengruppe** die Option  **Neu erstellen** aus, geben Sie einen Namen für die neue Ressourcengruppe ein, und wählen Sie dann **OK** aus.

6. Geben Sie einen Namen für das Speicherkonto ein. Der ausgewählte Name muss in Azure eindeutig sein und zwischen 3 und 24 Zeichen aufweisen, und er darf nur Zahlen und Kleinbuchstaben enthalten.

7. Wählen Sie den Standort Ihres Speicherkontos aus, oder akzeptieren Sie den Standardstandort.

8. Übernehmen Sie alle anderen Standardwerte, und wählen Sie   **Überprüfen und erstellen** > **Erstellen** aus.

9. Wählen Sie nach dem Erstellen des neuen Speicherkontos  **Zu Ressource wechseln** aus.

#### <a name="create-a-container"></a>Erstellen eines Containers

1. Wählen Sie auf der Übersichtsseite des Speicherkontos die Option   **Blobs** aus.

2. Wählen Sie   **Container** aus, geben Sie einen Namen für den Container ein, und wählen Sie   **Blob (anonymer Lesezugriff nur für Blobs)** aus. Klicken Sie anschließend auf **OK**.

#### <a name="enable-cross-origin-resource-sharing-cors"></a>Aktivieren von CORS (Cross-Origin Resource Sharing)

Azure AD B2C-Code in einem Browser verwendet einen modernen Standardansatz zum Laden benutzerdefinierter Inhalte über eine URL, die Sie in einem Benutzerflow angeben. CORS (Cross-Origin Resource Sharing, Ressourcenfreigabe zwischen verschiedenen Ursprüngen) ermöglicht es, dass eingeschränkte Ressourcen auf einer Webseite aus anderen Domänen angefordert werden können.

1. Wählen Sie im Menü die Option  **CORS** aus.

2. Geben Sie unter  **Zulässige Ursprünge** die URL `https://your-tenant-name.b2clogin.com` ein. Ersetzen Sie „your-tenant-name“ durch den Namen Ihres Azure AD B2C-Mandanten. Beispiel:  `https://fabrikam.b2clogin.com`. Verwenden Sie bei der Eingabe Ihres Mandantennamens ausschließlich Kleinbuchstaben.

3. Wählen Sie unter  **Zulässige Methoden** die Optionen  **GET**, **PUT** und  **OPTIONS** aus.

4. Geben Sie für **Zulässige Header** ein Sternchen (*) ein.

5. Geben Sie für **Verfügbar gemachte Header** ein Sternchen (*) ein.

6. Für **Max. Alter** geben Sie 200 ein.

   ![Registrierung und Anmeldung für Arkose Labs](media/partner-arkose-labs/signup-signin-arkose.png)

7. Wählen Sie **Speichern** aus.

### <a name="part-2--set-up-a-back-end-server"></a>Teil 2: Einrichten eines Back-End-Servers

Laden Sie Git Bash herunter, und führen Sie die unten angegebenen Schritte aus:

1. Befolgen Sie die Anleitung zum [Erstellen einer Web-App](../app-service/quickstart-php.md), bis die Meldung „Glückwunsch! Sie haben Ihre erste PHP-App für App Service bereitgestellt.“ angezeigt wird.

2. Öffnen Sie Ihren lokalen Ordner, und benennen Sie die Datei **index.php** in **verify-token.php** um.

3. Öffnen Sie die umbenannte Datei „verify-token.php“, und gehen Sie dann wie folgt vor:

   a. Ersetzen Sie den Inhalt durch den Inhalt aus der Datei „verify-token.php“, die im [GitHub-Repository](https://github.com/ArkoseLabs/Azure-AD-B2C) enthalten ist.

   b. Ersetzen Sie „<private_key>“ in Zeile 3 durch Ihren privaten Schlüssel aus dem Arkose Labs-Dashboard.

4. Committen Sie im lokalen Terminalfenster Ihre Änderungen in Git, und übertragen Sie die Codeänderungen dann per Pushvorgang an Azure, indem Sie in Git Bash Folgendes eingeben:

   ``git commit -am "updated output"``

   ``git push azure master``  

### <a name="part-3---final-setup"></a>Teil 3: Abschließende Einrichtung

#### <a name="store-the-custom-html"></a>Speichern des benutzerdefinierten HTML-Codes

1. Öffnen Sie die Datei „index.html“, die im [GitHub-Repository](https://github.com/ArkoseLabs/Azure-AD-B2C) gespeichert ist.

2. Ersetzen Sie alle Instanzen von `<tenantname>` durch den Namen Ihres B2C-Mandanten (also `<tenantname>.b2clogin.com`). Es sollten vier Instanzen vorhanden sein.

3. Ersetzen Sie `<appname>` durch den App-Namen, den Sie im ersten Schritt von Teil 2 erstellt haben.

   ![Screenshot: Arkose Labs Azure CLI](media/partner-arkose-labs/arkose-azure-cli.png)

4. Ersetzen Sie `<public_key>` in Zeile 64 durch den öffentlichen Schlüssel aus dem Arkose Labs-Dashboard.

5. Laden Sie die Datei „index.html“ in den oben erstellten Blobspeicher hoch.

6. Navigieren Sie zu **Speicher** > **Container** > **Hochladen**.

#### <a name="set-up-azure-ad-b2c"></a>Einrichten von Azure AD B2C

> [!NOTE]
> Wenn Sie noch nicht über einen Azure AD B2C-Mandanten verfügen, [erstellen Sie einen](tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.

1. Erstellen Sie basierend auf [diesen Informationen](tutorial-create-user-flows.md) einen Benutzerflow. Halten Sie beim Abschnitt **Testen des Benutzerflows** an.

2. Aktivieren Sie JavaScript in Ihrem [Benutzerflow](javascript-and-page-layout.md).

3. Aktivieren Sie auf derselben Seite des Benutzerflows die benutzerdefinierte Seiten-URL: Navigieren Sie zu **Benutzerflow** > **Seitenlayout** > **Benutzerdefinierten Seiteninhalt verwenden** = **Ja** > **Benutzerdefinierte Seiten-URL einfügen**.
Diese benutzerdefinierte Seiten-URL finden Sie am Speicherort der Datei „index.html“ im Blobspeicher.  

   ![Screenshot: Arkose Labs-Speicher-URL](media/partner-arkose-labs/arkose-storage-url.png)

## <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Öffnen Sie den Azure AD B2C-Mandanten, und wählen Sie unter **Richtlinien** die Option **Benutzerflows** aus.

2. Wählen Sie den zuvor erstellten Benutzerflow aus.

3. Wählen Sie **Benutzerflow ausführen** und dann die Einstellungen aus:

   a. **Anwendung**: Wählen Sie die registrierte App aus (Beispiel: JWT).

   b. **Antwort-URL**: Wählen Sie die Umleitungs-URL aus.

   c. Wählen Sie **Benutzerflow ausführen** aus.

4. Führen Sie den Registrierungsablauf aus, und erstellen Sie ein Konto.

5. Melden Sie sich ab.

6. Führen Sie den Ablauf für die Anmeldung durch.

7. Nachdem Sie **Weiter** ausgewählt haben, wird ein Arkose-Puzzle angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Benutzerdefinierte Richtlinien in Azure AD B2C](custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](custom-policy-get-started.md?tabs=applications)