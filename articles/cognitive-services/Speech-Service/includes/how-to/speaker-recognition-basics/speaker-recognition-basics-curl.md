---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 3a32946e10e8a81394b54fc44e4c92e8625e7ad6
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015477"
---
In diesem Schnellstart erlernen Sie grundlegende Entwurfsmuster für die Sprechererkennung mit dem Sprach-SDK, einschließlich:

* Textabhängige und textunabhängige Überprüfung
* Sprecheridentifikation zum Identifizieren eines Stimmbeispiels unter einer Gruppe von Stimmen
* Löschen von Stimmenprofilen

Einen Überblick über die Konzepte der Spracherkennung finden Sie im Artikel [Was ist der Azure-Sprechererkennungsdienst?](../../../speaker-recognition-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird vorausgesetzt, dass Sie über ein Azure-Konto und über ein Abonnement für den Speech-Dienst verfügen. Falls nicht, können Sie [den Speech-Dienst kostenlos testen](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Sprechererkennung wird derzeit *nur* in Azure Speech-Ressourcen unterstützt, die in der Region `westus` erstellt wurden.

## <a name="text-dependent-verification"></a>Textabhängige Überprüfung

Mit der Sprecherüberprüfung wird bestätigt, dass ein Sprecher mit einer bekannten oder **registrierten** Stimme übereinstimmt. Der erste Schritt ist das **Registrieren** eines Stimmenprofils, damit der Dienst über etwas verfügt, mit dem zukünftige Stimmbeispiele verglichen werden können. In diesem Beispiel registrieren Sie das Profil mit einer **textabhängigen** Strategie, bei der eine bestimmte Passphrase erforderlich ist, die sowohl für die Registrierung als auch für die Überprüfung verwendet wird. Eine Liste mit den unterstützten Passphrasen finden Sie in den [Referenzdokumenten](/rest/api/speakerrecognition/).

[Erstellen Sie zunächst ein Stimmenprofil](/rest/api/speakerrecognition/verification/textdependent/createprofile). Sie müssen den Abonnementschlüssel und die Region Ihres Speech-Diensts in jeden cURL-Befehl in diesem Artikel einfügen.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile":::

Es gibt drei Typen von Stimmenprofil:

- Textabhängige Überprüfung
- Textunabhängige Überprüfung
- Textunabhängige Identifikation

In diesem Fall erstellen Sie ein textabhängiges Stimmenprofil. Sie sollten die unten angegebene Antwort erhalten.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile_response":::

Als Nächstes [registrieren Sie das Stimmenprofil](/rest/api/speakerrecognition/verification/textdependent/createenrollment). Geben Sie für den Parameterwert `--data-binary` eine Audiodatei auf Ihrem Computer an, die eine der unterstützten Passphrasen enthält, z. B. „My voice is my passport, verify me“ (Meine Stimme ist mein Pass, überprüfen Sie mich). Sie können eine Audiodatei dieser Art mit einer App wie [Windows-Sprachrekorder](https://www.microsoft.com/p/windows-voice-recorder/9wzdncrfhwkn?activetab=pivot:overviewtab) aufzeichnen, oder Sie können sie per [Sprachsynthese](../../../index-text-to-speech.yml) generieren.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll":::

Sie sollten die unten angegebene Antwort erhalten.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_1":::

Mit dieser Antwort wird Ihnen mitgeteilt, dass Sie zwei weitere Audiobeispiele registrieren müssen.

Nachdem Sie insgesamt drei Audiobeispiele registriert haben, sollten Sie die unten angegebene Antwort erhalten.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_2":::

Jetzt können Sie [ein Audiobeispiel anhand des Stimmenprofils verifizieren](/rest/api/speakerrecognition/verification/textdependent/verifyprofile). Dieses Audiobeispiel sollte die gleiche Passphrase wie die Beispiele enthalten, die Sie zum Registrieren des Stimmenprofils verwendet haben.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify":::

Sie sollten die unten angegebene Antwort erhalten.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify_response":::

`Accept` bedeutet, dass die Passphrase übereinstimmt und die Verifizierung erfolgreich war. Die Antwort enthält auch ein Ähnlichkeitsergebnis im Bereich von 0,0 bis 1,0.

[Löschen Sie abschließend das Stimmenprofil](/rest/api/speakerrecognition/verification/textdependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_delete_profile":::

Es wird keine Antwort zurückgegeben.

## <a name="text-independent-verification"></a>Textunabhängige Überprüfung

Dies unterscheidet die **textabhängige** Überprüfung von der **textunabhängigen** Überprüfung:

* Hierbei ist es nicht erforderlich, dass eine bestimmte Passphrase gesprochen wird, sondern es können beliebige Wörter sein.
* Es sind nicht drei Audiobeispiele erforderlich, aber es *müssen* insgesamt 20 Sekunden an Audiodaten vorhanden sein.

[Erstellen Sie zunächst ein textunabhängiges Verifizierungsprofil](/rest/api/speakerrecognition/verification/textindependent/createprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile":::

Sie sollten die unten angegebene Antwort erhalten.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile_response":::

[Registrieren Sie als Nächstes das Stimmenprofil](/rest/api/speakerrecognition/verification/textindependent/createenrollment). Anstatt drei Audiobeispiele zu übermitteln, müssen Sie auch hier Audiobeispiele übermitteln, die insgesamt 20 Sekunden an Audiodaten enthalten.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll":::

Nachdem Sie genügend Audiobeispiele übermittelt haben, sollten Sie die unten angegebene Antwort erhalten.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll_response":::

Jetzt können Sie [ein Audiobeispiel anhand des Stimmenprofils verifizieren](/rest/api/speakerrecognition/verification/textindependent/verifyprofile). Auch dieses Audiobeispiel muss keine Passphrase enthalten. Es kann beliebige Sprachdaten enthalten, solange insgesamt vier Sekunden an Audiodaten vorhanden sind.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify":::

Sie sollten die unten angegebene Antwort erhalten.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify_response":::

`Accept` bedeutet, dass die Verifizierung erfolgreich war. Die Antwort enthält auch ein Ähnlichkeitsergebnis im Bereich von 0,0 bis 1,0.

[Löschen Sie abschließend das Stimmenprofil](/rest/api/speakerrecognition/verification/textindependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_delete_profile":::

Es wird keine Antwort zurückgegeben.

## <a name="speaker-identification"></a>Sprecheridentifikation

Anhand der Sprecheridentifikation wird bestimmt, **welche** Stimme aus einer bestimmten Gruppe registrierter Stimmen spricht. Der Prozess ähnelt der **textunabhängigen Überprüfung**, wobei der Hauptunterschied darin besteht, dass eine Überprüfung anhand mehrerer Stimmenprofile gleichzeitig erfolgen kann, anstatt nur anhand eines einzelnen Profils.

[Erstellen Sie zunächst ein Profil für die textunabhängige Identifikation](/rest/api/speakerrecognition/identification/textindependent/createprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile":::

Sie sollten die unten angegebene Antwort erhalten.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile_response":::

Als Nächstes [registrieren Sie das Stimmenprofil](/rest/api/speakerrecognition/identification/textindependent/createenrollment). Auch hier müssen Sie Audiobeispiele übermitteln, die insgesamt 20 Sekunden an Audiodaten enthalten. Diese Beispiele müssen keine Passphrase enthalten.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll":::

Nachdem Sie genügend Audiobeispiele übermittelt haben, sollten Sie die unten angegebene Antwort erhalten.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll_response_2":::

Nun können Sie [ein Audiobeispiel anhand des Stimmenprofils identifizieren](/rest/api/speakerrecognition/identification/textindependent/identifysinglespeaker). Der Identifizierungsbefehl akzeptiert eine Liste mit Kommas als Trennzeichen, die die möglichen Stimmenprofil-IDs enthält. In diesem Fall übergeben Sie einfach die ID des Stimmenprofils, das Sie zuvor erstellt haben. Wenn Sie möchten, können Sie aber auch mehrere Stimmenprofil-IDs übergeben, wobei jedes Stimmenprofil mit Audiobeispielen einer anderen Stimme registriert wird.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify":::

Sie sollten die unten angegebene Antwort erhalten.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify_response":::

Die Antwort enthält die ID des Stimmenprofils, das die größte Übereinstimmung mit dem von Ihnen übermittelten Audiobeispiel aufweist. Darüber hinaus enthält sie eine Liste mit Kandidaten für Stimmenprofile, die nach Ähnlichkeit sortiert sind.

[Löschen Sie abschließend das Stimmenprofil](/rest/api/speakerrecognition/identification/textindependent/deleteprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_delete_profile":::

Es wird keine Antwort zurückgegeben.