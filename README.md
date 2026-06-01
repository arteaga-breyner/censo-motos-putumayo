name: Build APK - Censo Motos
on:
  push:
    branches: [ main, master ]
  workflow_dispatch:

env:
  FORCE_JAVASCRIPT_ACTIONS_TO_NODE24: true

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Instalar Android SDK
        uses: android-actions/setup-android@v3

      - name: Instalar Gradle 8.4
        run: |
          wget -q https://services.gradle.org/distributions/gradle-8.4-bin.zip
          unzip -q gradle-8.4-bin.zip -d /opt/gradle
          echo "/opt/gradle/gradle-8.4/bin" >> $GITHUB_PATH

      - name: Generar Gradle Wrapper
        run: gradle wrapper --gradle-version=8.4

      - name: Compilar APK
        run: |
          chmod +x gradlew
          ./gradlew assembleDebug --no-daemon --stacktrace

      - name: Subir APK
        uses: actions/upload-artifact@v4
        with:
          name: CensoMotos-APK
          path: app/build/outputs/apk/debug/app-debug.apk
          retention-days: 30
