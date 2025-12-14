# Fortnite Mobile SSL Bypass
A Method to use proxies to be able to play fortnite mobile with fiddler.

## Requirememnts
- A Windows Laptop
- [Voltronite](https://github.com/RazerFrFr/Voltronite) or a Backend that supports mobile well (especially if you're looking to play Ch2+)
- [HxD](https://mh-nexus.de/en/downloads.php?product=HxD20) (To patch libUE4.so or libUnreal.so)
- [Java](https://www.oracle.com/java/technologies/downloads/) 17+ and [apktool](https://apktool.org/) to decompile the apk
- [Uber apk signer](https://github.com/patrickfav/uber-apk-signer) to sign the apk
- [Fiddler Classic](https://www.telerik.com/download/fiddler) to actually redirect

## Step 1: Decompiling the APK
1. Get any version between s6-s21 (s5 and versions between 10.31 to 15.50 wont work) from [uptodown](https://fortnite.en.uptodown.com/android/versions)
2. Create a folder named `Fortnite-SSL-Bypass` and get apktool, uber apk signer jars and the apk to that folder
3. Open a command prompt window and run ```java -jar apktool.jar decode Fortnite.apk -o ./Fortnite```
## Step 2: Patching the APK
1. Go to ```Fortnite/libs/arm64-v8a/``` and open **libUE4.so** if ch1-2 or **libUnreal.so** if ch3+ with **HxD**
2. In HxD make sure the **Byte Sex** is **Little Endian** and then continue
3. Click **Ctrl + F** and go to **Hex-Values Tab** and follow the next step carefully

   - If you chose an APK from **Chapter 1 - Chapter 2** search for these **Hex Patterns**
     ```
     08 01 40 F9 E0 03 1F 2A 1F 01 00 F1 E8 07 9F 1A 68 02 11 39
     ```
     and **Manually Replace Them** with
     ```
     1F 20 03 D5 1F 20 03 D5 1F 20 03 D5 08 00 80 52
     ```

   - If you chose an APK from **Chapter 3 Season 1** to **Chapter 3 Season 3** search for these **Hex Patterns**
     ```
     68 0E 40 F9 F5 03 1F 2A 3F 01 00 F1 E9 07 9F 1A 4A 19 1F 12
     ```
     and **Manually Replace** ```E9 07 9F 1A``` with ```E9 03 1F 2A```

   - Chapter 3 Season 4 and higher are unsupported at the moment...
4. Save the file and close HxD
## Step 3: Building the patched APK and signing it
- Note: if your phone is running android 12+ make sure u set **Target SDK** to **24** if running an older version made before 2020 in `AndroidManifest.xml`.
1. Go back to the **Command Prompt** window and run ```java -jar apktool.jar build ./Fortnite -o ./Fortnite-Patched.apk``` You should see a new apk being made.
2. Now we need to sign the apk to do that run this ```java -jar uber-apk-signer.jar --apks ./Fortnite-Patched.apk```
3. You should see a new signed apk take that apk and install it on your phone.
## Step 4: Fiddler & Redirecting
1. Open Fiddler
2. Go to **Tools/Options** and open **HTTPS** tab
3. Make sure you checked **Decrypt HTTPS Traffic** and trusted **Root Certificate** by clicking **Actions** and **Trust Root Certificate**
4. Go to **Connections** tab and make sure **Allow Remote Connections** is checked
5. Press OK and go to **FiddlerScript** tab and paste this script

    ```
    import Fiddler;

    class Handlers
    {
      static function OnBeforeRequest(oSession: Session) {
        var epicDomains = [
            "game-social.epicgames.com",
            "ol.epicgames.com",
            "ol.epicgames.net",
            "on.epicgames.com",
            "ak.epicgames.com",
            "epicgames.dev"
        ];

        var host = oSession.hostname.toLowerCase();
        for (var i = 0; i < epicDomains.length; i++) {
            if (host.Contains(epicDomains[i])) {
                if (oSession.HTTPMethodIs("CONNECT")) {
                    oSession["x-replywithtunnel"] = "FortniteTunnel";
                    return;
                }
                oSession.fullUrl = "http://127.0.0.1:8080" + oSession.PathAndQuery;
                oSession["ui-color"] = "orange";
                return;
            }
         }
      }
    }
    ```

6. Click **Save Script**
7. On your **Android Phone** go to your wifi settings click on the wifi your **PC** is connected to
8. Click **Advanced Options** set **Proxy** to **Manual** and set **Proxy Hostname** to your local pc ip and **Port** to `8888`
9. Press **Save** and you can now exit the settings app
## Optional Step: Fortnite Chapter 2+
1. If you chose **Voltronite** as your **Backend** go to `src/routes/data.ts` and make sure you get your manifest from `cloudcontent.json` inside the **assets** folder in the apk and replace it in the **Backend** and **DON'T REPLACE** `manifest.manifest`.
2. Launch **Fortnite** with the backend on and the game should install
3. After the game installs you cannot login since there is only **2 options** that will just open **Epic Games** Website. To fix this open **A Web Browser** and open `http://your-pc-ip:8080/login`
4. Type your **Username** that you would like to use in-game then press **Continue**
5. Fortnite should open up if you are still stuck on the **Login Screen** press **YES** and **BACK OUT QUICKLY** then you should login and you should be in the lobby 
---
And YOU DID IT! now just launch Fortnite and you should see it downloading the game files. If you enjoyed this please **Star the repo** as a thank you and credit me if you do a tutorial with this or share the **Repo link** and cheers!!!!

## Credits:
- **DHyper:** For showing me Chapter 1 - 2 SSL bypass.
- **Ghostcubert & Puppe:** For testing.
     
