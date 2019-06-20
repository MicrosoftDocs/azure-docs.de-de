---
title: Einrichten der zweistufigen Überprüfung – Azure Active Directory | Microsoft-Dokumentation
description: Wenn Ihr Unternehmen Azure Multi-Factor Authentication konfiguriert, werden Sie aufgefordert, sich für die zweistufige Überprüfung anzumelden. Hier erfahren Sie, wie Sie das Verfahren einrichten.
services: active-directory
keywords: Verwenden von Azure Active Directory, Active Directory in der Cloud, Tutorial zu Active Directory
author: eross-msft
manager: daveba
ms.reviewer: richagi
ms.assetid: 46f83a6a-dbdd-4375-8dc4-e7ea77c16357
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2df72d03bae8987de4998276a0be0f3ce1ec0333
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65230044"
---
# <a name="set-up-my-account-for-two-step-verification"></a>Einrichten meines Kontos für die zweistufige Überprüfung
Die zweistufige Überprüfung ist eine zusätzliche Sicherheitsmaßnahme, die zum Schutz Ihres Kontos beiträgt, indem sie den Zugriff durch Unbefugte erschwert. Wenn Sie diesen Artikel lesen, haben Sie wahrscheinlich vom Administrator Ihres Geschäfts-, Schul- oder Unikontos eine E-Mail zum Thema Multi-Factor Authentication erhalten. Oder Sie haben eventuell versucht, sich anzumelden, und eine Nachricht mit der Aufforderung empfangen, eine zusätzliche Sicherheitsüberprüfung einzurichten. Wenn dies der Fall ist, **können Sie sich erst anmelden, nachdem Sie die automatische Registrierung abgeschlossen haben**.

Dieser Artikel unterstützt Sie beim Einrichten Ihres **Geschäfts-, Schul- oder Unikontos**. Wenn Sie die zweistufige Überprüfung für Ihr eigenes persönliches Microsoft-Konto aktivieren möchten, finden Sie entsprechende Informationen unter [Zweistufige Überprüfung](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="set-up-your-account"></a>Einrichten Ihres Kontos

Wenn die Supportabteilung Ihres Unternehmens das Starten mithilfe der zweistufigen Überprüfung erzwingt, wird auf einem Bildschirm Folgendes angezeigt: **Ihr Administrator verlangt, dass Sie dieses Konto für eine zusätzliche Sicherheitsüberprüfung einrichten**:

![Einrichtung](./media/multi-factor-authentication-end-user-first-time/first.png)

Wählen Sie zum Einstieg **Jetzt einrichten** aus.

Wenn bei der Anmeldung kein solcher Bildschirm angezeigt wird, befolgen Sie die Anweisungen in [Verwalten der Einstellungen für die zweistufige Überprüfung](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page), um die Einstellungsseite zu suchen, auf der Sie Ihre Überprüfungsoptionen verwalten können.

## <a name="decide-how-you-want-to-verify-your-sign-ins"></a>Auswählen der Überprüfung von Anmeldungen

Die erste Frage bei der Registrierung betrifft die Methode, mit der wir Kontakt zu Ihnen aufnehmen sollen. Überprüfen Sie die Optionen in der Tabelle, und wechseln Sie mithilfe der Links zu den Einrichtungsschritten für jede Methode.

| Kontaktmethode | BESCHREIBUNG |
| --- | --- |
| [Mobile App](#use-a-mobile-app-as-the-contact-method) |- **Benachrichtigungen zur Überprüfung empfangen** Mit dieser Option wird eine Benachrichtigung an die Authenticator-App auf Ihrem Smartphone oder Tablet gesendet. Überprüfen Sie die Benachrichtigung, und wählen Sie in der App **Authentifizieren** aus, wenn Sie den Zugriff zulassen möchten. Ihr Unternehmen oder Ihre Bildungseinrichtung erfordert möglicherweise die Eingabe einer PIN, bevor Sie sich authentifizieren.<br>- **Überprüfungscode verwenden** In diesem Modus generiert die Authenticator-App einen Überprüfungscode, der alle 30 Sekunden aktualisiert wird. Geben Sie im Anmeldungsfenster den aktuellen Überprüfungscode ein.<br>Die Microsoft Authenticator-App ist für [Android](https://go.microsoft.com/fwlink/?linkid=866594) und [iOS](https://go.microsoft.com/fwlink/?linkid=866594) verfügbar.|
| [Anruf beim Mobiltelefon oder Senden einer SMS](#use-your-mobile-phone-as-the-contact-method) |- **Telefonanruf**: Das Telefon mit der von Ihnen angegebenen Telefonnummer empfängt einen automatisierten Anruf. Nehmen Sie den Anruf an, und drücken Sie die #-TASTE auf der Telefontastatur, um sich zu authentifizieren.<br>- **SMS**: Es wird eine SMS gesendet, die einen Überprüfungscode enthält. Je nachdem, wozu Sie in der SMS aufgefordert werden, antworten Sie auf die SMS, oder geben Sie im Anmeldungsfenster den angegebenen Überprüfungscode ein. |
| [Anruf beim Bürotelefon](#use-your-office-phone-as-the-contact-method) |Das Telefon mit der von Ihnen angegebenen Telefonnummer empfängt einen automatisierten Anruf. Nehmen Sie den Anruf an, und drücken Sie die #-TASTE auf der Telefontastatur, um sich zu authentifizieren. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Verwenden einer mobilen App als Kontaktmethode
Für diese Methode müssen Sie auf Ihrem Smartphone oder Tablet eine Authenticator-App installieren. Die Schritte in diesem Artikel basieren auf der Microsoft Authenticator-App, die für [Android](https://go.microsoft.com/fwlink/?Linkid=825072) und [iOS](https://go.microsoft.com/fwlink/?Linkid=825073) verfügbar ist.

>[!NOTE]
>Sie müssen nicht unbedingt die Microsoft Authenticator-App verwenden. Falls Sie bereits eine andere Authentifikator-App nutzen, können Sie diese weiterverwenden.

1. Wählen Sie in der Dropdownliste **Mobile App** aus.
2. Wählen Sie **Benachrichtigungen zur Überprüfung empfangen** oder **Überprüfungscode verwenden** aus, und wählen Sie dann **Einrichten** aus.

   ![Zusätzliche Sicherheitsüberprüfung, Bildschirm](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)

3. Öffnen Sie auf dem Smartphone oder Tablet die App, und tippen Sie auf **+** , um ein Konto hinzuzufügen. (Wählen Sie auf Android-Geräten die drei Punkte und dann **Konto hinzufügen** aus.)
4. Geben Sie dann an, dass Sie ein Geschäfts-, Schul- oder Unikonto hinzufügen möchten. Der QR-Codescanner auf Ihrem Smartphone wird geöffnet. Wenn die Kamera nicht ordnungsgemäß funktioniert, können Sie die Informationen zu Ihrem Unternehmen manuell eingeben. Weitere Informationen finden Sie unter [Manuelles Hinzufügen eines Kontos](#add-an-account-manually).  
5. Scannen Sie das Bild mit dem QR-Code, das auf dem Bildschirm zum Konfigurieren der mobilen App angezeigt wird.  Klicken Sie auf **Fertig** , um den Bildschirm mit dem QR-Code zu schließen.  

   ![QR-Code, Bildschirm](./media/multi-factor-authentication-end-user-first-time/scan2.png)

6. Wenn die Aktivierung auf dem Telefon abgeschlossen ist, wählen Sie **Nehmen Sie Kontakt mit mir auf**.  Dadurch wird eine Benachrichtigung oder ein Überprüfungscode an Ihr Mobiltelefon gesendet. Wählen Sie **Überprüfen**aus.  
7. Wenn Ihr Unternehmen eine PIN anfordert, um die Überprüfung der Anmeldung zu genehmigen, gegen Sie sie hier ein.

   ![Feld für die Eingabe einer PIN](./media/multi-factor-authentication-end-user-first-time/scan3.png)

8. Tippen Sie nach der PIN-Eingabe auf **Schließen**. Die Überprüfung sollte jetzt erfolgreich abgeschlossen sein.
9. Sie sollten Ihre Mobiltelefonnummer eingeben, falls Sie einmal keinen Zugang zu Ihrer mobilen App mehr haben. Wählen Sie Ihr Land / Ihre Region in der Dropdownliste aus, und geben Sie Ihre Mobiltelefonnummer in das Feld neben dem Namen von Land/Region ein. Klicken Sie auf **Weiter**.
10. Jetzt werden Sie aufgefordert, App-Kennwörter für Nicht-Browser-Apps, z. B. Outlook 2010 oder früher, oder die native E-Mail-App auf Apple-Geräten einzurichten. Der Grund dafür ist, dass einige Apps die zweistufige Überprüfung nicht unterstützen. Wenn Sie solche Apps nicht verwenden, klicken Sie auf **Fertig**, und überspringen Sie die restlichen Schritte.
11. Wenn Sie solche Apps verwenden, kopieren Sie das angegebene App-Kennwort, und fügen Sie es anstelle des regulären Kennworts in die App ein. Sie können das gleiche App-Kennwort für mehrere Apps verwenden. Weitere Informationen finden Sie unter [Hilfe bei App-Kennwörtern].
12. Klicken Sie auf **Fertig**.

### <a name="add-an-account-manually"></a>Manuelles Hinzufügen eines Kontos
Gehen Sie folgendermaßen vor, wenn Sie der mobilen Anwendung manuell ein Konto hinzufügen möchten, statt den QR-Reader zu verwenden.

1. Wählen Sie die Schaltfläche **Konto manuell eingeben** aus.  
2. Geben Sie den Code und die URL ein, der bzw. die auf der Seite mit dem Barcode angegeben ist. Diese Informationen werden in der mobilen App in die Felder **Code** und **URL** eingegeben.

    ![Einrichtung](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. Wenn die Aktivierung abgeschlossen ist, wählen Sie **Nehmen Sie Kontakt mit mir auf**aus. Dadurch wird eine Benachrichtigung oder ein Überprüfungscode an Ihr Mobiltelefon gesendet. Wählen Sie **Überprüfen**aus.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Verwenden des Mobiltelefons als Kontaktmethode
1. Wählen Sie in der Dropdownliste **Telefon für Authentifizierung** aus.  

    ![Einrichtung](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. Wählen Sie Ihr Land / Ihre Region in der Dropdownliste aus, und geben Sie Ihre Mobiltelefonnummer ein.
3. Wählen Sie die gewünschte Methode für das Mobiltelefon aus: SMS oder Anruf.
4. Wählen Sie **Kontaktformular** aus, um Ihre Telefonnummer überprüfen zu lassen. Abhängig vom ausgewählten Modus senden wir Ihnen eine SMS oder rufen Sie an. Befolgen Sie die Anweisungen auf dem Bildschirm, und wählen Sie dann **Überprüfen** aus.
5. Jetzt werden Sie aufgefordert, App-Kennwörter für Nicht-Browser-Apps, z. B. Outlook 2010 oder früher, oder die native E-Mail-App auf Apple-Geräten einzurichten. Der Grund dafür ist, dass einige Apps die zweistufige Überprüfung nicht unterstützen. Wenn Sie solche Apps nicht verwenden, klicken Sie auf **Fertig**, und überspringen Sie die restlichen Schritte.
6. Wenn Sie solche Apps verwenden, kopieren Sie das angegebene App-Kennwort, und fügen Sie es anstelle des regulären Kennworts in die App ein. Sie können das gleiche App-Kennwort für mehrere Apps verwenden. Weitere Informationen finden Sie unter [Hilfe bei App-Kennwörtern].
7. Klicken Sie auf **Fertig**.

## <a name="use-your-office-phone-as-the-contact-method"></a>Verwenden des Bürotelefons als Kontaktmethode
1. Wählen Sie in der Dropdownliste **Bürotelefon** aus.  

    ![Einrichtung](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. Das Feld für die Telefonnummer wird automatisch mit den Kontaktinformationen Ihres Unternehmens ausgefüllt. Wenn die Nummer falsch oder nicht vorhanden ist, bitten Sie den Administrator, Änderungen vorzunehmen.
3. Wählen Sie **Kontaktformular** aus, um Ihre Telefonnummer überprüfen zu lassen, und wir senden einen Anruf an diese Nummer. Befolgen Sie die Anweisungen auf dem Bildschirm, und wählen Sie dann **Überprüfen** aus.
4. Jetzt werden Sie aufgefordert, App-Kennwörter für Nicht-Browser-Apps, z. B. Outlook 2010 oder früher, oder die native E-Mail-App auf Apple-Geräten einzurichten. Der Grund dafür ist, dass einige Apps die zweistufige Überprüfung nicht unterstützen. Wenn Sie solche Apps nicht verwenden, klicken Sie auf **Fertig**, und überspringen Sie die restlichen Schritte.
5. Wenn Sie solche Apps verwenden, kopieren Sie das angegebene App-Kennwort, und fügen Sie es anstelle des regulären Kennworts in die App ein. Sie können das gleiche App-Kennwort für mehrere Apps verwenden. Weitere Informationen finden Sie unter [Welchen Zweck erfüllen App-Kennwörter bei Azure Multi-Factor Authentication?](multi-factor-authentication-end-user-app-passwords.md).
6. Klicken Sie auf **Fertig**.

## <a name="next-steps"></a>Nächste Schritte
* Ändern Sie Ihre bevorzugten Optionen und [verwalten Sie Ihre Einstellungen für die zweistufige Überprüfung](multi-factor-authentication-end-user-manage-settings.md).
* Richten Sie [App-Kennwörter](multi-factor-authentication-end-user-app-passwords.md) für native Geräte-Apps ein, die die zweistufige Überprüfung nicht unterstützen.
* Testen Sie die [Microsoft Authenticator-App](user-help-auth-app-download-install.md) für die schnelle, sichere Authentifizierung, auch wenn kein Mobilfunkempfang möglich ist.
