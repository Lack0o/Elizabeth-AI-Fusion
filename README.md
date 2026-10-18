# Nagy Noémi Elizabeth – offline Android prototype

Installable Android demo **v0.1**. It has an offline, rule-based SDS/ADR/ATEX assistant, locally saved notes and a basic inventory for WH1/WH2/WH3, 20% low-stock alerts based on the entered target quantity, CSV export.

The app does **not** yet contain an AI language model, real SDS parsing, barcode scanning, chemical compatibility engine, validated ATEX decisions, encrypted storage or the complete previously discussed AI Fusion capabilities.

**Safety:** This app does not authorize hazardous chemical storage. Independently validate current SDS, compatibility, permits, ADR and EHS procedures. No corporate data or real SDS was included.

**Privacy:** No Internet permission is requested. Local notes/inventory are stored in plain Android app preferences on the device, not encrypted. The GitHub repository containing the workflow is currently publicly visible.

Build with Java 17, Android Gradle Plugin 8.7.3, Gradle 8.9 and Android compileSdk 35. Run `gradle :app:assembleDebug` with Android SDK installed.
