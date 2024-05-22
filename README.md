
# Passo a passo para gerar apk pelo Expo:

## SMISDK

* No diretório `smisdk-android-plugin` alterar o `build.gradlew`:
```
buildscript {
    repositories {
        google()
        jcenter()
        mavenCentral()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:8.1.3'
    }
    ext {
        buildToolsVersion = findProperty('android.buildToolsVersion') ?: '33.0.0'
        minSdkVersion = Integer.parseInt(findProperty('android.minSdkVersion') ?: '21')
        compileSdkVersion = Integer.parseInt(findProperty('android.compileSdkVersion') ?: '33')
        targetSdkVersion = Integer.parseInt(findProperty('android.targetSdkVersion') ?: '33')

        ndkVersion = "23.1.7779620"
    }
}

allprojects {
    repositories {
        mavenCentral()
        google()
        jcenter()
        maven {
            url 'https://s3.amazonaws.com/sdk-ga-releases.cloudmi.datami.com/android/mvn/smisdk/'
        }
    }
}


apply plugin: 'com.android.library'

android {
    compileSdkVersion rootProject.ext.compileSdkVersion
    buildToolsVersion rootProject.ext.buildToolsVersion

    namespace "com.datami.smisdk_plugin"

    defaultConfig {
        minSdkVersion rootProject.ext.minSdkVersion
        targetSdkVersion rootProject.ext.targetSdkVersion
        versionCode 1
        versionName "1.0"
    }
}

dependencies {
    implementation 'com.facebook.react:react-native:+'
    implementation 'com.datami:smisdk:+'
}
```
Agora vamos adicionar alguns arquivos no diretório `smisdk-android-plugin`:
* Crie um arquivo chamado `local.properties` com o seguinte conteúdo:
```
sdk.dir=/path/to/your/sdk
```
No lugar de `/path/to/your/sdk` coloque o caminho para onde o sdk está localizado no seu PC

* Crie um arquivo chamado `gradle.properties` no diretório `smisdk-android-plugin/` com o seguinte conteúdo:
```
android.useAndroidX=true
```
* Crie um diretório/arquivo `gradle/wrapper/gradle-wrapper.properties` dentro de `smisdk-android-plugin/` com o seguinte conteúdo:
```
distributionUrl=https\://services.gradle.org/distributions/gradle-8.6-all.zip
```
* Altere o versão do react para 18.2.0 (para ficar compatível com a aplicação Conecta Recife) ou então copie e cole o conteúdo abaixo:
```
{
  "name": "react-native-smisdk-plugin",
  "version": "1.2.5",
  "description": "Datami React native plugin",
  "private": true,
"files": [
    "README.md",
    "smisdk-android-plugin",
    "ApplicationClass.txt",
    "smisdk_integration.js",
    "index.js",
    "ios",
    "react-native+0.62.2.patch",
    "sd-react-native-smisdk-plugin.podspec",
    "postInstall.sh"
  ],  
  "scripts": {
    "postinstall": "sh postInstall.sh"
  },
  "repository": {
    "type": "git",
    "url": "https://bitbucket.org/datami/sd-react-native-smisdk-plugin.git"
  },
    "peerDependencies": {
    "react": "^18.2.0",
    "react-native": ">=0.60.0-rc.0 <1.0.x",
    "patch-package": "^6.2.2",
    "postinstall-postinstall": "^2.1.0"
  },
  "devDependencies": {
    "react": "^18.2.0",
    "react-native": "^0.61.5",
    "patch-package": "^6.2.2",
    "postinstall-postinstall": "^2.1.0"
  },

  "author": "Datami inc",
  "license": "Datami mobile solutions",
  "homepage": "https://bitbucket.org/datami/sd-react-native-smisdk-plugin"
}
```
* Abra o projeto (`smisdk-android-plugin/`) no Android Studio vá em `Build` e aperte em `Make Project`. Obs.: Caso haja algum erro, por favor, me informar. Se der BUILD SUCCESSFUL, vamos para o próximo passo.  
* Aperte CTRL + Shift + O para sincronizar o projeto com o build.gradle

## CONECTA RECIFE

* ATENÇÃO!!! Após ter feito todos esses passos sem erro algum, teremos que instalar o projeto (smisdk) do github, pois o Expo não reconhece plugins instalados localmente.
 - No package.json em dependencies altere a dependencia para o seguinte > "react-native-smisdk-plugin": git+https://[TOKEN]@github.com/[USER]/[REPO].git#[BRANCH]    
 - Para gerar o <TOKEN> faça o seguinte
    1. Acesse as configurações do GitHub
        Vá para GitHub e faça login na sua conta.
        No canto superior direito, clique na sua foto de perfil e depois em "Settings" (Configurações).
    2. Navegue até a seção de tokens de acesso
        No menu à esquerda, role para baixo até a seção "Developer settings" (Configurações de desenvolvedor).
        Clique em "Personal access tokens".
    3. Gerar um novo token
        Clique no botão "Generate new token".
        Você será solicitado a confirmar sua senha.
    4. Configurar o token
        Dê um nome para o seu token no campo "Note" (Nota), como "Meu Token de Acesso".
        Defina uma data de expiração para o token se desejar, ou deixe em branco para que o token não expire.
        Selecione as permissões que o token precisa. Para acessar repositórios privados, você geralmente precisa das seguintes permissões:
        repo (Acesso completo aos repositórios privados)
        read:org (Ler a organização, se aplicável) 
  
* Dê um `npm install` (É importante que seja o npm, pois o Expo usa o npm também)

* Em `android/app/build.gradlew` mudar o trecho `implementation "com.datami:vpnsdk:+"` para `implementation "com.datami:smisdk:+"` dentro do corpo de `dependencies`.

* Abra o Android Studio na pasta `android` do projeto Conecta Recife vá em Build > Make Project (Se der BUILD SUCCESSFUL vamos pro próximo passo!)



PASSO FINAL!! 

* Execute `eas build -p android --profile preview`
