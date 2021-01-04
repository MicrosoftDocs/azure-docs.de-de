---
title: 'Tutorial: Anpassen der Benutzeroberfläche'
titleSuffix: Azure AD B2C
description: In diesem Tutorial erfahren Sie, wie Sie die Benutzeroberfläche Ihrer Anwendungen in Azure Active Directory B2C mithilfe des Azure-Portals anpassen.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 036e9e6bf58d837c698a6092770ef581f77bc20c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108245"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>Tutorial: Anpassen der Benutzeroberfläche in Azure Active Directory B2C

Für weitere gängige Benutzerfunktionen wie etwa Registrierung, Anmeldung und Profilbearbeitung können Sie [Benutzerflows](user-flow-overview.md) in Azure Active Directory B2C (Azure AD B2C) verwenden. Die Informationen in diesem Tutorial helfen Ihnen beim [Anpassen der Benutzeroberfläche (UI)](customize-ui-with-html.md) dieser Erfahrungen unter Verwendung Ihrer eigenen HTML- und CSS-Dateien.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Erstellen von Dateien für die Benutzeroberflächenanpassung
> * Aktualisieren des Benutzerflows für die Verwendung von Dateien
> * Testen der angepassten Benutzeroberfläche

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

[Erstellen Sie einen Benutzerflow](tutorial-create-user-flows.md), damit sich Benutzer bei Ihrer Anwendung registrieren und anmelden können.

## <a name="create-customization-files"></a>Erstellen von Anpassungsdateien

Sie erstellen ein Azure-Speicherkonto mit Container und legen dann einfache HTML- und CSS-Dateien in dem Container ab.

### <a name="create-a-storage-account"></a>Speicherkonto erstellen

Zwar können Sie Ihre Dateien auf viele Arten speichern, doch für dieses Tutorial speichern Sie sie in [Azure-Blob-Speicher](../storage/blobs/storage-blobs-introduction.md).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihr Azure-Abonnement enthält. Wählen Sie im Hauptmenü den Filter **Verzeichnis + Abonnement** aus, und wählen Sie das Verzeichnis aus, das Ihr Abonnement enthält. Dabei handelt es sich um ein anderes Verzeichnis als das, in dem Ihr Azure B2C-Mandant enthalten ist.
3. Wählen Sie links oben im Azure-Portal „Alle Dienste“ aus, und suchen Sie dann nach **Speicherkonten**, und wählen Sie dies aus.
4. Wählen Sie **Hinzufügen**.
5. Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus, geben Sie einen Namen für die neue Ressourcengruppe ein, und klicken Sie dann auf **OK**.
6. Geben Sie einen Namen für das Speicherkonto ein. Der ausgewählte Name muss in Azure eindeutig sein und zwischen 3 und 24 Zeichen aufweisen, und er darf nur Zahlen und Kleinbuchstaben enthalten.
7. Wählen Sie den Standort Ihres Speicherkontos aus, oder akzeptieren Sie den Standardstandort.
8. Akzeptieren Sie alle anderen Standardwerte, wählen Sie **Überprüfen + erstellen** aus, und klicken Sie dann auf **Weiter**.
9. Wählen Sie nach dem Erstellen des neuen Speicherkontos **Zu Ressource wechseln** aus.

### <a name="create-a-container"></a>Erstellen eines Containers

1. Wählen Sie auf der Übersichtsseite des Speicherkontos **Blobs** aus.
2. Wählen Sie **Container** aus, geben Sie einen Namen für den Container ein, wählen Sie **Blob (anonymer Lesezugriff nur für Blobs)** aus, und klicken Sie dann auf **OK**.

### <a name="enable-cors"></a>Aktivieren von CORS

 Azure AD B2C-Code in einem Browser verwendet einen modernen Standardansatz zum Laden benutzerdefinierter Inhalte über eine URL, die Sie in einem Benutzerflow angeben. Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS) ermöglicht, dass eingeschränkte Ressourcen auf einer Webseite aus anderen Domänen angefordert werden können.

1. Wählen Sie im Menü **CORS** aus.
2. Geben Sie für **Zulässige Ursprünge** den Wert `https://your-tenant-name.b2clogin.com` ein. Ersetzen Sie `your-tenant-name` durch den Namen des Azure AD B2C-Mandanten. Beispiel: `https://fabrikam.b2clogin.com`. Sie dürfen bei der Eingabe Ihres Mandantennamens ausschließlich Kleinbuchstaben verwenden.
3. Wählen Sie für **Zulässige Methoden** sowohl `GET` und `PUT` als auch `OPTIONS` aus.
4. Geben Sie für **Zulässige Header** ein Sternchen (*) ein.
5. Geben Sie für **Verfügbar gemachte Header** ein Sternchen (*) ein.
6. Für **Max. Alter** geben Sie 200 ein.

    ![CORS-Konfigurationsseite in Azure Blob Storage im Azure-Portal](./media/tutorial-customize-ui/enable-cors.png)

5. Klicken Sie auf **Speichern**.

### <a name="create-the-customization-files"></a>Erstellen der Anpassungsdateien

Um die Benutzeroberfläche der Registrierungserfahrung anzupassen, beginnen Sie mit dem Erstellen einer einfache HTML- und CSS-Datei. Sie können Ihren HTML-Code beliebig konfigurieren, aber er muss ein **div**-Element mit einem Bezeichner von `api` enthalten. Beispiel: `<div id="api"></div>`. Azure AD B2C fügt Elemente in den `api`-Container ein, wenn die Seite angezeigt wird.

1. Erstellen Sie die folgende Datei in einem lokalen Ordner, und stellen Sie sicher, dass Sie `your-storage-account` in den Namen des Speicherkontos ändern und `your-container` in den Namen des Containers, den Sie erstellt haben. Beispiel: `https://store1.blob.core.windows.net/b2c/style.css`.

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>My B2C Application</title>
        <link rel="stylesheet" href="https://your-storage-account.blob.core.windows.net/your-container/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>
      </body>
    </html>
    ```

    Die Seite kann entworfen werden, wie Sie möchten, aber das **api** div-Element ist für jede HTML-Anpassungsdatei, die Sie erstellen, erforderlich.

3. Speichern Sie die Datei als *custom-ui.html*.
4. Erstellen Sie den folgenden einfachen CSS-Code, der alle Elemente auf der Registrierungs- oder Anmeldeseite, einschließlich der Elemente, die Azure AD B2C einfügt, zentriert.

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

5. Speichern Sie die Datei als *style.css*.

### <a name="upload-the-customization-files"></a>Hochladen der Anpassungsdateien

In diesem Tutorial speichern Sie die Dateien, die Sie im Speicherkonto erstellt haben, damit Azure AD B2C darauf zugreifen können.

1. Wählen Sie links oben im Azure-Portal **Alle Dienste** aus, und suchen Sie dann nach **Speicherkonten**, und wählen Sie dies aus.
2. Wählen Sie das von Ihnen erstellte Speicherkonto aus, wählen Sie **Blobs** aus, und wählen Sie dann den Container aus, den Sie erstellt haben.
3. Wählen Sie **Hochladen** aus, navigieren Sie zur Datei *custom-ui.html*, wählen Sie sie aus, und klicken Sie dann auf **Hochladen**.

    ![Seite „BLOB hochladen“ im Portal mit hervorgehobener Schaltfläche „Hochladen“ und Dateien](./media/tutorial-customize-ui/upload-blob.png)

4. Kopieren Sie die URL für die Datei, die Sie hochgeladen haben, um sie später in diesem Tutorial zu verwenden.
5. Wiederholen Sie Schritt 3 und 4 für die Datei *style.css*.

## <a name="update-the-user-flow"></a>Aktualisieren des Benutzerflows

1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
2. Wählen Sie **Benutzerflows** und dann den Benutzerflow *B2C_1_signupsignin1* aus.
3. Wählen Sie **Seitenlayouts** aus, und klicken Sie dann unter **Einheitliche Seite für Registrierung oder Anmeldung** bei **Benutzerdefinierten Seiteninhalt verwenden** auf **Ja**.
4. Geben Sie unter **Benutzerdefinierter Seiten-URI** den URI für die Datei *custom-ui.html* ein, die Sie zuvor gespeichert haben.
5. Klicken Sie am oberen Rand der Seite auf **Speichern**.

## <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten **Benutzerflows** und dann den Benutzerflow *B2C_1_signupsignin1* aus.
2. Klicken Sie im oberen Bereich der Seite auf **Benutzerflow ausführen**.
3. Klicken Sie auf die Schaltfläche **Benutzerflow ausführen**.

    ![Seite „Benutzerflow ausführen“ des Benutzerflows für die Registrierung oder Anmeldung](./media/tutorial-customize-ui/run-user-flow.png)

    Es sollte eine Seite ähnlich dem folgenden Beispiel mit den zentrierten Elementen angezeigt werden, basierend auf der CSS-Datei, die Sie erstellt haben:

    ![Webbrowser mit Seite für die Registrierung/Anmeldung mit benutzerdefinierten Benutzeroberflächenelementen](./media/tutorial-customize-ui/run-now.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen von Dateien für die Benutzeroberflächenanpassung
> * Aktualisieren des Benutzerflows für die Verwendung von Dateien
> * Testen der angepassten Benutzeroberfläche

> [!div class="nextstepaction"]
> [Anpassen der Benutzeroberfläche in Azure Active Directory B2C](customize-ui-with-html.md)
