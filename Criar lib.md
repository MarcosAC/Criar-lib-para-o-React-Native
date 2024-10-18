Passo a Passo: Criar uma Biblioteca de Módulo de Permissões para NPM

1. Configurar a Estrutura do Projeto da Biblioteca

Primeiramente, crie um novo projeto para a sua biblioteca. Você pode fazer isso manualmente ou utilizar uma ferramenta como o create-react-native-library.

Usando create-react-native-library (Recomendado):

```
npx create-react-native-library react-native-permissions-module
cd react-native-permissions-module

```

Este comando cria a estrutura básica de uma biblioteca React Native. Ele já configura pastas e arquivos necessários para que a biblioteca seja instalada via npm e usada em projetos React Native.

A estrutura do projeto será algo como:

```
react-native-permissions-module/
├── android/
├── ios/
├── src/
├── README.md
├── package.json
├── index.js
```

2. Implementar o Módulo de Permissões no Android

Agora, dentro da pasta android, vamos adicionar o código de permissões que implementamos anteriormente.
Passo 2.1: Criar o Módulo de Permissões

Crie a estrutura de diretórios:

```
mkdir -p android/src/main/java/com/reactnativepermissionsmodule

```

Crie o arquivo PermissionsModule.java:

Caminho:

```android/src/main/java/com/reactnativepermissionsmodule/PermissionsModule.java```

Adicione o conteúdo:

```
package com.reactnativepermissionsmodule;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Build;

import androidx.annotation.NonNull;
import androidx.core.app.ActivityCompat;

import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.bridge.ReactContextBaseJavaModule;
import com.facebook.react.bridge.ReactMethod;
import com.facebook.react.bridge.Promise;
import com.facebook.react.bridge.ActivityEventListener;

public class PermissionsModule extends ReactContextBaseJavaModule {

    private static final int PERMISSION_REQUEST_CODE = 100;

    public PermissionsModule(ReactApplicationContext reactContext) {
        super(reactContext);
    }

    @NonNull
    @Override
    public String getName() {
        return "PermissionsModule";
    }

    @ReactMethod
    public void checkPermission(Promise promise) {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            if (getCurrentActivity().checkSelfPermission(Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED) {
                ActivityCompat.requestPermissions(getCurrentActivity(), new String[]{Manifest.permission.WRITE_EXTERNAL_STORAGE}, PERMISSION_REQUEST_CODE);
                promise.resolve(false);
            } else {
                promise.resolve(true);
            }
        } else {
            promise.resolve(true);
        }
    }
}
```

Passo 2.2: Criar o PermissionsPackage.java

Caminho:
```android/src/main/java/com/reactnativepermissionsmodule/PermissionsPackage.java```

Adicione o seguinte conteúdo:

```
package com.reactnativepermissionsmodule;

import com.facebook.react.ReactPackage;
import com.facebook.react.bridge.NativeModule;
import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.uimanager.ViewManager;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class PermissionsPackage implements ReactPackage {
    @Override
    public List<NativeModule> createNativeModules(ReactApplicationContext reactContext) {
        List<NativeModule> modules = new ArrayList<>();
        modules.add(new PermissionsModule(reactContext));
        return modules;
    }

    @Override
    public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
        return Collections.emptyList();
    }
}

```

3. Registrar o Módulo no Android

No arquivo ```android/src/main/AndroidManifest.xml```, adicione a permissão necessária:

```<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />```

No arquivo ```android/build.gradle```, garanta que o minSdkVersion seja compatível (normalmente 21 ou superior):

```
android {
    compileSdkVersion 33

    defaultConfig {
        minSdkVersion 21
    }
}
```

4. Expor o Módulo para o JavaScript

No arquivo ```index.js``` da biblioteca, expor o módulo nativo para o JavaScript:

```
import { NativeModules } from 'react-native';

const { PermissionsModule } = NativeModules;

export default PermissionsModule;
```

5. Adicionar Scripts para Compilação e Publicação

No arquivo package.json, adicione os scripts necessários para compilar e preparar a biblioteca:

```json

{
  "name": "react-native-permissions-module",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "android:build": "cd android && ./gradlew assembleRelease",
    "publish": "npm publish"
  },
  "peerDependencies": {
    "react": ">=17.0.1",
    "react-native": ">=0.64.0"
  }
}
```

6. Testar Localmente

Antes de publicar, é uma boa ideia testar a biblioteca em um projeto React Native localmente.

    Instale a biblioteca localmente: No projeto onde você quer testar a biblioteca, use o seguinte comando:

    ```bash

npm install ../caminho-para-a-sua-biblioteca/react-native-permissions-module
```

Link Manualmente (se não estiver usando autolinking):

Se o autolinking não funcionar, adicione manualmente o link do módulo Android no ```MainApplication.java:```

```java

import com.reactnativepermissionsmodule.PermissionsPackage;

@Override
protected List<ReactPackage> getPackages() {
    return Arrays.<ReactPackage>asList(
        new MainReactPackage(),
        new PermissionsPackage()  // Aqui está o módulo que criamos
    );
}
```

Executar o projeto:

```bash

    npx react-native run-android
```

Se funcionar corretamente, está pronto para publicação.

7. Publicar no NPM

Agora que tudo está configurado e funcionando, você pode publicar sua biblioteca no npm.

    Login no npm (se ainda não estiver logado):

    ```bash

npm login
```

Publicar a biblioteca:

```bash

    npm publish
```

Isso publicará sua biblioteca no npm, e ela estará disponível para qualquer pessoa instalar via:

```bash

npm install react-native-permissions-module
```

