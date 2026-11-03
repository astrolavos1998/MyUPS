# MyUPS

**Παρακολούθηση UPS για Windows μέσω NUT (Network UPS Tools)**
**UPS monitoring for Windows through NUT (Network UPS Tools)**

![version](https://img.shields.io/badge/version-0.2-4f7cff)
![platform](https://img.shields.io/badge/platform-Windows%2010%20%7C%2011-0b1020)
![python](https://img.shields.io/badge/python-3.12%2B-3776ab)
![language](https://img.shields.io/badge/UI-Ελληνικά%20%7C%20English-a78bfa)

™ 2026 · © Copyright by Petros Andrianos

[Ελληνικά](#ελληνικά) · [English](#english)

<!-- Πρόσθεσε screenshots στον φάκελο docs/ και ξεσχολίασε τις γραμμές:
![Επισκόπηση](docs/overview.png)
![Ροή ρεύματος](docs/power-flow.png)
-->

---

## Ελληνικά

Το MyUPS είναι εφαρμογή System Tray που δείχνει τι κάνει το UPS σας: από πού έρχεται το ρεύμα, πόση ενέργεια καταναλώνουν οι συσκευές, πόση αυτονομία έχετε και τι συνέβη στις τελευταίες διακοπές. Δουλεύει με όποιο UPS υποστηρίζει το NUT, τοπικά μέσω USB ή απομακρυσμένα μέσω δικτύου.

### Δυνατότητες

- **Πολλά UPS ταυτόχρονα**, με σελίδα Επισκόπησης και ξεχωριστό ιστορικό για καθένα.
- **Αυτόματη ανίχνευση** με το `nut-scanner`. Ο οδηγός ρύθμισης γράφει μόνος του το `ups.conf` (κρατώντας αντίγραφο ασφαλείας) και ξεκινά τον σωστό driver.
- **Ζωντανό διάγραμμα ροής ρεύματος**: Δίκτυο → UPS → Συσκευές, με κινούμενη ροή. Διακρίνει κανονική λειτουργία, μπαταρία, AVR και χαμηλή μπαταρία.
- **Ιστορικό** με γραφήματα τάσης, ισχύος και μπαταρίας (1 ώρα έως 30 ημέρες) και εξαγωγή σε CSV.
- **Συμβάντα**: διακοπές με διάρκεια, AVR, υπερφόρτωση, απώλεια επικοινωνίας, τεστ μπαταρίας.
- **Ενέργεια και κόστος**: kWh ανά ημέρα και μήνα, με εκτίμηση μηνιαίου κόστους.
- **Εκτίμηση αυτονομίας** από:
  1. το ίδιο το UPS
  2. τη λίστα μοντέλων (`models.json`)
  3. χειροκίνητα στοιχεία
  4. **αυτόματη εκμάθηση** από πραγματικές διακοπές
- **Τεστ μπαταρίας** και έλεγχος **Beeper** μέσω `upscmd`.
- **Ασφαλής τερματισμός** του υπολογιστή σε παρατεταμένη διακοπή, με αντίστροφη μέτρηση και ακύρωση.
- **Ειδοποιήσεις Windows** και εικονίδιο tray που αλλάζει χρώμα ανάλογα με την κατάσταση.
- **GR / ENG** και **Light / Dark** (θέμα Astro) από διακόπτες στην κεφαλίδα κάθε παραθύρου και στο μενού του tray. Η αλλαγή γίνεται αμέσως, χωρίς επανεκκίνηση.

### Εγκατάσταση

1. Κατεβάστε το `MyUPS_0.2.zip` από τα [Releases](../../releases).
2. Αποσυμπιέστε τον φάκελο `MyUPS` σε φάκελο με δικαίωμα εγγραφής, π.χ. `C:\MyUPS`.
   Όχι στο `C:\Program Files`, γιατί η εφαρμογή γράφει το `NUT\etc\ups.conf`.
3. Συνδέστε το UPS με USB και ανοίξτε το `MyUPS.exe`.
4. Πατήστε **Προσθήκη UPS** → **Αυτόματη ανίχνευση UPS στο USB**.

Την πρώτη φορά τα Windows μπορεί να εμφανίσουν προειδοποίηση SmartScreen, επειδή το exe δεν έχει ψηφιακή υπογραφή. Πατήστε **Περισσότερες πληροφορίες → Εκτέλεση οπωσδήποτε**.

### Αν δεν βρεθεί το UPS

Τα Windows συχνά «κρατούν» το UPS με τον δικό τους driver. Το NUT χρειάζεται driver WinUSB/libusb, που αλλάζει **μία φορά** με το [Zadig](https://zadig.akeo.ie/):

1. Ανοίξτε το Zadig ως διαχειριστής.
2. **Options → List All Devices**.
3. Επιλέξτε το UPS (π.χ. «USB to Serial», «UPS», «HID UPS Battery»). Ελέγξτε το USB ID.
4. Driver **WinUSB** → **Replace Driver**. Αν δεν δουλέψει, δοκιμάστε **libusbK**.
5. Στο MyUPS: **Ξανά σάρωση**.

Μετά την αλλαγή, τα Windows δεν δείχνουν πλέον το UPS ως μπαταρία στη γραμμή εργασιών. Η αλλαγή αναιρείται από τη **Διαχείριση Συσκευών → Κατάργηση εγκατάστασης συσκευής**.

### Τεστ μπαταρίας και Beeper

Οι εντολές προς το UPS χρειάζονται χρήστη upsd. Στο `NUT\etc\upsd.users` προσθέστε:

```ini
[admin]
    password = ο_κωδικός_σας
    instcmds = ALL
```

Μετά, στις **Ρυθμίσεις → NUT** συμπληρώστε χρήστη και κωδικό και πατήστε **Επανεκκίνηση υπηρεσιών NUT**.

Ο κωδικός αποθηκεύεται χωρίς κρυπτογράφηση στο `%APPDATA%\MyUPS\config.json`. Χρησιμοποιήστε κωδικό που δεν έχετε αλλού.

### UPS σε άλλον υπολογιστή

Στον υπολογιστή με το UPS:
- το `upsd.conf` πρέπει να έχει `LISTEN` στη διεύθυνση δικτύου του, π.χ. `LISTEN 0.0.0.0 3493`
- το firewall πρέπει να επιτρέπει τη θύρα **3493**

Στο MyUPS: **Προσθήκη UPS → UPS σε άλλον υπολογιστή**.

### Λίστα μοντέλων (`models.json`)

Το αρχείο βρίσκεται δίπλα στο `MyUPS.exe` και συμπληρώνει όσα δεν αναφέρει το UPS. Κάθε μοντέλο:

```json
{
  "key": "powerwalker-vi-1500-csw",
  "mfr": "PowerWalker",
  "model": "VI 1500 CSW",
  "match": ["VI 1500 CSW", "VI1500CSW"],
  "va": 1500,
  "w": 900,
  "battery_v": 24,
  "runtime_half_min": 10,
  "runtime_full_min": 3.5,
  "notes": "Line-Interactive, καθαρό ημίτονο"
}
```

`0` σημαίνει «άγνωστο». Προσθήκες μοντέλων είναι ευπρόσδεκτες με Pull Request. Βάλτε μόνο στοιχεία από τα τεχνικά χαρακτηριστικά του κατασκευαστή.

### Αρχεία

| Αρχείο | Περιεχόμενο |
|---|---|
| `%APPDATA%\MyUPS\config.json` | Ρυθμίσεις και προφίλ UPS |
| `%APPDATA%\MyUPS\myups.db` | Ιστορικό, συμβάντα, ενέργεια (SQLite) |
| `models.json` (δίπλα στο exe) | Λίστα γνωστών μοντέλων |
| `NUT\etc\ups.conf.bak-*` | Αντίγραφα ασφαλείας πριν από κάθε αλλαγή |

### Build από τον πηγαίο κώδικα

Απαιτήσεις: Python 3.12+ και NUT for Windows σε φάκελο `NUT` (με `bin`, `sbin`, `etc`) δίπλα στο `MyUPS.py`.

```powershell
python -m pip install pystray pillow pyinstaller
```

Τρέξτε το `MyUPS_py2exe.bat`. Φτιάχνει:

- `dist\MyUPS\MyUPS.exe`: την εφαρμογή
- `release\MyUPS_0.2.zip`: πακέτο διανομής με το NUT, **χωρίς** τα προσωπικά `ups.conf`, `upsd.users`, `upsmon.conf`

Χρησιμοποιείτε πάντα `python -m PyInstaller`. Το σκέτο `pyinstaller` μπορεί να τρέξει από άλλη εγκατάσταση Python που δεν έχει τις βιβλιοθήκες.

Δοκιμή χωρίς UPS, με προσομοίωση δύο UPS και ξεχωριστά αρχεία ρυθμίσεων:

```powershell
python MyUPS.py --demo
```

### Συχνά προβλήματα

| Πρόβλημα | Λύση |
|---|---|
| «Χωρίς επικοινωνία με το UPS» | Ρυθμίσεις → NUT → **Επανεκκίνηση υπηρεσιών NUT**. Ελέγξτε το καλώδιο USB. |
| Το `nut-scanner` δεν βρίσκει τίποτα | Αλλαγή driver με το Zadig (δείτε παραπάνω). |
| `ERR ACCESS-DENIED` στο Beeper / Τεστ | Λάθος χρήστης ή κωδικός, ή δεν έγινε επανεκκίνηση του upsd μετά την αλλαγή του `upsd.users`. |
| Το Beeper δεν δείχνει την πραγματική κατάσταση | Κάποια UPS δεν την αναφέρουν. Δεξί κλικ στο κουμπί διορθώνει την ένδειξη χωρίς να στείλει εντολή. |
| Τα Watt δεν εμφανίζονται | Ορίστε την ονομαστική ισχύ (W) στις Ρυθμίσεις → UPS. |
| «Δεν επιτρέπεται η εγγραφή στο ups.conf» | Μεταφέρετε τον φάκελο MyUPS έξω από το `Program Files`. |

---

## English

MyUPS is a system tray app that shows what your UPS is doing: where the power comes from, how much your devices draw, how much runtime you have left and what happened during recent outages. It works with any UPS supported by NUT, locally over USB or remotely over the network.

### Features

- **Several UPS at once**, with an Overview page and separate history for each.
- **Automatic detection** with `nut-scanner`. The setup wizard writes `ups.conf` for you (with a backup) and starts the right driver.
- **Live power-flow diagram**: Mains → UPS → Devices, with animated flow. Shows normal operation, battery, AVR and low battery.
- **History** charts for voltage, power and battery (1 hour to 30 days), with CSV export.
- **Events**: outages with duration, AVR, overload, communication loss, battery tests.
- **Energy and cost**: kWh per day and month, with a monthly cost estimate.
- **Runtime estimate** from:
  1. the UPS itself
  2. the model list (`models.json`)
  3. manual entry
  4. **automatic learning** from real outages
- **Battery test** and **Beeper** control through `upscmd`.
- **Safe shutdown** during long outages, with a countdown and cancel button.
- **Windows notifications** and a tray icon that changes color with the UPS state.
- **GR / ENG** and **Light / Dark** (Astro theme) switches in the header of every window and in the tray menu. Changes apply instantly, without a restart.

### Installation

1. Download `MyUPS_0.2.zip` from [Releases](../../releases).
2. Extract the `MyUPS` folder to a writable location, e.g. `C:\MyUPS`.
   Not `C:\Program Files`, because the app writes `NUT\etc\ups.conf`.
3. Connect the UPS via USB and run `MyUPS.exe`.
4. Click **Add UPS** → **Detect a UPS on USB automatically**.

Windows SmartScreen may warn the first time because the exe is not code-signed. Click **More info → Run anyway**.

### If the UPS is not found

Windows often claims the UPS with its own driver. NUT needs a WinUSB/libusb driver, which you change **once** with [Zadig](https://zadig.akeo.ie/):

1. Run Zadig as administrator.
2. **Options → List All Devices**.
3. Select the UPS (e.g. "USB to Serial", "UPS", "HID UPS Battery"). Check the USB ID.
4. Driver **WinUSB** → **Replace Driver**. If it does not work, try **libusbK**.
5. In MyUPS: **Scan again**.

After the change, Windows no longer shows the UPS as a battery in the taskbar. Undo it from **Device Manager → Uninstall device**.

### Battery test and Beeper

UPS commands need an upsd user. Add to `NUT\etc\upsd.users`:

```ini
[admin]
    password = your_password
    instcmds = ALL
```

Then, in **Settings → NUT**, enter the user and password and click **Restart NUT services**.

The password is stored unencrypted in `%APPDATA%\MyUPS\config.json`. Use one you do not use elsewhere.

### UPS on another computer

On the computer with the UPS:
- `upsd.conf` must `LISTEN` on its network address, e.g. `LISTEN 0.0.0.0 3493`
- the firewall must allow port **3493**

In MyUPS: **Add UPS → UPS on another computer**.

### Model list (`models.json`)

The file sits next to `MyUPS.exe` and fills in what the UPS does not report (see the example above). `0` means unknown. Model contributions are welcome via Pull Request. Please use manufacturer datasheet values only.

### Build from source

Requirements: Python 3.12+ and NUT for Windows in a `NUT` folder (with `bin`, `sbin`, `etc`) next to `MyUPS.py`.

```powershell
python -m pip install pystray pillow pyinstaller
```

Run `MyUPS_py2exe.bat`. It produces:

- `dist\MyUPS\MyUPS.exe`: the app
- `release\MyUPS_0.2.zip`: a distribution package with NUT, **without** your personal `ups.conf`, `upsd.users`, `upsmon.conf`

Test without a UPS (two simulated UPS, separate settings files):

```powershell
python MyUPS.py --demo
```

---

## Άδειες / Licenses

- **MyUPS**: ™ 2026 · © Copyright by Petros Andrianos.
- **NUT (Network UPS Tools)**: GNU GPL v2 or later. Source: <https://github.com/networkupstools/nut>
- **pystray**: LGPL v3. **Pillow**: MIT-CMU (HPND).
