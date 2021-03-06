---
title: Etablera äldre enheter med hjälp av symmetriska nycklar – Azure IoT Hub Device Provisioning Service
description: Använda symmetriska nycklar för att etablera äldre enheter med din DPS-instans (Device Provisioning service)
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 4d1a92f3ebf32d2270eb77ec9c79fe860ba090e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75434720"
---
# <a name="how-to-provision-legacy-devices-using-symmetric-keys"></a>Så här etablerar du äldre enheter med symmetriska nycklar

Ett vanligt problem med många äldre enheter är att de ofta har en identitet som består av en enda del av informationen. Den här identitets informationen är vanligt vis en MAC-adress eller ett serie nummer. Äldre enheter kanske inte har något certifikat, TPM eller någon annan säkerhetsfunktion som kan användas för att identifiera enheten på ett säkert sätt. Enhets etablerings tjänsten för IoT Hub innehåller symmetrisk nyckel attestering. Symmetrisk nyckel attestering kan användas för att identifiera en enhet baserat på information som MAC-adressen eller ett serie nummer.

Om du enkelt kan installera en [maskinvaru-säkerhetsmodul (HSM)](concepts-security.md#hardware-security-module) och ett certifikat kan det vara en bättre metod för att identifiera och etablera dina enheter. Eftersom den metoden kan göra det möjligt att kringgå uppdateringen av koden som distribuerats till alla dina enheter och du inte har en hemlig nyckel inbäddad i enhets avbildningen.

Den här artikeln förutsätter att varken HSM eller ett certifikat är ett lämpligt alternativ. Det förutsätts dock att du har en del metod för att uppdatera enhets koden för att använda enhets etablerings tjänsten för att etablera enheterna. 

Den här artikeln förutsätter också att enhets uppdateringen äger rum i en säker miljö för att förhindra obehörig åtkomst till huvud grupp nyckeln eller den härledda enhets nyckeln.

Den här artikeln riktar sig till en Windows-arbetsstation. Du kan dock utföra procedurerna i Linux. Ett Linux-exempel finns i informationen om att [etablera för flera innehavare](how-to-provision-multitenant.md).

> [!NOTE]
> Exemplet som används i den här artikeln är skrivet i C. Det finns också ett [C#-enhets etablering av symmetriskt nyckel exempel](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) tillgängligt. Om du vill använda det här exemplet laddar du ned eller klona [Azure-IoT-samples-csharp-](https://github.com/Azure-Samples/azure-iot-samples-csharp) lagringsplatsen och följer de infogade anvisningarna i exempel koden. Du kan följa anvisningarna i den här artikeln för att skapa en grupp för symmetrisk nyckel registrering via portalen och för att hitta ID-omfånget och de sekundära nycklar som krävs för att köra exemplet. Du kan också skapa enskilda registreringar med hjälp av exemplet.

## <a name="overview"></a>Översikt

Ett unikt registrerings-ID kommer att definieras för varje enhet baserat på information som identifierar enheten. Till exempel MAC-adressen eller ett serie nummer.

En registrerings grupp som använder [symmetrisk nyckel attestering](concepts-symmetric-key-attestation.md) skapas med enhets etablerings tjänsten. Registrerings gruppen kommer att innehålla en huvud nyckel för gruppen. Huvud nyckeln kommer att användas för att Hasha varje unik registrerings-ID för att skapa en unik enhets nyckel för varje enhet. Enheten kommer att använda den härledda enhets nyckeln med sitt unika registrerings-ID för att intyga med enhets etablerings tjänsten och tilldelas till en IoT-hubb.

Enhets koden som visas i den här artikeln följer samma mönster som [snabb starten: etablera en simulerad enhet med symmetriska nycklar](quick-create-simulated-device-symm-key.md). Koden simulerar en enhet med hjälp av ett exempel från [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Den simulerade enheten kommer att intyga med en registrerings grupp i stället för en enskild registrering som visas i snabb starten.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Krav

* [Konfigurations IoT Hub Device Provisioning service har](./quick-setup-auto-provision.md) slutförts med snabb starten för Azure Portal.

Följande förutsättningar gäller för en Windows-utvecklings miljö. För Linux eller macOS, se lämpligt avsnitt i [förbereda utvecklings miljön](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) i SDK-dokumentationen.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 med arbets belastningen ["Skriv bords utveckling med C++"](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) aktiverat. Visual Studio 2015 och Visual Studio 2017 stöds också.

* Senaste versionen av [Git](https://git-scm.com/download/) installerad.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Förbereda en utvecklingsmiljö för Azure IoT C SDK

I det här avsnittet förbereder du en utvecklingsmiljö som används för att skapa [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). 

SDK innehåller exempel koden för den simulerade enheten. Den här simulerade enheten försöker etablera under enhetens startsekvens.

1. Ladda ned [cmake build-systemet](https://cmake.org/download/).

    Det är viktigt att förutsättningarna för Visual Studio (Visual Studio och arbetsbelastningen ”Desktop development with C++” (Skrivbordsutveckling med C++)) är installerade på datorn **innan** installationen av `CMake` påbörjas. När förutsättningarna är uppfyllda och nedladdningen har verifierats installerar du CMake-byggesystemet.

2. Hitta taggnamnet för den [senaste versionen](https://github.com/Azure/azure-iot-sdk-c/releases/latest) av SDK.

3. Öppna en kommandotolk eller Git Bash-gränssnittet. Kör följande kommandon för att klona den senaste versionen av [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen. Använd taggen som du hittade i föregående steg som `-b` parameter värde:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Den här åtgärden kan förväntas ta flera minuter att slutföra.

4. Skapa en `cmake`-underkatalog i rotkatalogen på git-lagringsplatsen och navigera till den mappen. Kör följande kommandon från `azure-iot-sdk-c` katalogen:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Kör följande kommando som skapar en version av SDK:t som är specifik för plattformen i din utvecklingsklient. En Visual Studio-lösning för den simulerade enheten genereras i `cmake`-katalogen. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```
    
    Om `cmake` inte hittar din C++-kompilerare kan du få kompileringsfel när du kör kommandot ovan. Om det händer ska du försöka köra det här kommandot i [kommandotolken i Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    När bygget är klart ser de sista utdataraderna ut ungefär som följande utdata:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```


## <a name="create-a-symmetric-key-enrollment-group"></a>Skapa en grupp för symmetrisk nyckel registrering

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din enhets etablerings tjänst instans.

2. Välj fliken **Hantera registreringar** och klicka sedan på knappen **Lägg till registrerings grupp** överst på sidan. 

3. I **Lägg till registrerings grupp**anger du följande information och klickar på knappen **Spara** .

   - **Grupp namn**: ange **mylegacydevices**.

   - **Attesterings typ**: Välj **symmetrisk nyckel**.

   - **Generera nycklar automatiskt**: Markera den här kryssrutan.

   - **Välj hur du vill tilldela enheter till hubbar**: Välj **statisk konfiguration** så att du kan tilldela till en speciell hubb.

   - **Välj de IoT-hubbar som gruppen kan tilldelas**: Välj ett av dina nav.

     ![Lägg till registrerings grupp för symmetrisk nyckel attestering](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. När du har sparat din registrering genereras **primärnyckeln** och **sekundärnyckel** och läggs till registreringsposten. Din registrerings grupp för symmetrisk nyckel visas som **mylegacydevices** under kolumnen *grupp namn* på fliken *registrerings grupper* . 

    Öppna registreringen och kopiera värdet för din genererade **primärnyckel**. Den här nyckeln är din huvud grupps nyckel.


## <a name="choose-a-unique-registration-id-for-the-device"></a>Välj ett unikt registrerings-ID för enheten

Ett unikt registrerings-ID måste definieras för att identifiera varje enhet. Du kan använda MAC-adressen, serie numret eller någon unik information från enheten. 

I det här exemplet använder vi en kombination av en MAC-adress och ett serie nummer som utgör följande sträng för ett registrerings-ID.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Skapa ett unikt registrerings-ID för din enhet. Giltiga tecken är alfanumeriska bokstäver och bindestreck (-).


## <a name="derive-a-device-key"></a>Härled en enhets nyckel 

Generera enhets nyckeln genom att använda gruppens huvud nyckel för att beräkna en [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) av det unika registrerings-ID: t för enheten och konvertera resultatet till base64-format.

Ta inte med din grupp huvud nyckel i enhets koden.


#### <a name="linux-workstations"></a>Linux-arbetsstationer

Om du använder en Linux-arbetsstation kan du använda OpenSSL för att generera en härledd enhets nyckel som visas i följande exempel.

Ersätt värdet för **Key** med den **primära nyckel** du noterade tidigare.

Ersätt värdet för **REG_ID** med ditt registrerings-ID.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


#### <a name="windows-based-workstations"></a>Windows-baserade arbets stationer

Om du använder en Windows-baserad arbets Station kan du använda PowerShell för att generera en härledd enhets nyckel som visas i följande exempel.

Ersätt värdet för **Key** med den **primära nyckel** du noterade tidigare.

Ersätt värdet för **REG_ID** med ditt registrerings-ID.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


Enheten kommer att använda den härledda enhets nyckeln med ditt unika registrerings-ID för att utföra symmetrisk nyckel attestering med registrerings gruppen under etableringen.



## <a name="create-a-device-image-to-provision"></a>Skapa en enhets avbildning för att etablera

I det här avsnittet ska du uppdatera ett etablerings exempel med namnet **test\_dev\_client\_-exempel** som finns i Azure IoT C SDK som du har skapat tidigare. 

Den här exempel koden simulerar en enhets startsekvens som skickar etablerings förfrågan till din enhets etablerings tjänst instans. Startsekvensen gör att enheten identifieras och tilldelas IoT-hubben som du konfigurerade i registrerings gruppen.

1. I Azure-portalen väljer du fliken **Översikt** för enhetsetableringstjänsten och noterar värdet för **_ID-omfång_**.

    ![Extrahera information om enhetsetableringstjänstens slutpunkt från bladet på portalen](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Öppna lösnings filen **azure_iot_sdks. SLN** som genererades genom att köra cmake tidigare i Visual Studio. Lösningsfilen bör vara på följande plats:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. I fönstret *Solution Explorer* i Visual Studio går du till mappen **Provision (Etablera)\_Exempel**. Expandera exempelprojektet som heter **prov\_dev\_client\_sample**. Expandera **Källfiler** och öppna **prov\_dev\_client\_sample.c**.

4. Hitta konstanten `id_scope` och ersätt värdet med ditt värde för **ID-omfång** som du kopierade tidigare. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Hitta definitionen för funktionen `main()` i samma fil. Kontrollera att variabeln `hsm_type` är inställd på `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` enligt nedan:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Leta upp anropet till `prov_dev_set_symmetric_key_info()` i **prov\_dev\_client\_sample.c** som har kommenterats bort.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Ta bort kommentarer till funktions anropet och ersätt plats hållarnas värden (inklusive vinkelparenteser) med det unika registrerings-ID: t för din enhet och den härledda enhets nyckeln som du skapade.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6", "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=");
    ```
   
    Spara filen.

7. Högerklicka på projektet **prov\_dev\_client\_sample** och välj **Set as Startup Project** (Ange som startprojekt). 

8. På Visual Studio-menyn väljer du **Felsök** > **Start utan fel sökning** för att köra lösningen. I meddelandet för att omkompilera projektet klickar du på **Ja**, för att omkompilera projektet innan du kör.

    Följande utdata är ett exempel på när den simulerade enheten lyckas med starten och ansluter till etableringstjänstinstansen för att tilldelas en IoT-hubb:

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

    Press enter key to exit:
    ```

9. I portalen navigerar du till den IoT-hubb som din simulerade enhet har tilldelats och klickar på fliken **IoT-enheter** . Vid lyckad etablering av den simulerade till hubben visas dess enhets-ID på bladet **IoT-enheter** med *status* **aktive rad**. Du kan behöva klicka på knappen **Uppdatera** längst upp. 

    ![Enheten är registrerad på IoT-hubben](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>Säkerhets problem

Tänk på att detta lämnar den härledda enhets nyckeln som ingår som en del av avbildningen, vilket inte är en rekommenderad säkerhets rutin. Detta är en orsak till att säkerhet och enkel användning är kompromisser. 





## <a name="next-steps"></a>Nästa steg

* Mer information om hur du reetablerar finns i [IoT Hub metoder för att etablera enheter](concepts-device-reprovision.md) 
* [Snabbstart: Etablera en simulerad enhet med symmetriska nycklar](quick-create-simulated-device-symm-key.md)
* Mer information om hur du avetablerar [enheter finns i så här avetablerar du enheter som tidigare var automatiskt etablerade](how-to-unprovision-devices.md) 











