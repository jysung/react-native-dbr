# Barcode Scanning Module for React Native

This is a sample that shows how to implement barcode scanning in React Native using Dynamsoft Barcode Reader SDK. 

To learn more about Dynamsoft Barcode Reader, please visit http://www.dynamsoft.com/Products/Dynamic-Barcode-Reader.aspx.


## License

You can request for a free trial license online. [Get a trial license >](https://www.dynamsoft.com/CustomerPortal/Portal/Triallicense.aspx)

Without a valid license, the SDK can work but will not return a full result.

<kbd><img src="http://www.codepool.biz/wp-content/uploads/2017/04/react-native-barcode-license.png" width="50%">

## Dependencies
```bash
Node, Python2, JDK, Watchman, Xcode and Android Studio. 
(Windows: The version of Node must be greater than or equal to 10 and less than or equal to 12.11, the version of Python must be 2.x (does not support 3.x), and the version of JDK must be 1.8)
```

## How to Run the Example

### iOS
Run `npm install or yarn (npm install -g yarn)` from the Example directory first, then `pod install` in the react-native-dbr/Libraries.
And make sure `Your Project Target -> Build Settings -> Search Paths -> Frameworks Search Paths`:
```bash
Frameworks Search Paths = "${PODS_ROOT}/DynamsoftBarcodeReader"
```
Then `react-native run-ios`.

### Android
```bash
npm install or yarn (npm install -g yarn)
react-native run-android
```

### Screenshots

<kbd><img src="http://www.codepool.biz/wp-content/uploads/2017/04/react-native-barcode-detection.jpg" width="50%">

<kbd><img src="http://www.codepool.biz/wp-content/uploads/2017/04/react-native-barcode-result.png" width="50%">

## How to Use the Barcode Scanning Module

### In Android

1. Create a new React Native project if you donnot have one.

    ```bash
    react-native init NewProject
    ```

2. Add the local module to dependencies in `package.json`.

    ```json
    "dependencies": {
        "react": "16.9.0",
        "react-native": "^0.61.1",
        "react-native-dbr": "^8.0.1"
    },
    ```

3. On `android/settings.gradle`.

    ```
    include ':react-native-dbr'
    project(':react-native-dbr').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-dbr/androidLib')
   ```

4. Add `':react-native-dbr'` dependency in `android/app/build.gradle`.

    ```
    dependencies {
        implementation project(path: ':react-native-dbr')
    }
    ```
5. On the MainApplication of your Android project add the import of BarcodeReaderPackage line to:

    ```java
    import com.demo.barcodescanner.BarcodeReaderPackage;
    
    ...
    @Override
    protected List<ReactPackage> getPackages() {
      @SuppressWarnings("UnnecessaryLocalVariable")
      List<ReactPackage> packages = new PackageList(this).getPackages();
      packages.add(new BarcodeReaderPackage()); // add BarcodeReaderPackage here!!
      return packages;
    }
    ...
    
    ```
6. Use the module in `App.js`.

    ```javascript
    import {NativeModules} from 'react-native';
    const BarcodeReaderManager = NativeModules.BarcodeReaderManager;
    
    //android
    BarcodeReaderManager.readBarcode('your license here',events => {
      this.setState({result: events});
    },err => {
      alert(err);
    }
    );
    ```

### In iOS

1. Create a new React Native project.

    ```bash
    react-native init NewProject
    ```

2. Add the local module to dependencies in `NewProject/package.json`.

    ```json
    "dependencies": {
        "react": "16.9.0",
        "react-native": "^0.61.1",
        "react-native-dbr": "^8.0.1"
    }
    ```

3. Remove `node_moudules` and install.

    ```bash
    sudo rm -rf node_moudules 
    npm install or yarn
    ```

4. Add `BarcodeReaderManager.xcodeproj` to  your project libraries. And make sure `Pods/Target Support Files/Pods-BarcodeReaderManager.debug.xcconfig`:

```
  FRAMEWORK_SEARCH_PATHS = "${PODS_ROOT}/DynamsoftBarcodeReader"
  HEADER_SEARCH_PATHS = "${PODS_ROOT}/DynamsoftBarcodeReader/DynamsoftBarcodeReader.framework/Headers"
  LIBRARY_SEARCH_PATHS = "${PODS_ROOT}/DynamsoftBarcodeReader"
  OTHER_LDFLAGS = -framework "DynamsoftBarcodeReader"
```

5.  In your NewProject: 

```
  Project -> Build Settings -> FRAMEWORK_SEARCH_PATHS = `$(PROJECT_DIR)/../Libraries` 
  HEADER_SEARCH_PATHS = `$(PROJECT_DIR)/../Libraries`
```

6. Modify the module in `App.js`(different from android).

    ```javascript
    import {NativeModules} from 'react-native';
    const BarcodeReaderManager = NativeModules.BarcodeReaderManager;
    
    //ios
    BarcodeReaderManager.readBarcode('your license here').then((msg) =>{
        this.setState({result: msg});
    }).catch((err) => {
        console.log(err);
    });
    ```

7. To achieve navigation from react-native to viewController, in `AppDelegate.h` and `AppDelegate.m`, add the following code:

    ```AppDelegate.h:
    ...
    @property (nonatomic, strong) UIWindow *window;
    @property (nonatomic, strong) UINavigationController *nav;
    @property (nonatomic, strong) RCTRootView *rootView;
    @property (nonatomic, strong) UIViewController *rootViewController;
    ...
    
    ```AppDelegate.m:
    #import "../../../ios/BarcodeReaderManagerViewController.h"
    #import "../../../ios/DbrManager.h"

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
    //  self.window.rootViewController = rootViewController;
        _nav = [[UINavigationController alloc] initWithRootViewController:_rootViewController];
        self.window.rootViewController = _nav;
        _nav.navigationBarHidden = YES;
        [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(doNotification:) name:@"readBarcode" object:nil];
        [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(backToJs:) name:@"backToJs" object:nil];
        [self.window makeKeyAndVisible];
        return YES;
    }

    -(void)doNotification:(NSNotification *)notification{
        BarcodeReaderManagerViewController* dbrMangerController = [[BarcodeReaderManagerViewController alloc] init];
        dbrMangerController.dbrManager = [[DbrManager alloc] initWithLicense:notification.userInfo[@"inputValue"]];
        [self.nav pushViewController:dbrMangerController animated:YES];
    }

    -(void)backToJs:(NSNotification *)notification{
        [self.nav popToViewController:self.rootViewController animated:YES];
    }
    ```

## Blog

[Android Barcode Detection Component for React Native](http://www.codepool.biz/android-barcode-detection-component-react-native.html)
