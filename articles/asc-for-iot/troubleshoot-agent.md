---
title: Felsöka start av säkerhets agent (Linux)
description: Felsöka arbetet med Azure Security Center för IoT-säkerhetsagenter för Linux.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: mlottner
ms.openlocfilehash: 935a99dd34b0a4e3d4970e8d91f9332d2bc1489a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81310557"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>Felsöknings guide för säkerhetsagent (Linux)

Den här artikeln förklarar hur du löser potentiella problem i Start processen för säkerhets agenten.

Azure Security Center för IoT-agenten själv startar omedelbart efter installationen. Agentens start process omfattar läsning av lokal konfiguration, anslutning till Azure-IoT Hub och hämtning av en fjärran sluten dubbel konfiguration. Fel i något av de här stegen kan orsaka att säkerhets agenten Miss lyckas.

I den här fel söknings guiden får du lära dig att:

> [!div class="checklist"]
> * Verifiera om säkerhets agenten körs
> * Hämta säkerhets agent fel
> * Förstå och åtgärda säkerhets agent fel

## <a name="validate-if-the-security-agent-is-running"></a>Verifiera om säkerhets agenten körs

1. För att verifiera att säkerhets agenten körs väntar du några minuter efter att du har installerat agenten och kör följande kommando.
     <br>

    **C-agent**

    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```

    **C#-agent**

    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```

1. Om kommandot returnerar en tom rad kunde säkerhets agenten inte starta korrekt.

## <a name="force-stop-the-security-agent"></a>Framtvinga stopp av säkerhets agenten

I de fall där säkerhets agenten inte kan starta stoppar du agenten med följande kommando och fortsätter sedan till tabellen nedan:

```bash
systemctl stop ASCIoTAgent.service
```

## <a name="get-security-agent-errors"></a>Hämta säkerhets agent fel

1. Hämta säkerhets agent fel genom att köra följande kommando:

    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```

1. Kommandot Get Security Agent Error hämtar alla loggar som skapats av Azure Security Center för IoT-agenten. Använd följande tabell för att förstå felen och vidta lämpliga åtgärder för reparation.

> [!Note]
> Fel loggarna visas i kronologisk ordning. Se till att anteckna tidsstämpeln för varje fel för att hjälpa till med reparationen.

## <a name="restart-the-agent"></a>Starta om agenten

1. När du har hittat och korrigerat ett säkerhets agent fel, försök att starta om agenten genom att köra följande kommando.

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. Upprepa föregående process för att hämta stopp och hämta felen om agenten fortfarande kommer att sluta starta processen.

## <a name="understand-security-agent-errors"></a>Förstå säkerhets agent fel

De flesta av säkerhets agent felen visas i följande format:

```
Azure Security Center for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```

| Felkod | Fel under kod | Felinformation | Åtgärda C | Åtgärda C # |
|:-----------|:---------------|:--------|:------------|:------------|
| Lokal konfiguration | Saknad konfiguration | En konfiguration saknas i den lokala konfigurations filen. Fel meddelandet ska ange vilken nyckel som saknas. | Lägg till den saknade nyckeln i/var/LocalConfiguration.json-filen, se [CS-localconfig-Reference](azure-iot-security-local-configuration-c.md) för mer information.| Lägg till den saknade nyckeln i filen General. config. mer information finns i [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) . |
| Lokal konfiguration | Konfiguration av rälsförhöjning | Det går inte att parsa ett konfigurations värde. Fel meddelandet ska ange vilken nyckel nyckeln inte kan parsas. Det går inte att parsa ett konfigurations värde antingen eftersom värdet inte är av den förväntade typen, eller så ligger värdet utanför intervallet. | Korrigera värdet för nyckeln i/var/LocalConfiguration.json-filen så att den matchar LocalConfiguration-schemat, se [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) för mer information. |  Korrigera värdet för nyckeln i filen General. config så att den matchar schemat, se [CS-localconfig-Reference](azure-iot-security-local-configuration-c.md) för mer information.|
| Lokal konfiguration | Filformat | Det gick inte att parsa konfigurations filen. | Konfigurations filen är skadad, ladda ned agenten och installera den igen. | |
| Fjärrkonfiguration | Timeout | Agenten kunde inte hämta azureiotsecurity-modulen dubbla inom timeout-perioden. | Se till att konfiguration av autentisering är korrekt och försök igen. | Agenten kunde inte hämta azureiotsecurity-modulen dubbla inom timeout-perioden. | Se till att konfiguration av autentisering är korrekt och försök igen. |
| Autentisering | Filen finns inte | Filen i den angivna sökvägen finns inte. | Kontrol lera att filen finns på den angivna sökvägen eller gå till filen **LocalConfiguration. JSON** och ändra sökvägen till **sökvägen** . | Kontrol lera att filen finns på den angivna sökvägen eller gå till filen **Authentication. config** och ändra **sökvägen till sökvägen** .|
| Autentisering | Fil behörighet | Agenten har inte behörighet att öppna filen. | Ge **asciotagent** -användaren Läs behörighet för filen på den angivna sökvägen. | Se till att filen är tillgänglig. |
| Autentisering | Filformat | Den aktuella filen har inte rätt format. | Kontrol lera att filen har rätt format. De filtyper som stöds är. pfx och. pem. | Kontrol lera att filen är en giltig certifikat fil. |
| Autentisering | Behörighet saknas | Agenten kunde inte autentisera mot IoT Hub med de angivna autentiseringsuppgifterna. | Verifiera autentiseringsinställningarna i LocalConfiguration-filen, gå igenom konfigurationen för autentisering och kontrol lera att alla detaljer är korrekta. kontrol lera att hemligheten i filen matchar den autentiserade identiteten. | Verifiera autentiseringsinställningarna i Authentication. config, gå igenom konfigurationen för autentisering och se till att all information är korrekt. kontrol lera sedan att hemligheten i filen matchar den autentiserade identiteten.
| Autentisering | Hittades inte | Enheten/modulen hittades. | Verifiera autentisering av autentisering – kontrol lera att värd namnet är rätt, att enheten finns i IoT Hub och har en azureiotsecurity-modul. |  Verifiera autentisering av autentisering – kontrol lera att värd namnet är rätt, att enheten finns i IoT Hub och har en azureiotsecurity-modul. |
| Autentisering | Saknad konfiguration | En konfiguration saknas i filen *Authentication. config* . Fel meddelandet ska ange vilken nyckel som saknas. | Lägg till den saknade nyckeln i filen *LocalConfiguration. JSON* .| Lägg till den saknade nyckeln i filen *Authentication. config.* mer information finns i [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) . |
| Autentisering | Konfiguration av rälsförhöjning | Det går inte att parsa ett konfigurations värde. Fel meddelandet ska ange vilken nyckel nyckeln inte kan parsas. Det går inte att parsa ett konfigurations värde eftersom värdet inte är av den förväntade typen, eller så ligger värdet utanför intervallet. |Korrigera värdet för nyckeln i filen **LocalConfiguration. JSON** . |Korrigera värdet för nyckeln i **Authentication. config** -filen för att matcha schemat, se [CS-localconfig-Reference](azure-iot-security-local-configuration-c.md) för mer information.|
|

## <a name="restart-the-agent"></a>Starta om agenten

1. När du har hittat och korrigerat ett säkerhets agent fel startar du om agenten genom att köra följande kommando:

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. Om det behövs upprepar du de tidigare processerna så att agenten stoppas och felen hämtas om agenten fortsätter att sluta starta processen.

## <a name="next-steps"></a>Nästa steg

- Läs [översikten över](overview.md) Azure Security Center för IoT-tjänsten
- Läs mer om Azure Security Center för IoT- [arkitektur](architecture.md)
- Aktivera Azure Security Center för IoT [-tjänsten](quickstart-onboard-iot-hub.md)
- Läs [vanliga frågor och svar](resources-frequently-asked-questions.md) om tjänsten Azure Security Center for IoT
- Lär dig hur du kommer åt [rå säkerhets data](how-to-security-data-access.md)
- Förstå [rekommendationer](concept-recommendations.md)
- Förstå säkerhets [aviseringar](concept-security-alerts.md)
