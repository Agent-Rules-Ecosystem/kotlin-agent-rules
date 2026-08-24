# Checklist de Release y Despliegue — Kotlin (Android / Play Store)

Lista de verificación obligatoria para compilar, auditar y desplegar versiones de producción en Google Play Console o entornos empresariales.

---

## 📋 1. Checklist Pre-Build (Verificación de Código)

- [ ] **Limpieza de Lint**: Ejecutar `./gradlew lintRelease` y resolver cualquier error grave (Fatal / Security).
- [ ] **Pruebas Unitarias**: Ejecutar `./gradlew testReleaseUnitTest` y verificar que el 100% de los unit tests pasen.
- [ ] **Análisis Estático**: Ejecutar `./gradlew detekt` / `./gradlew ktlintCheck`.
- [ ] **Inyección de Dependencias**: Verificar que no existan módulos Hilt/Koin incompletos o faltantes.

---

## 🔐 2. Configuración de Seguridad y Firma

- [ ] **Configuración de Gradle (`build.gradle.kts`)**:
  * `versionCode` incrementado correlativamente.
  * `versionName` actualizado según la semántica de la versión (ej. `1.4.2`).
  * `minifyEnabled = true` en el build type `release` (activar R8 / ProGuard).
  * `shrinkResources = true` activado para eliminar recursos no utilizados.
- [ ] **Firmado del APK / AAB**:
  * Verificar que `signingConfigs.release` esté configurado con el KeyStore de producción.
  * **NUNCA** commitear contraseñas del KeyStore (`storePassword`, `keyPassword`) en el control de versiones (usar `local.properties` o variables de entorno CI/CD).
- [ ] **Fichero ProGuard/R8 (`proguard-rules.pro`)**:
  * Conservar reglas de ofuscación para librerías que usen reflexión (Retrofit, Gson, Room, Hilt).

---

## 📦 3. Compilación de Artefactos de Producción

Para generar el Android App Bundle (`.aab`) oficial para Google Play:

```bash
# Compilar Android App Bundle firmado
./gradlew bundleRelease
```

El artefacto resultante se ubica en: `app/build/outputs/bundle/release/app-release.aab`.

---

## 🚀 4. Despliegue y Post-Lanzamiento

- [ ] Subir `.aab` a **Google Play Console** (Internal Testing -> Beta -> Production).
- [ ] Verificar reporte de pre-lanzamiento en Play Console (crash rate, performance check en dispositivos reales).
- [ ] Monitoreo activo de excepciones vía Firebase Crashlytics / Sentry en las primeras 48 horas post-despliegue.
