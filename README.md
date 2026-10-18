# Nagy Noémi Elizabeth — Elizabeth AI Fusion

**Android-only, Kotlin, 2026-10-18, v1.0.0-alpha.** A program középpontjában kizárólag az AI gondolkodási, kommunikációs, memóriakezelési és tulajdonosi működése áll. Nincs külön ipari, raktári vagy nyilvántartó modul.

## Mi van TÉNYLEG megírva?

- Natív Android-felület négy nézettel: **AI**, **Memória**, **Teendők**, **Tulajdonos**.
- Valódi, **helyi neurális szöveggenerálás** a Google LiteRT-LM 0.18.0 könyvtárával, ha a tulajdonos kompatibilis **`.litertlm`** modellt importál. Egyetlen modell sem jár az APK-val. Nem szerveres ChatGPT.
- Négy mód: általános, szoftverfejlesztő, analitikus, és **tanácskozó** (három egymást követő tervező–kritikus–összegző generálás ugyanazzal a lokális modellel). A tanácskozó mód jelentősen lassabb.
- Magyar elsődleges prompt; opcionális megjelenített név és nyelv.
- Előzmények és személyes emlékek titkosítva **Android Keystore + AES-256-GCM** segítségével.
- Helyi memóriakeresés tokenegyezéssel, felhasználói pozitív/negatív visszajelzésekből képzett **Beta(1,1) posterior**; ez **nem** a válasz objektív helyességének valószínűsége.
- Feladatlista, helyi napló, számológép `= (2+3)*4` alakban modell nélkül.
- Tulajdonosi PIN/jelszó PBKDF2-HMAC-SHA256 deriválással. Törlés, import, export és a helyi leállítás PIN-hez kötött.
- Jelszóval AES-GCM titkosított, hordozható `.eab` backup fájl; importálás tulajdonosi jóváhagyással.
- **Nincs INTERNET jogosultság**, háttérszolgáltatás, más alkalmazás adatainak beolvasása vagy önkényes önmódosítás.
- `android-apk.yml` automatikus debug APK fordító és artifact feltöltés GitHub Actions rendszeren.
- Egységtesztek az auditálható, tisztán Kotlinban implementált döntési és matematikai alrendszerekhez.

## Működési folyamat

1. Az első indításkor a tulajdonos létrehoz egy minimum hat karakteres PIN-t.
2. Belépés után az **AI** lapon szöveges kérdést küldhet.
3. Modell nélkül a felület nyíltan **nem neurális fallback** választ ad; memória és feladat továbbra is működik.
4. A **Tulajdonos > Modell importálása** alatt válassz egy LiteRT-LM-kompatibilis `.litertlm` modellt a telefonról. A program a fájlt az alkalmazás saját tárhelyére másolja. Nem tölt le semmit a háttérben.
5. A következő üzenetnél az Androidon, hálózat nélkül inicializálódik a neurális motor, betölti a memóriakivonatot és előzményeket, majd válaszol.
6. A **👍 / 👎** segítségével a keresési kontextus jövőbeli rangsorolását változtathatod meg, **nem** az LLM súlyait.
7. Jelentős változtatáshoz külön tulajdonosi PIN-t kér az app; külső szolgáltatások kezelése a programból egyelőre nem valósult meg.
8. A **teljes helyi leállítás** az app folyamatát megszünteti; más processzeket és távoli automatizmusokat nem állít le. Újraindításkor új tulajdonosi feloldást kér.

## Modellválasztás

Hivatalos útmutató és modellek: https://ai.google.dev/edge/litert-lm/android

Google LiteRT Community modellek: https://huggingface.co/litert-community

**A `.gguf` és a `.task` fájlok NEM kompatibilisek ezzel a LiteRT-LM implementációval.** Csak megfelelően elkészített `.litertlm` modell importálható. A Google LiteRT-LM natív futtatókörnyezet kompatibilitása telefononként eltérhet; CPU backendet választottunk a GPU-driverfüggőség csökkentésére. A nagy modellek több GB tárhelyet és RAM-ot igényelhetnek.

## APK fordítása GitHubon

1. A ZIP tartalmát **kicsomagolva**, a könyvtárstruktúra megtartásával töltsd fel a `Lack0o/Elizabeth-AI-Fusion` GitHub-repó **gyökerébe** (ne egyetlen ZIP-fájlt tölts fel).
2. A `.github/workflows/android-apk.yml` fájlt is add hozzá.
3. GitHub **Actions > Elizabeth Android AI APK**. A futtatás végeztével az **Artifacts > Elizabeth-AI-Fusion-Android-APK** alól tölthető le a debug APK-t tartalmazó ZIP.
4. Másik lehetőség: Android Studio, **Build > Build APK(s)**. Java 17 + Android SDK 35 és internet kell a függőségek letöltéséhez a fordításkor. Az elkészült app offline működik.

**Telepítés előtt:** debug APK tesztcélú; végleges terjesztéshez saját release-aláírás, fizikai készülékteszt, adatvédelmi és biztonsági audit szükséges.

## Korlátok, amelyekre nem állítható, hogy kész

- Nem készült modellfájl és nincs igazolt APK-fordítás vagy telefonos működési teszt ebben a környezetben.
- Nincs tényleges folyamatos neurális súlyfrissítés, internetes integráció, önálló GitHub-beavatkozás, offline beszédfelismerés, kameraképfeldolgozás vagy garantált éjjel-nappali háttérfutás.
- Nem garantált az AI-válasz tényszerűsége. A memória megbízhatósága az oda bevitt adatoktól függ.
- A lokális folyamat leállítása nem kapcsolja le a más szolgáltatásokban korábban konfigurált automatizmusokat.
- Kiválasztott modell licencét és rendszerigényét külön ellenőrizni kell.
- A korábbi githubos adatállományok nem íródnak felül automatikusan. A feltöltés jogosultságot igényel.

## Biztonság

- A felhasználói szöveg **nem hagyja el a készüléket** az alkalmazás saját kódján keresztül.
- A jelszó nem kerül a beszélgetésbe és nincs forráskódba égetett API kulcs.
- Az Android Keystore kulcs elvesztése, alkalmazás-adat törlése vagy készülékcsere esetén a helyi állapot visszaállításához előzetesen készített `.eab` mentés szükséges.
- Nagy többszöri sikertelen PIN-kísérletekre késleltető szabályt és biometrikus védelmet még érdemes fejleszteni, ezért jelenleg fejlesztői tesztkiadás.
- **A modell kimenete soha nem kap automatikus operációs rendszer vagy fiókkezelő jogosultságot**; a v1.0 alfa semmilyen ilyen végrehajtó eszközt nem tartalmaz.

Lásd `docs/ARCHITEKTURA.md`, `docs/BIZTONSAG.md`, `docs/TESZTELÉS.md`.
