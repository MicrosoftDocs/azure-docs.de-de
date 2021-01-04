---
title: store-sendgrid-java-how-to-send-email-example
description: Senden von E-Mails mit SendGrid aus Java in einer Azure-Bereitstellung. Die resultierende Anwendung fordert den Benutzer auf, E-Mail-Werte einzugeben.
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: af096a73-6985-4350-92e4-ce1722c8d72f
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com
ms.custom: devx-track-java
ms.openlocfilehash: 57daf9170a7f97c6c141532dafe759cde9d1c77b
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95519063"
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Senden von E-Mails mit SendGrid aus Java in einer Azure-Bereitstellung
Das folgende Beispiel zeigt, wie Sie mit SendGrid E-Mails von einer in Azure gehosteten Webseite senden können. Die Anwendung fragt den Benutzer nach Werten für die E-Mail, wie im folgenden Screenshot gezeigt.

![E-Mail-Formular][emailform]

Im folgenden Screenshot sehen Sie ein Beispiel für die resultierende E-Mail.

![E-Mail-Nachricht][emailsent]

Sie benötigen Folgendes, um den Code in diesem Artikel ausführen zu können:

1. Rufen Sie die „javax.mail“-JARs auf, z.b. von <https://www.oracle.com/technetwork/java/javamail/index.html>.
2. Fügen Sie die JARs zu Ihrem Java-Buildpfad hinzu.
3. Falls Sie Eclipse für diese Java-Anwendung verwenden, können Sie die SendGrid-Bibliotheken mit der Bereitstellungs-Assembly-Funktion von Eclipse in Ihre Bereitstellungsdatei (WAR) einbinden. Falls Sie Eclipse für diese Java-Anwendung nicht verwenden, müssen Sie sicherstellen, dass die Bibliotheken in derselben Azure-Rolle enthalten sind wie Ihre Java-Anwendung und dass Sie diese zum Classpath Ihrer Anwendung hinzugefügt haben.

Außerdem benötigen Sie einen Benutzernamen und ein Kennwort für SendGrid, um E-Mails zu verschicken. Hinweise zu ersten Schritten mit SendGrid finden Sie unter [Senden von E-Mails mit SendGrid aus Java](store-sendgrid-java-how-to-send-email.md).

Außerdem sollten Sie sich mit den Informationen unter [Erstellen einer Hallo-Welt-Anwendung für Azure in Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app?view=azure-java-stable)oder mit anderen Techniken zum Hosten von Java-Anwendungen in Azure vertraut machen, falls Sie Eclipse nicht verwenden.

## <a name="create-a-web-form-for-sending-email"></a>Erstellen eines Web-Formulars für den E-Mail-Versand
Der folgende Code erstellt ein Web-Formular zur Eingabe der Benutzerdaten für den E-Mail-Versand. Die JSP-Datei für diesen Inhalt heißt **emailform.jsp**.

```html
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Email form</title>
</head>
<body>
  <p>Fill in all fields and click <b>Send this email</b>.</p>
  <br/>
  <form action="sendemail.jsp" method="post">
    <table>
      <tr>
        <td>To:</td>
        <td><input type="text" size=50 name="emailTo">
        </td>
      </tr>
      <tr>
        <td>From:</td>
        <td><input type="text" size=50 name="emailFrom">
        </td>
      </tr>
      <tr>
        <td>Subject:</td>
        <td><input type="text" size=100 name="emailSubject" value="My email subject">
        </td>
      </tr>
      <tr>
        <td>Text:</td>
        <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
        </td>
      </tr>
      <tr>
        <td>SendGrid user name:</td>
        <td><input type="text" name="sendGridUser">
        </td>
      </tr>
      <tr>
        <td>SendGrid password:</td>
        <td><input type="password" name="sendGridPassword">
        </td>
      </tr>
      <tr>
        <td colspan=2><input type="submit" value="Send this email">
        </td>
      </tr>
    </table>
  </form>
  <br/>
</body>
</html>
```

## <a name="create-the-code-to-send-the-email"></a>Erstellen des Codes für den E-Mail-Versand
Der folgende Code wird aufgerufen wenn der Benutzer das Formular in emailform.jsp abschließt, generiert die E-Mail-Nachricht und sendet sie. Die JSP-Datei für diesen Inhalt heißt **sendemail.jsp**.

```java
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Email processing happens here</title>
</head>
<body>
    <b>This is my send mail page.</b><p/>
  <%

  final String sendGridUser = request.getParameter("sendGridUser");
  final String sendGridPassword = request.getParameter("sendGridPassword");

  class SMTPAuthenticator extends Authenticator
  {
    public PasswordAuthentication getPasswordAuthentication()
    {
        String username = sendGridUser;
        String password = sendGridPassword;

        return new PasswordAuthentication(username, password);   
    }
  }
  try
  {

      // The SendGrid SMTP server.
      String SMTP_HOST_NAME = "smtp.sendgrid.net";

      Properties properties;

      properties = new Properties();

      // Specify SMTP values.
      properties.put("mail.transport.protocol", "smtp");
      properties.put("mail.smtp.host", SMTP_HOST_NAME);
      properties.put("mail.smtp.port", 587);
      properties.put("mail.smtp.auth", "true");

      // Display the email fields entered by the user. 
      out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
      out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
      out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
      out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");

      // Create the authenticator object.
      Authenticator authenticator = new SMTPAuthenticator();

      // Create the mail session object.
      Session mailSession;
      mailSession = Session.getDefaultInstance(properties, authenticator);

      // Display debug information to stdout, useful when using the
      // compute emulator during development.
      mailSession.setDebug(true);

      // Create the message and message part objects.
      MimeMessage message;
      Multipart multipart;
      MimeBodyPart messagePart; 

      message = new MimeMessage(mailSession);

      multipart = new MimeMultipart("alternative");
      messagePart = new MimeBodyPart();
      messagePart.setContent(request.getParameter("emailText"), "text/html");
      multipart.addBodyPart(messagePart);            

      // Specify the email To, From, Subject and Content. 
      message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
      message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
      message.setSubject(request.getParameter("emailSubject")); 
      message.setContent(multipart);

      // Uncomment the following if you want to add a footer.
      // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");

      // Uncomment the following if you want to enable click tracking.
      // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");

      Transport transport;
      transport = mailSession.getTransport();
      // Connect the transport object.
      transport.connect();
      // Send the message.
      transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
      // Close the connection.
      transport.close();

    out.println("<p>Email processing completed.</p>");

  }
  catch (Exception e)
  {
      out.println("<p>Exception encountered: " + 
                        e.getMessage()     +
                        "</p>");   
  }
%>

</body>
</html>
```

„emailform.jsp“ sendet die E-Mail und gibt ein Ergebnis an den Benutzer aus, wie im folgenden Screenshot gezeigt:

![Ergebnis des E-Mail-Versands][emailresult]

## <a name="next-steps"></a>Nächste Schritte
Stellen Sie Ihre Anwendung im Serveremulator bereit, und führen Sie „emailform.jsp“ in einem Browser aus. Füllen Sie das Formular aus, klicken Sie auf **Send this email**, und sehen Sie sich das Ergebnis in „sendemail.jsp“ an.

Dieser Code zeigt, wie Sie SendGrid mit Java in Azure verwenden können. Bevor Sie dieses Beispiel in einer Produktionsumgebung bereitstellen, sollten Sie einige Funktionen zur Fehlerbehandlung oder andere Features hinzufügen. Beispiel: 

* Anstelle eines Web-Formulars könnten Sie auch Azure-Speicher-Blobs oder SQL Database zum Speichern von E-Mail-Adressen und -Nachrichten verwenden. Weitere Informationen zur Verwendung von Azure-Speicher-Blobs in Java finden Sie unter [Verwenden des Blob-Speicherdiensts in Java](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). Informationen zur Verwendung von SQL Database in Java finden Sie unter [Verwenden von SQL Database in Java](./azure-sql/database/connect-query-java.md).
* Weitere Informationen zur Verwendung von SendGrid in Java finden Sie unter [Senden von E-Mails mit SendGrid aus Java](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg