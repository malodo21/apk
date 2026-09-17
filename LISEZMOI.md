# Darts Scorer Pro — Génération de l'APK Android

Ce dossier contient un projet [Capacitor](https://capacitorjs.com/) qui enveloppe l'application
web (le même fichier `www/index.html` que la version web) dans une vraie app Android.

**Important** : je n'ai pas pu compiler l'APK moi-même (pas d'accès au SDK Android dans mon
environnement). Ce projet est prêt à builder, mais il faut soit passer par GitHub Actions
(aucune installation nécessaire), soit builder en local si vous avez déjà Android Studio.

---

## Option A — Le plus simple : GitHub Actions (aucune installation)

1. Créez un nouveau dépôt sur [github.com](https://github.com) (public ou privé).
2. Poussez tout ce dossier dedans :
   ```bash
   git init
   git add .
   git commit -m "Darts Scorer Pro - projet Android"
   git branch -M main
   git remote add origin https://github.com/VOTRE-COMPTE/VOTRE-DEPOT.git
   git push -u origin main
   ```
3. Allez dans l'onglet **Actions** de votre dépôt GitHub. Le workflow "Build Android APK"
   se lance automatiquement (environ 5 à 10 minutes).
4. Une fois terminé (coche verte), cliquez sur le run, puis sur **darts-scorer-pro-debug-apk**
   en bas de page pour télécharger l'APK.
5. Transférez l'APK sur votre téléphone Android (email, Drive, câble USB...) et installez-le.
   Android demandera d'autoriser "l'installation depuis des sources inconnues" la première fois.

Vous pouvez relancer le build à tout moment depuis l'onglet Actions (bouton "Run workflow"),
par exemple après avoir modifié `www/index.html`.

## Option B — En local avec Android Studio

Si vous avez déjà [Android Studio](https://developer.android.com/studio) et Node.js installés :

```bash
npm install
npx cap add android
npx cap sync android
npx cap open android
```

Cela ouvre le projet dans Android Studio. Ensuite : **Build > Build Bundle(s) / APK(s) > Build APK(s)**.

---

## Voix de l'annonceur (MP3 enregistrées)

Le set complet est maintenant intégré dans `www/audio/` (206 fichiers) : les 179 scores
individuels (1 à 179), les 3 variantes de "180" (choisie au hasard à chaque fois),
les 6 sons spéciaux (Manqué, TOF, Manches gagnées, Match gagné, Match commencé, "il
reste"), et les 18 prénoms de joueurs du club. `announceLegScore()`, `speakFrench()` et
`announceTurnIfFinishPossible()` jouent maintenant ces MP3 en priorité — la séquence
complète "prénom du joueur + il reste + score" fonctionne pour finir.

Repli automatique conservé pour tout ce qui manquerait encore (nouveau joueur non
enregistré, futur son ajouté) : ça retombe sur la voix de synthèse sans planter. Pour
ajouter un fichier plus tard, déposez-le dans `www/audio/` avec le bon nom — aucune
modification de code nécessaire.

## Ce qui a été adapté pour l'Android

- **Font Awesome retiré** : les icônes CDN ont été remplacées par des emojis (déjà utilisés
  partout ailleurs dans l'appli), pour éliminer une dépendance réseau externe.
- **Export de fichiers** : les boutons JSON / CSV Global / CSV Détaillé utilisent maintenant
  `@capacitor/filesystem` + `@capacitor/share` sur mobile (sauvegarde dans le dossier Documents
  puis ouverture du menu de partage natif), et gardent le téléchargement navigateur classique
  quand l'appli tourne sur le web.

## Limites connues à tester sur un vrai téléphone

- **Tailwind CSS** charge toujours depuis un CDN (`cdn.jsdelivr.net`) au premier lancement —
  il faut donc une connexion internet la première fois que l'appli s'ouvre. Ensuite, le WebView
  Android met généralement la ressource en cache. Si vous voulez un vrai fonctionnement
  hors-ligne dès l'installation, il faudrait pré-compiler Tailwind en CSS statique et le
  bundler dans `www/` — dites-le-moi si vous voulez que je fasse cette étape.
- **La synthèse vocale** (`speechSynthesis`, annonces "Cent quatre-vingts !" etc.) dépend du
  moteur TTS installé sur le téléphone. Le code gère déjà l'absence de l'API, mais testez sur
  un appareil réel pour vérifier que ça sonne comme attendu (voix française disponible, etc.).
- **`appId`** dans `capacitor.config.json` est réglé sur `com.dartsscorerpro.app` (générique).
  Changez-le pour votre propre identifiant si vous comptez publier l'app un jour
  (ex: `fr.votreclub.dartsscorer`) — une fois l'app installée sous un appId, le changer plus
  tard équivaut à une nouvelle app aux yeux d'Android.

## Icône de l'application

`assets/icon.svg` (une cible fléchettes vert émeraude / ambre, assortie au thème de l'appli)
est généré automatiquement en toutes les tailles nécessaires (icône classique + icône
adaptative Android) par le workflow GitHub Actions, juste après la création du projet Android.
Pour changer le visuel, remplacez ce fichier par votre propre image (SVG ou PNG carré, au
moins 1024×1024) et repoussez — rien d'autre à modifier.

## Structure du projet

```
darts-apk/
├── www/index.html          → l'application (identique à la version web)
├── package.json            → dépendances Capacitor
├── capacitor.config.json   → configuration (nom de l'app, appId)
├── .github/workflows/      → le pipeline qui build l'APK automatiquement
└── android/                → généré automatiquement au build, pas dans le dépôt
```
