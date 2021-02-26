---
title: Tätigen eines Telefonanrufs über Twilio (Java) | Microsoft Docs
description: Erfahren Sie, wie Sie mithilfe von Twilio in einer Java-Anwendung in Azure einen Anruf von einer Webseite tätigen.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.custom: devx-track-java
ms.openlocfilehash: fdaa2eeef58599cbff8dbf7daa34f9a30f31b9cb
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548759"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Tätigen eines Telefonanrufs mithilfe von Twilio in einer Java-Anwendung auf Azure
Das folgende Beispiel zeigt, wie Sie von einer in Azure gehosteten Webseite einen Anruf über Twilio tätigen können. Die Anwendung fragt den Benutzer nach Werten für den Telefonanruf, wie im folgenden Screenshot gezeigt.

![Azure-Anrufformular mit Twilio und Java][twilio_java]

Sie benötigen Folgendes, um den Code in diesem Artikel ausführen zu können:

1. Ein Twilio-Konto und ein Authentifizierungs-Token. Sie können Sie die Preise von Twilio unter [https://www.twilio.com/pricing][twilio_pricing] einsehen. Unter [https://www.twilio.com/try-twilio][try_twilio] können Sie sich anmelden. Informationen zu der von Twilio bereitgestellten API finden Sie unter [https://www.twilio.com/api][twilio_api].
2. Laden Sie das Twilio-JAR herunter. Unter [https://github.com/twilio/twilio-java][twilio_java_github] können Sie die GitHub-Quellen herunterladen und Ihr eigenes JAR erstellen oder ein bereits erstelltes JAR herunterladen (mit oder ohne Abhängigkeiten).
   Der Code in diesem Beispiel wurde mit dem vorkompilierten TwilioJava-3.3.8-with-dependencies-Jar geschrieben.
3. Fügen Sie die JAR-Datei zu Ihrem Buildpfad hinzu.
4. Falls Sie Eclipse für diese Java-Anwendung verwenden, binden Sie das Twilio-JAR mit der Bereitstellungs-Assembly-Funktion von Eclipse in Ihre Bereitstellungsdatei (WAR) ein. Falls Sie Eclipse für diese Java-Anwendung nicht verwenden, müssen Sie sicherstellen, dass das Twilio-JAR in derselben Azure-Rolle enthalten ist wie Ihre Java-Anwendung und dass Sie diese zum Classpath Ihrer Anwendung hinzugefügt haben.
5. Stellen Sie sicher, dass Ihr cacerts-Zertifikatspeicher das Zertifikat der Equifax-Zertifizierungsstelle mit dem MD5-Fingerabdruck 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 enthält (die Seriennummer ist 35:DE:F4:CF und der SHA1-Fingerabdruck ist D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Dies ist das Zertifizierungsstellenzertifikat für den [https://api.twilio.com][twilio_api_service]-Dienst, der beim Verwenden der Twilio-APIs aufgerufen wird.

Außerdem sollten Sie sich mit den Informationen unter [Erstellen einer Hello World-Anwendung mithilfe des Azure-Toolkits für Eclipse][azure_java_eclipse_hello_world] oder mit anderen Techniken zum Hosten von Java-Anwendungen in Azure vertraut machen, falls Sie Eclipse nicht verwenden.

## <a name="create-a-web-form-for-making-a-call"></a>Erstellen eines Webformulars für den Anruf
Der folgende Code erstellt ein Web-Formular zur Eingabe der Benutzerdaten für den Anruf. Für dieses Beispiel wurde ein neues dynamisches Webprojekt mit dem Namen **TwilioCloud** erstellt und **callform.jsp** als JSP-Datei hinzugefügt.

```jsp
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
        <title>Automated call form</title>
    </head>
    <body>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <br/>
        <form action="makecall.jsp" method="post">
            <table>
                <tr>
                    <td>To:</td>
                    <td><input type="text" size=50 name="callTo" value="" />
                    </td>
                </tr>
                <tr>
                    <td>From:</td>
                    <td><input type="text" size=50 name="callFrom" value="" />
                    </td>
                </tr>
                <tr>
                    <td>Call message:</td>
                    <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
                    </td>
                </tr>
                <tr>
                    <td colspan=2><input type="submit" value="Make this call" />
                    </td>
                </tr>
            </table>
        </form>
        <br/>
    </body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>Erstellen des Codes für den Anruf
Der folgende Code wird aufgerufen wenn der Benutzer das Formular aus callform.jsp abschließt und generiert die Anrufnachricht und führt den Anruf aus. Für dieses Beispiel wurde die Datei **makecall.jsp** genannt und zum **TwilioCloud**-Projekt hinzugefügt. (Ersetzen Sie die Platzhalterwerte **accountSID** und **authToken** im folgenden Code durch Ihr Twilio-Konto und Ihr Authentifizierungs-Token.)

```jsp
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
import="java.util.*"
import="com.twilio.*"
import="com.twilio.sdk.*"
import="com.twilio.sdk.resource.factory.*"
import="com.twilio.sdk.resource.instance.*"
pageEncoding="ISO-8859-1" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
        <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
<%
try 
{
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Instantiate an instance of the Twilio client.     
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to retrieve the CallFactory.
    Account account = client.getAccount();

    // Display the client endpoint. 
    out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");

    // Display the API version.
    String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
    out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

    // Retrieve the values entered by the user.
    String callTo = request.getParameter("callTo");  
    // The Outgoing Caller ID, used for the From parameter,
    // must have previously been verified with Twilio.
    String callFrom = request.getParameter("callFrom");
    String userText = request.getParameter("callText");

    // Replace spaces in the user's text with '%20', 
    // to make the text suitable for a URL.
    userText = userText.replace(" ", "%20");

    // Create a URL using the Twilio message and the user-entered text.
    String Url="https://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=" + userText;

    // Display the message URL.
    out.println("<p>");
    out.println("The URL is " + Url);
    out.println("</p>");

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", callFrom);
    params.put("To", callTo);
    params.put("Url", Url);

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);
    out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
} 
catch (TwilioRestException e) 
{
    out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
    out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
}
catch (Exception e) 
{
    out.println("<p>Exception encountered: " + e.getMessage() + "");
    out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
}
%>
    </body>
</html>
```

makecall.jsp führt nicht nur den Anruf aus, sondern gibt auch noch den Twilio-Endpunkt, die API-Version und den Anrufstatus aus. Der folgende Screenshot zeigt ein Beispiel:

![Azure-Anrufantwort mit Twilio und Java][twilio_java_response]

## <a name="run-the-application"></a>Ausführen der Anwendung
Es folgen nun die allgemeinen Schritte zum Ausführen Ihrer Anwendung. Details zu diesen Schritten finden Sie unter [Erstellen einer Hello World-Anwendung mithilfe des Azure-Toolkits für Eclipse][azure_java_eclipse_hello_world].

1. Exportieren Sie Ihr TwilioCloud-WAR in den **approot** -Ordner in Azure. 
2. Ändern Sie **startup.cmd** , um Ihr TwilioCloud-WAR zu entzippen.
3. Kompilieren Sie Ihre Anwendung für den Serveremulator.
4. Starten Sie Ihre Bereitstellung im Serveremulator.
5. Öffnen Sie einen Browser, und führen Sie `http://localhost:8080/TwilioCloud/callform.jsp` aus.
6. Geben Sie Werte in das Formular ein, klicken Sie auf **Make this call** und sehen Sie sich die Ergebnisse in makecall.jsp an.

Wenn Sie die Anwendung in Azure bereitstellen möchten, kompilieren Sie sie für die Cloudbereitstellung neu, laden sie in Azure hoch und öffnen „http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp“ im Browser (ersetzen Sie *your_hosted_name* durch den entsprechenden Wert).

## <a name="next-steps"></a>Nächste Schritte
Dieser Code demonstriert die allgemeinen Funktionen für die Verwendung von Twilio mit Java in Azure. Bevor Sie dieses Beispiel in einer Produktionsumgebung bereitstellen, sollten Sie einige Funktionen zur Fehlerbehandlung oder andere Features hinzufügen. Beispiel:

* Anstelle eines Web-Formulars könnten Sie Azure-Speicher-Blobs oder eine SQL-Datenbank zum Speichern von Telefonnummern und Anruftexten verwenden. Weitere Informationen zur Verwendung von Azure-Speicher-Blobs in Java finden Sie unter [Verwenden des Blob-Speicherdiensts in Java][howto_blob_storage_java]. 
* Sie können **RoleEnvironment.getConfigurationSettings** verwenden, um die Twilio-Konto-ID und das Authentifizierungs-Token aus den Konfigurationseinstellungen Ihrer Anwendung abzurufen, anstelle diese Werte in makecall.jsp fest einzuprogrammieren. Weitere Informationen zur **RoleEnvironment**-Klasse finden Sie unter [Verwenden der Azure-Dienstlaufzeitbibliothek in JSP][azure_runtime_jsp].
* Der Code von „makecall.jsp“ weist die von Twilio bereitgestellte URL [https://twimlets.com/message][twimlet_message_url] der **Url**-Variablen zu. Diese URL gibt eine Antwort in der Twilio Markup Language (TwiML), die Twilio mitteilt, wie mit dem Anruf verfahren werden soll. Das zurückgegebene TwiML kann z. B. ein **&lt;Say&gt;** -Verb enthalten, das dazu führt, dass dem Anrufempfänger ein bestimmter Text vorgesprochen wird. Anstelle der von Twilio bereitgestellten URL können Sie auch mit einem eigenen Dienst auf die Twilio-Anfrage antworten. Weitere Informationen finden Sie unter [Verwenden von Twilio für Telefonie- und SMS-Funktionen in Java][howto_twilio_voice_sms_java]. Weitere Informationen zu TwiML finden Sie unter [https://www.twilio.com/docs/api/twiml][twiml], und weitere Informationen zu **&lt; Say&gt;** und anderen Twilio-Verben finden Sie unter [https://www.twilio.com/docs/api/twiml/say][twilio_say].
* Lesen Sie die Twilio-Sicherheitsrichtlinien unter [https://www.twilio.com/docs/security][twilio_docs_security].

Zusätzliche Informationen zu Twilio finden Sie unter [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Weitere Informationen
* [Verwenden von Twilio für Telefonie- und SMS-Funktionen in Java][howto_twilio_voice_sms_java]

[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: https://github.com/twilio/twilio-java
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[azure_java_eclipse_hello_world]: /java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: https://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: https://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: /previous-versions/azure/hh690948(v=azure.100)
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
