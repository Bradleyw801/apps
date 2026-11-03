# Brad White's apps

These are the Android apps I have built, in one F-Droid repository. Add it to F-Droid, Droid-ify or any other
F-Droid client and you can install the apps and get their updates from there.

The install page has the QR code: https://bradleyw801.github.io/apps/

| | |
| --- | --- |
| Address | `https://bradleyw801.github.io/apps/repo` |
| Fingerprint | `F5D063BDF9290A290A5EB2DE6520648C7E742E2FA8E3E734C8826D33782AD58B` |

## The apps

| App | Package | What it's for |
| --- | --- | --- |
| Slickrock Station | `com.bradwhite.slickrock` | A game launcher that sets up RetroArch for you, for physical keys, a controller or a touchscreen |
| RetroArch for Slickrock | `com.bradwhite.slickrock.retroarch` | The RetroArch build Slickrock Station runs, with a few input patches |
| Twyn Keyboard | `com.bradwhite.twyn` | A predictive keyboard for any device, built for real keys and small screens |
| Twyn Dansk, Deutsch, Español, Français, Português | `com.bradwhite.twyn.lang.da`, `.de`, `.es`, `.fr`, `.pt` | Twyn's languages past English, one download each, with no code: Twyn reads the words from them |
| Paigr Relay | `com.textrelay.relay` | Sends the texts from the phone with your SIM to your Paigr inbox |
| Paigr | `com.textrelay.texts` | Reads and answers those texts on another phone |

If you'd rather skip F-Droid, each app's current build is also on the install page under its name:
`Slickrock-Station.apk`, `RetroArch-for-Slickrock.apk`, `Twyn-Keyboard.apk`, `Paigr-Relay.apk` and `Paigr.apk`, and
Twyn's languages as `Twyn-Dansk.apk`, `Twyn-Deutsch.apk`, `Twyn-Español.apk`, `Twyn-Français.apk` and `Twyn-Português.apk`.
A `.json` file with the same name has the version.

## Publishing a new version

Slickrock Station and RetroArch for Slickrock come from
[slickrock-releases](https://github.com/Bradleyw801/slickrock-releases). Publish a release there, tagged
`slickrock-<version>` or `retroarch-<version>`, with a `versionCode: N` line in the notes. The workflow looks for new
releases every hour. For RetroArch it takes the arm64-v8a APK and copies the release's `.patch` into `source/`.

Twyn Keyboard, its languages, Paigr and Paigr Relay go in `fdroid/incoming/`, either with `git push` or with "Add file,
Upload files" on GitHub. GitHub's web upload takes files up to 25 MB. A bigger file can go in as pieces and the workflow puts them back
together:

```
split -b 9000000 -d -a 2 Twyn-2.7.2.apk Twyn-2.7.2.apk.part
```

Every build gets checked before it goes out. It has to be signed with the key pinned for its app in
`fdroid/metadata/<package>.yml` (`AllowedAPKSigningKeys`), and its version code has to be higher than the one already
published. If it passes, it replaces the old build, and the workflow signs a new index and deploys it with the install
page. If it fails, it gets deleted and the run fails with the reason on the run's page. You can also run the workflow
by hand from the Actions tab.

The name, version and icon come from the APK and from `fdroid/metadata/<package>/en-US/icon.png`, so the `.yml` only
needs editing when the description changes. A new app needs its `.yml`, with its signing key pinned, and an icon
before its first build goes in.

Phones get the update the next time their F-Droid client refreshes.

## Keys

The index is signed with `keystore.p12` (alias `apps`), which isn't in git. The workflow restores it from the
`FDROID_KEYSTORE_B64` secret and reads its password from `FDROID_KEYSTORE_PASS`. Keep a copy of the keystore and
password somewhere other than GitHub. If the key is ever lost, every phone has to remove the repository and add it
again with a new fingerprint.

Each app has its own signing key, kept with that app's project. The repository key only signs the index.

## What's where

```
fdroid/config.yml                 repository name, address and key settings (no secrets)
fdroid/metadata/<package>.yml     each app's name, summary, description, licence and pinned key
fdroid/metadata/<package>/en-US/icon.png
fdroid/repo/*.apk                 the apps
fdroid/incoming/                  new builds waiting to be checked (normally empty)
site/                             the install page and QR code
source/                           patches for the GPL apps served here
licenses/                         licences for code the apps include
tools/intake.py                   checks new builds (the workflow runs it)
.github/workflows/publish.yml     fetches Slickrock releases, checks new builds, signs the index and deploys the site
```

## Licences

Slickrock Station and Twyn Keyboard are my own apps and aren't open source. Twyn Keyboard's English word data comes
from the Leipzig Corpora Collection (CC BY 4.0), Traditional T9's word list (Apache 2.0, built on SCOWL) and the UCI
SMS Spam Collection (CC BY 4.0), and its other languages' from the Leipzig Corpora Collection; the notices are in the
app. Paigr and Paigr Relay are open source under the
Apache License 2.0, at [Bradleyw801/Paigr](https://github.com/Bradleyw801/Paigr). Both include Java-WebSocket (MIT,
`licenses/Java-WebSocket.txt`) and ZXing (Apache 2.0, `licenses/ZXing.txt`). RetroArch is GPL-3.0, and my changes to
the build served here are in `source/`.
