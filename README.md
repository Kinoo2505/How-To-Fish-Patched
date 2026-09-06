# How-To-Fish-Patched
Android reverse engineering project: Smali code patches to disable Vivo SDK login checks and initialization in "How To Fish". For educational purposes.
# 🎣 How To Fish - No Vivo Login Patch

## 📖 Overview
This repository contains a patched version of the Chinese Vivo channel port of **"How To Fish"**. The original game enforces a mandatory Vivo account login via the Vivo Union SDK, which blocks players without a Vivo device or account from playing. 

This patch completely bypasses that restriction, allowing anyone to install and play the game as a "Guest" without any account verification, region locks, or annoying login pop-ups.

---

## ⚠️ Installation Instructions (READ CAREFULLY)

Because this APK has been modified and re-signed, Android's security system will block the installation if the original game is still on your device. 

1. **UNINSTALL the original game** from your phone completely. *(If you skip this, you will get an "App not installed" or "Signature mismatch" error).*
2. **Download the latest APK** by clicking the green "Download Latest APK" button at the top of this page.
3. **Enable Unknown Sources**: Go to your phone's `Settings` > `Security` (or `Apps`) > `Install Unknown Apps`, and allow it for your browser or file manager.
4. **Install the APK**: Tap the downloaded file and follow the prompts.
5. **Launch the game**: The Vivo login screen will no longer appear. Enjoy!

---

## 🔧 Technical Details: How It Was Patched

This section details the reverse engineering process used to create this patch. It is provided for **educational purposes** for those interested in Android modding and Smali manipulation.

### 1. Reconnaissance (Jadx-GUI)
The original APK was decompiled using [Jadx-GUI](https://github.com/skylot/jadx) to analyze the Java code. By searching for keywords like `isLogin`, `vivo`, and `UnionActivity`, the following critical checks were identified:
* `com.vivo.ic.systemaccount.VivoSystemAccount.isLogin()`: The background check verifying if a user is authenticated.
* `com.vivo.unionsdk.open.VivoUnionSDK.login()`: The method that triggers the actual login UI.
* `com.vivo.unionsdk.open.VivoUnionSDK.onPrivacyAgreed()`: The initialization trigger called immediately after the splash screen.

### 2. Static Patching (APKTool & Smali)
The APK was disassembled into readable Smali code using [APKTool](https://ibotpeaches.github.io/Apktool/). The following methods were neutralized by modifying their Smali bytecode.

#### Patch A: Faking the Login State
**File:** `smali/com/vivo/ic/systemaccount/VivoSystemAccount.smali`  
**Original Logic:** Checked for a valid native account and returned `0x0` (False) if none was found.  
**Patched Logic:** Forced to immediately return `0x1` (True), tricking the game into thinking a valid session already exists.
```smali
.method public static isLogin(Landroid/content/Context;)Z
    .locals 0
    # Bypassed Vivo account check. Forcing return True (1).
    const/4 p0, 0x1
    return p0
.end method
```

#### Patch B: Nuking the Login Trigger
**File:** `smali_classes3/com/vivo/unionsdk/open/VivoUnionSDK.smali`  
**Original Logic:** Called the underlying SDK to render the `UnionActivity` login screen.  
**Patched Logic:** Replaced the entire method body with a simple `return-void`, silently swallowing any requests to open the login UI.
```smali
.method public static login(Landroid/app/Activity;)V
    .locals 0
    # Bypassed: Block login screen from ever opening
    return-void
.end method
```

#### Patch C: Disabling SDK Initialization
**File:** `smali_classes3/com/vivo/unionsdk/open/VivoUnionSDK.smali`  
**Patched Logic:** Similarly, the `onPrivacyAgreed` method was emptied to prevent the SDK from establishing background connections or callbacks.
```smali
.method public static onPrivacyAgreed(Landroid/content/Context;)V
    .locals 0
    # Bypassed: Do nothing when privacy is agreed
    return-void
.end method
```

### 3. Recompilation & Signing
The modified Smali code was recompiled into a new APK using APKTool. Finally, the APK was cryptographically signed using [Uber Apk Signer](https://github.com/nickstenning/uber-apk-signer) to make it installable on Android devices.

---

## 🛠️ Tools Used
* **[Jadx-GUI](https://github.com/skylot/jadx)**: Dex to Java decompiler.
* **[APKTool](https://ibotpeaches.github.io/Apktool/)**: Tool for reverse engineering Android APK files.
* **[Uber Apk Signer](https://github.com/nickstenning/uber-apk-signer)**: Tool to easily sign and zipalign multiple APK files.
* **[Android Debug Bridge (ADB)](https://developer.android.com/studio/command-line/adb)**: For installing and managing the app on the test device.

---

## ❓ Frequently Asked Questions (FAQ)

**Q: I get an "App not installed" error.**  
**A:** You did not uninstall the original game first. Android blocks installations when the new APK has a different signature than the existing one. Uninstall the original, then try again.

**Q: Will my cloud saves work?**  
**A:** No. Because this patch bypasses the Vivo account system, any features tied to a specific Vivo ID (like Vivo Cloud Saves or Vivo-exclusive leaderboards) will not function. The game will operate in a local/offline state.

**Q: Will this update automatically?**  
**A:** No. This is a manually patched APK. If the developers release an update on the Vivo App Store, you will need to wait for a new patched version to be released there.

**Q: Can you play multiplayer?**
**A:**No. The port was made by chinese people, i just got access to it and removed the forced login prompts from vivo. I am NOT a developper, just a patcher who wanted to try something new.

---

## ⚖️ Disclaimer
This project is created strictly for **educational and archival purposes** to demonstrate Android reverse engineering and Smali patching techniques. 

* I do not claim ownership of "How To Fish" or any of its assets.
* All rights, trademarks, and copyrights belong to the original game developers and publishers.
* Do not use this patch for malicious purposes or to gain unfair advantages in competitive online environments.
* If you enjoy the game, please support the original developers by playing the official version.

* DOWNLOAD LINK IS IN RELEASE PAGE

---

## 📜 License
The reverse engineering documentation and Smali patches in this repository are provided under the [MIT License](LICENSE). The game assets and original code remain the property of their respective owners.
