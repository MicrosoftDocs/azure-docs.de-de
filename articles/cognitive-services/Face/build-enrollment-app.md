---
title: Erstellen einer Registrierungs-App für Android mit React
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Ihre Entwicklungsumgebung einrichten und eine Registrierungs-App für Gesichtserkennung bereitstellen, um die Zustimmung von Kunden zu erhalten.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: 085dd18214f795566669fb862bba63b67eb9115d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350363"
---
# <a name="build-an-enrollment-app-for-android-with-react"></a>Erstellen einer Registrierungs-App für Android mit React

In dieser Anleitung lernen Sie die ersten Schritte mit der Registrierungsbeispielanwendung für Gesichtserkennung kennen. Die App veranschaulicht bewährte Methoden, um eine aussagekräftige Zustimmung zu der Registrierung von Benutzern bei einem Gesichtserkennungsdienst und zum Abrufen von hoch exakten Gesichtserkennungsdaten zu erhalten. Ein integriertes System könnte eine Registrierungs-App wie diese verwenden, um eine berührungslose Zugriffssteuerung, eine Identitätsprüfung, eine Anwesenheitserfassung, einen Personalisierungskiosk oder eine auf ihren Gesichtserkennungsdaten basierende Identitätsprüfung bereitzustellen.

Wenn die Anwendung gestartet wird, zeigt sie den Benutzern einen detaillierten Zustimmungsbildschirm an. Wenn der Benutzer seine Zustimmung erteilt, fordert die App ihn zur Eingabe eines Benutzernamens und Kennworts auf und erfasst dann ein qualitativ hochwertiges Bild des Gesichts mithilfe der Kamera des Geräts.

Die Registrierungs-Beispiel-App wird mit JavaScript und dem React Native-Framework geschrieben. Sie kann derzeit auf Android-Geräten bereitgestellt werden. Weitere Bereitstellungsoptionen werden in Zukunft verfügbar gemacht.

## <a name="prerequisites"></a>Voraussetzungen 

* Azure-Abonnement ([kostenloses Abonnement erstellen](https://azure.microsoft.com/free/cognitive-services/)).  
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie im Azure-Portal [eine Gesichtserkennungsressource erstellen](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace), um Ihren Schlüssel und Endpunkt zu erhalten. Wählen Sie nach Abschluss der Bereitstellung **Zu Ressource wechseln** aus.  
  * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Gesichtserkennungs-API zu verbinden.  
  * Für lokale Entwicklungs- und Testzwecke können Sie den API-Schlüssel und Endpunkt in die Konfigurationsdatei einfügen. Speichern Sie den API-Schlüssel für die endgültige Bereitstellung an einem sicheren Ort und nie im Code.  

> [!IMPORTANT]
> Diese Abonnementschlüssel werden für den Zugriff auf Ihre Cognitive Service-API verwendet. Geben Sie Ihre Schlüssel nicht weiter. Speichern Sie diese beispielsweise sicher mit Azure Key Vault. Es wird außerdem empfohlen, diese Schlüssel regelmäßig neu zu generieren. Für einen API-Aufruf ist nur ein Schlüssel erforderlich. Beim erneuten Generieren des ersten Schlüssels können Sie den zweiten Schlüssel für kontinuierlichen Zugriff auf den Dienst verwenden.

## <a name="set-up-the-development-environment"></a>Einrichten der Entwicklungsumgebung

1. Klonen Sie das Git-Repository für die [Registrierungs-Beispiel-App](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample).
1. Um Ihre Entwicklungsumgebung einzurichten, befolgen Sie die <a href="https://reactnative.dev/docs/environment-setup"  title=""  target="_blank">React Native-Dokumentation<span class="docon docon-navigate-external x-hidden-focus"></span></a>. Wählen Sie **React Native CLI Schnellstart** als Entwicklungsbetriebssystem und **Android** als Zielbetriebssystem aus. Vervollständigen Sie die Abschnitte **Installieren von Abhängigkeiten** und **Android-Entwicklungsumgebung**.
1. Öffnen Sie die Datei „env.json“ in Ihrem bevorzugten Text-Editor, z. B. [Visual Studio Code](https://code.visualstudio.com/), und fügen Sie Ihren Endpunkt und Schlüssel hinzu. Sie können Ihren Endpunkt und Schlüssel im Azure-Portal auf der Registerkarte **Übersicht** Ihrer Ressource finden. Dieser Schritt dient nur lokalen Testzwecken. Checken Sie nicht Ihren Gesichtserkennungs-API-Schlüssel in Ihrem Remoterepository ein.
1. Führen Sie die App entweder mit dem Android-Emulator für virtuelle Geräte aus Android Studio oder Ihrem eigenen Android-Gerät aus. Um Ihre App auf einem physischen Gerät zu testen, befolgen Sie die relevante <a href="https://reactnative.dev/docs/running-on-device"  title=""  target="_blank">React Native-Dokumentation<span class="docon docon-navigate-external x-hidden-focus"></span></a>.  


## <a name="create-an-enrollment-experience"></a>Erstellen einer Registrierungserfahrung  

Nachdem Sie nun die Registrierungs-Beispiel-App eingerichtet haben, können Sie sie an Ihre eigenen Registrierungsanforderungen anpassen.

Beispielsweise können Sie auf Ihrer Zustimmungsseite situationsabhängige Informationen hinzufügen:

> [!div class="mx-imgBorder"]
> ![App-Zustimmungsseite](./media/enrollment-app/1-consent-1.jpg)

Der Dienst bietet Bildqualitätstests, um Ihnen bei der Entscheidung zu helfen, ob die Qualität des Bilds ausreichend ist, um den Kunden zu registrieren oder die Gesichtserkennung zu versuchen. Diese App veranschaulicht, wie Sie über die Kamera des Geräts auf Frames zugreifen, die Frames mit der höchsten Qualität auswählen und das erkannte Gesicht beim Gesichtserkennungs-API-Dienst registrieren. 

Viele Gesichtserkennungsprobleme werden durch Referenzbilder von niedriger Qualität verursacht. Einige Faktoren, die die Leistung des Modells beeinträchtigen können, sind:
* Gesichtsgröße (Gesichter, die weit von der Kamera entfernt sind)
* Gesichtsausrichtung (geneigte oder von der Kamera abgewendete Gesichter)
* Schlechte Beleuchtungsbedingungen (zu wenig Licht oder Gegenlicht), bei denen das Bild unterbelichtet wird oder zu viel Rauschen auftritt.
* Verdeckung (teilweise verborgene oder verdeckte Gesichter), einschließlich Accessoires wie Hüten oder Brillen mit dickem Gestell.
* Verschwommen/Weichzeichnung (z. B. durch schnelle Bewegung des Gesichts während der Aufnahme des Fotos). 

> [!div class="mx-imgBorder"]
> ![Seite mit Anweisungen für die App-Bilderfassung](./media/enrollment-app/4-instruction.jpg)

Beachten Sie, dass die App auch Funktionen zum Löschen der Registrierung des Benutzers sowie die Option zum erneuten Registrieren bietet.

> [!div class="mx-imgBorder"]
> ![Profilverwaltungsseite](./media/enrollment-app/10-manage-2.jpg)

Um die Funktionalität der App so zu erweitern, dass Sie die gesamte Registrierung abdeckt, lesen Sie die [Übersicht](enrollment-overview.md), wo Sie zusätzliche zu implementierende Funktionen und bewährte Methoden finden.

## <a name="deploy-the-enrollment-app"></a>Bereitstellen der Registrierungs-App

### <a name="android"></a>Android

Stellen Sie zunächst sicher, dass Ihre App für die Produktionsbereitstellung bereit ist: Entfernen Sie alle Schlüssel oder Geheimnisse aus dem App-Code, und stellen Sie sicher, dass Sie die [bewährten Sicherheitsmethoden](../cognitive-services-security.md?tabs=command-line%2ccsharp) befolgt haben.

Wenn Sie bereit sind, Ihre App für die Produktion freizugeben, generieren Sie eine releasebereite APK-Datei, bei der es sich um das Paketdateiformat für Android-Apps handelt. Diese APK-Datei muss mit einem privaten Schlüssel signiert werden. Mit diesem Releasebuild können Sie beginnen, die App direkt an Ihre Geräte zu verteilen. 

Befolgen Sie die Dokumentation zur <a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title="Vorbereiten für das Release"  target="_blank">Vorbereiten für das Release<span class="docon docon-navigate-external x-hidden-focus"></span></a>, um zu erfahren, wie Sie einen privaten Schlüssel generieren, Ihre Anwendung signieren und ein APK-Releasepaket generieren.  

Wenn Sie ein signiertes APK-Paket erstellt haben, finden Sie weitere Informationen zum Freigeben Ihrer App in der Dokumentation <a href="https://developer.android.com/studio/publish"  title="Veröffentlichen Ihrer App"  target="_blank">Veröffentlichen Ihrer App<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

## <a name="next-steps"></a>Nächste Schritte  

In dieser Anleitung haben Sie erfahren, wie Sie Ihre Entwicklungsumgebung einrichten und mit der Registrierungs-Beispiel-App beginnen. Wenn Sie nicht mit React Native vertraut sind, können Sie die dazugehörige [Dokumentation zu den ersten Schritten](https://reactnative.dev/docs/getting-started) lesen, um weitere Hintergrundinformationen zu erhalten. Es kann auch hilfreich sein, sich selbst mit der [Gesichtserkennungs-API](Overview.md) vertraut zu machen. Lesen Sie die anderen Abschnitte der Dokumentation zur Registrierungs-App, bevor Sie mit der Entwicklung beginnen.