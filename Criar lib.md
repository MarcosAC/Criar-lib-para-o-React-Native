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

```java
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

```java
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

```js
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

=====================================================================================================

Passos para criar uma biblioteca nativa iOS em React Native:

Configurar o Projeto:

Crie um projeto React Native ou adicione a biblioteca a um projeto existente.
Dentro do diretório ios, crie um novo módulo nativo onde escreveremos o código em Swift ou Objective-C para lidar com FTP.

Usar um Framework FTP no iOS:

O iOS não possui uma biblioteca nativa para FTP. Por isso, é necessário integrar um framework de terceiros, como CFFTPStream (que está obsoleto) ou um cliente FTP moderno. Uma opção popular é usar FTPManager, que é uma biblioteca Swift simples para FTP.

Criar o Módulo Nativo:

No diretório ios, crie um novo arquivo para o módulo nativo. Se você estiver usando Swift, certifique-se de ter o Bridging Header configurado.

Implementação Swift para FTP: Aqui está um exemplo de como enviar um arquivo via FTP usando Swift.

Passo a passo detalhado:

1. Instalar dependências

Certifique-se de ter um projeto React Native configurado corretamente. No terminal, navegue até o diretório do seu projeto e execute:

```bash
npx react-native init MyProject
cd MyProject
```

2. Adicionar módulo nativo no iOS

Navegue até o diretório ios e crie uma nova pasta para o módulo FTP, por exemplo, RNFTPModule.
No Xcode, crie um novo arquivo Swift dentro dessa pasta e certifique-se de que o Bridging Header esteja configurado para permitir a comunicação entre Swift e Objective-C.

3. Instalar FTPManager

Adicione a dependência FTPManager ao seu projeto iOS via CocoaPods. No diretório ios, abra o arquivo Podfile e adicione a linha:

```bash
pod 'FTPManager'
```

Depois, execute pod install para instalar a dependência:

```
cd ios
pod install
```
4. Código Swift para o módulo FTP

Crie o arquivo Swift do módulo nativo no diretório iOS, e implemente o seguinte código para enviar o arquivo via FTP:

```swift
import FTPManager
import React

@objc(RNFTPModule)
class RNFTPModule: NSObject {
  
  @objc(uploadFile:toServer:withUsername:password:resolver:rejecter:)
  func uploadFile(filePath: String, serverURL: String, username: String, password: String, resolver: @escaping RCTPromiseResolveBlock, rejecter: @escaping RCTPromiseRejectBlock) {
    
    let ftpManager = FTPManager()
    let fileURL = URL(fileURLWithPath: filePath)
    let server = URL(string: serverURL)!
    
    ftpManager.uploadFile(fileURL, to: server, user: username, password: password) { (success, error) in
      if success {
        resolver("File uploaded successfully")
      } else if let error = error {
        rejecter("FTP_UPLOAD_ERROR", "Failed to upload file", error)
      }
    }
  }
  
  @objc static func requiresMainQueueSetup() -> Bool {
    return false
  }
}
```

Esse código usa a biblioteca FTPManager para enviar um arquivo. O método uploadFile recebe o caminho do arquivo, a URL do servidor, o nome de usuário e a senha, e utiliza promessas para enviar a resposta ao React Native.

5. Criar o Bridging Header

Certifique-se de criar o arquivo Bridging-Header.h corretamente para garantir a integração do Swift com o código Objective-C no projeto React Native.

6. Registrar o Módulo no React Native

Para que o React Native possa usar o módulo, adicione o seguinte código no arquivo ios/MyProject/AppDelegate.m:

```swift
#import <React/RCTBridgeModule.h>

@interface RCT_EXTERN_MODULE(RNFTPModule, NSObject)
RCT_EXTERN_METHOD(uploadFile:(NSString *)filePath toServer:(NSString *)serverURL withUsername:(NSString *)username password:(NSString *)password resolver:(RCTPromiseResolveBlock)resolver rejecter:(RCTPromiseRejectBlock)rejecter)
@end
```

7. Usar o módulo no JavaScript

Agora que o módulo está implementado, você pode usá-lo no código JavaScript/TypeScript do React Native:

```js
import { NativeModules } from 'react-native';
const { RNFTPModule } = NativeModules;

const uploadFileToFTP = async () => {
  try {
    const result = await RNFTPModule.uploadFile(
      '/path/to/file.txt', // Caminho do arquivo no dispositivo
      'ftp://ftpserver.com', // URL do servidor FTP
      'username', // Nome de usuário
      'password'  // Senha
    );
    console.log(result); // Sucesso
  } catch (error) {
    console.error(error); // Falha
  }
};
```
