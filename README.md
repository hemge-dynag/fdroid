# Depot F-Droid MemoMind

Depot F-Droid heberge sur la branche `gh-pages` : la page publique est
`https://raw.githubusercontent.com/hemge-dynag/fdroid/gh-pages/repo`.

## Ajouter le depot dans F-Droid

1. Ouvrir F-Droid > Parametres > Depots.
2. « + » puis coller l'URL `https://raw.githubusercontent.com/hemge-dynag/fdroid/gh-pages/repo`
   (empreinte de signature : voir `keystore.fingerprint`).
3. Rafraichir : l'app « MemoMind Cardio » apparait.

## Presentation

C'est un depot F-Droid **secondaire** : les APK sont construits et signes par
l'editeur (Dynag), pas recompiles par fdroidserver. Il faut donc :

- un fichier `repo/index-v1.json` (genere par `tools/fdroid/gen_index.py`) ;
- les APK + icones dans `repo/` ;
- idealement un `repo/index-v1.jar` signe (sans lui F-Droid affiche « signature
  non verifiee » mais installe quand meme).

La configuration `fdroidserver` (`config.yml`) permet de re-signer l'index et de
regenerer `index-v1.jar` et `index.xml` avec `fdroid update` sur un poste qui a
fdroidserver installe.

## Structure

```
fdroid/
├── config.yml            configuration fdroidserver
├── keystore.jks          cle de signature du depot (committee sur demande de Dynag)
├── metadata/
│   └── com.dynag.cardio.yml
└── repo/
    ├── index-v1.json     index (genere)
    ├── index-v1.jar      index signe (apksigner, cle fdroid)
    ├── com.dynag.cardio.48.png / .72.png
    └── memomind-cardio-1.2.17.apk
```

## Regenerer l'index

```bash
python3 tools/fdroid/gen_index.py fdroid/repo      # index-v1.json
```

## Cle du depot

Creee le 19 nov. 2026 (`keystore.jks`, alias `fdroid`, RSA 4096, 10 000 jours),
empreinte dans `keystore.fingerprint` :

```
SHA-256: 0E:7A:8A:A3:6F:33:07:95:8F:91:80:2E:9C:BA:27:90:60:8B:EE:2D:83:A2:E5:CF:01:55:C9:03:7A:67:9E:5F
```

Le fichier est versionne dans ce depot prive a la demande de Dynag (pas de
perte de cle). Mot de passe du store : voir avec Dynag (non ecrit ici).

Signature de l'index (sans fdroidserver) :

```bash
apksigner sign --ks keystore.jks --ks-key-alias fdroid \
               --min-sdk-version 21 --out repo/index-v1.jar index-unsigned.jar
```

## Ajouter une app

1. Copier l'APK signe dans `fdroid/repo/`.
2. Ajouter une entree dans `APPS` de `tools/fdroid/gen_index.py` (ou
   `metadata/<package>.yml` pour fdroidserver).
3. Regenerer l'index, commit, push sur `gh-pages`.

## Publication

`fdroid/repo/` est publie sur la branche `gh-pages` (raw.githubusercontent.com).
Le token GitHub est gere par l'environnement de build de l'agent ; ne pas le
committer.
