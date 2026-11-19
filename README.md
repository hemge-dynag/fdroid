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
├── keystore.jks          cle de signature du depot (A creer, voir plus bas)
├── metadata/
│   └── com.dynag.cardio.yml
└── repo/
    ├── index-v1.json     index (genere)
    ├── index-v1.jar      index signe (a generer avec fdroid update)
    ├── com.dynag.cardio.48.png / .72.png
    └── memomind-cardio-1.2.17.apk
```

## Regenerer l'index

```bash
python3 tools/fdroid/gen_index.py fdroid/repo      # index-v1.json
```

## Creer la cle du depot (une seule fois)

```bash
cd fdroid
keytool -genkey -v -keystore keystore.jks -alias fdroid -keyalg RSA \
        -keysize 4096 -validity 10000 -storepass <motdepasse> \
        -dname "CN=MemoMind F-Droid, OU=Dynag, O=Dynag, C=FR"
keytool -list -v -keystore keystore.jks -alias fdroid | grep SHA256
```

Puis `fdroid update` (fdroidserver) signe `index-v1.jar` avec cette cle. Ne
jamais committer `keystore.jks` ni son mot de passe.

## Ajouter une app

1. Copier l'APK signe dans `fdroid/repo/`.
2. Ajouter une entree dans `APPS` de `tools/fdroid/gen_index.py` (ou
   `metadata/<package>.yml` pour fdroidserver).
3. Regenerer l'index, commit, push sur `gh-pages`.

## Publication

`fdroid/repo/` est publie sur la branche `gh-pages` (raw.githubusercontent.com).
Le token GitHub est gere par l'environnement de build de l'agent ; ne pas le
committer.
