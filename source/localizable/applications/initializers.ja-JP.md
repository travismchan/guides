イニシャライザはアプリケーション起動時にアプリケーションを設定する機会を提供します。

イニシャライザには、アプリケーションイニシャライザとアプリケーションインスタンスイニシャライザの２種類があります。

アプリケーションイニシャライザはアプリケーション起動時に実行され、アプリケーションで[依存性の注入](../dependency-injection)を行うための主要な手段を提供します。

アプリケーションインスタンスイニシャライザは、アプリケーションインスタンスの読み込み時に実行されます。 アプリケーションの状態を初期化する手段を提供し、アプリケーションインスタンス固有の依存性注入を行う手段も提供します (A/Bテスト構成など)。

Operations performed in initializers should be kept as lightweight as possible to minimize delays in loading your application. Although advanced techniques exist for allowing asynchrony in application initializers (i.e. `deferReadiness` and `advanceReadiness`), these techniques should generally be avoided. Any asynchronous loading conditions (e.g. user authorization) are almost always better handled in your application route's hooks, which allows for DOM interaction while waiting for conditions to resolve.

## アプリケーションイニシャライザ

Application initializers can be created with Ember CLI's `initializer` generator:

```bash
ember generate initializer shopping-cart
```

Let's customize the `shopping-cart` initializer to inject a `cart` property into all the routes in your application:

```app/initializers/shopping-cart.js export function initialize(application) { application.inject('route', 'cart', 'service:shopping-cart'); };

export default { name: 'shopping-cart', initialize: initialize };

    <br />## Application Instance Initializers
    
    Application instance initializers can be created with Ember CLI's `instance-initializer` generator:
    
    ```bash
    ember generate instance-initializer logger
    

Let's add some simple logging to indicate that the instance has booted:

```app/instance-initializers/logger.js export function initialize(applicationInstance) { let logger = applicationInstance.lookup('logger:main'); logger.log('Hello from the instance initializer!'); }

export default { name: 'logger', initialize: initialize };

    <br />## Specifying Initializer Order
    
    If you'd like to control the order in which initializers run, you can use the `before` and/or `after` options:
    
    ```app/initializers/config-reader.js
    export function initialize(application) {
      // ... your code ...
    };
    
    export default {
      name: 'config-reader',
      before: 'websocket-init',
      initialize: initialize
    };
    

```app/initializers/websocket-init.js export function initialize(application) { // ... your code ... };

export default { name: 'websocket-init', after: 'config-reader', initialize: initialize };

    <br />```app/initializers/asset-init.js
    export function initialize(application) {
      // ... your code ...
    };
    
    export default {
      name: 'asset-init',
      after: ['config-reader', 'websocket-init'],
      initialize: initialize
    };
    

Note that ordering only applies to initializers of the same type (i.e. application or application instance). Application initializers will always run before application instance initializers.