User:
How to change app display name in Flutter?

Assistant:
To change the display name of a Flutter application, you need to update the appropriate configuration files for both iOS and Android platforms. Here are the steps you need to follow:

### Android

1. **Open `AndroidManifest.xml`:**
    - Navigate to **`android/app/src/main/AndroidManifest.xml`**.

2. **Modify the `android:label` attribute:**
    - Update the `android:label` attribute within the `<application>` tag to your desired app name.

   ```xml
   <application
       android:name="io.flutter.app.FlutterApplication"
       android:label="Your New App Name"
       android:icon="@mipmap/ic_launcher">
       <activity
           android:name=".MainActivity"
           android:launchMode="singleTop"
           android:theme="@style/LaunchTheme"
           android:configChanges="orientation|keyboardHidden|keyboard|screenSize|smallestScreenSize|locale|layoutDirection|fontScale|screenLayout|density|uiMode"
           android:hardwareAccelerated="true"
           android:windowSoftInputMode="adjustResize">
           <meta-data
               android:name="io.flutter.embedding.android.NormalTheme"
               android:resource="@style/NormalTheme"
               />
           <intent-filter>
               <action android:name="android.intent.action.MAIN"/>
               <category android:name="android.intent.category.LAUNCHER"/>
           </intent-filter>
       </activity>
   </application>
   ```

### iOS

1. **Open `Info.plist`:**
    - Navigate to **`ios/Runner/Info.plist`**.

2. **Modify the `CFBundleDisplayName` entry:**
    - Add or update the `CFBundleDisplayName` key with your desired app name.

   ```xml
   <key>CFBundleDisplayName</key>
   <string>Your New App Name</string>
   ```

### Flutter

Additionally, if you are using a package like `flutter_launcher_icons` to manage your app icons, make sure to also update the name there (if needed).

1. **Open `pubspec.yaml`**:
    - Ensure that the app name is consistent across all configurations.

   ```yaml
   name: your_new_app_name
   description: A new Flutter application
   ```

### Summary

To summarize, change the app name in the following places:

- **Android**: `android/app/src/main/AndroidManifest.xml`
- **iOS**: `ios/Runner/Info.plist`
- **Flutter**: `pubspec.yaml`

After making these changes, you will need to rebuild your Flutter application:

```sh
flutter clean
flutter pub get
flutter run
```

This ensures that the changes are properly applied.

User:
name in pubspec.yaml is package name, not a display name

Assistant:
You're correct; the `name` field in `pubspec.yaml` is the package name, not the display name of the app. I apologize for the confusion. For changing the display name of your Flutter app, you only need to modify the files specific to the Android and iOS platforms as previously detailed.

### Summary

For Android, modify the `android:label` in `AndroidManifest.xml`:

```xml
<!-- Path: android/app/src/main/AndroidManifest.xml -->
<application
    android:name="io.flutter.app.FlutterApplication"
    android:label="Your New App Name"
    android:icon="@mipmap/ic_launcher">
    <activity
        android:name=".MainActivity"
        android:launchMode="singleTop"
        android:theme="@style/LaunchTheme"
        android:configChanges="orientation|keyboardHidden|keyboard|screenSize|smallestScreenSize|locale|layoutDirection|fontScale|screenLayout|density|uiMode"
        android:hardwareAccelerated="true"
        android:windowSoftInputMode="adjustResize">
        <meta-data
            android:name="io.flutter.embedding.android.NormalTheme"
            android:resource="@style/NormalTheme"
            />
        <intent-filter>
            <action android:name="android.intent.action.MAIN"/>
            <category android:name="android.intent.category.LAUNCHER"/>
        </intent-filter>
    </activity>
</application>
```

For iOS, update the `CFBundleDisplayName` in `Info.plist`:

```xml
<!-- Path: ios/Runner/Info.plist -->
<key>CFBundleDisplayName</key>
<string>Your New App Name</string>
```

### Additional Notes

For a more streamlined approach and better management of the application name and other configurations, you might also consider using the [flutter_launcher_name](https://pub.dev/packages/flutter_launcher_name) package. This package helps automate the process of updating the application name for both Android and iOS.

1. Add the package to your `pubspec.yaml` file:

```yaml
dev_dependencies:
  flutter_launcher_name: ^0.0.1
```

2. Create a `flutter_launcher_name.yaml` configuration file in the root of your project:

```yaml
# Place this file in the root of your project
flutter_launcher_name:
  name: "Your New App Name"
```

3. Run the following command to update your app's name:

```sh
flutter pub run flutter_launcher_name:main
```

This package will update the necessary files for you, ensuring consistency across both platforms.

User:
how to change app display name for Windows?

Assistant:
To change the display name of a Flutter application for Windows, you need to modify the appropriate configuration files.

Here are the steps to update the app display name for a Flutter application targeting Windows:

### Updating the Display Name for Windows

1. **Open `Runner.rc`:**
    - Navigate to **`windows/runner/Runner.rc`**.

2. **Update the `VALUE "FileDescription"` and `VALUE "ProductName"` entries:**
    - Change the values to your desired app name.

   ```c
   // Path: windows/runner/Runner.rc
   ...
   VALUE "FileDescription", "Your New App Name\0"
   VALUE "ProductName", "Your New App Name\0"
   ...
   ```

   These entries correspond to the display name that Windows shows for your application.

### Rebuild the Application

After making the changes, rebuild your Flutter application to apply the modifications:

```sh
flutter clean
flutter pub get
flutter build windows
```

### Recap

1. Modify the `VALUE "FileDescription"` and `VALUE "ProductName"` entries in the `windows/runner/Runner.rc` file to your new app name.
2. Rebuild your Flutter application to reflect the changes.

By following these steps, you will successfully change the display name of your Flutter application on the Windows platform.

User:
how to change app display name for Linux?

Assistant:
To change the display name of a Flutter application for Linux, you need to modify the appropriate configuration files.

### Updating the Display Name for Linux

1. **Open the `.desktop` file:**
    - Navigate to **`linux/flutter/ephemeral/flutter.desktop`**.

2. **Update the `Name` entry:**
    - Change the `Name` value to your desired app display name.

   ```ini
   # Path: linux/flutter/ephemeral/flutter.desktop
   [Desktop Entry]
   Name=Your New App Name
   Comment=Your Application Description
   Exec=YourExecutable
   Icon=YourIcon
   Terminal=false
   Type=Application
   ```

   The `Name` entry corresponds to the display name that Linux shows for your application.

3. **(Optional) Create or modify the `CMakeLists.txt` file:**
    - Navigate to **`linux/CMakeLists.txt`** (if it does not exist, create it).

4. **Update the `set_application_name` function:**
    - Make sure you set the application name properly in the CMake configuration if required.

   ```cmake
   # Path: linux/CMakeLists.txt
   cmake_minimum_required(VERSION 3.10)
   project(YourNewAppName)

   set(BINARY_NAME "your_new_app_name")

   add_executable(${BINARY_NAME} "my_application.cc" "main.cc")

   target_link_libraries(${BINARY_NAME} PRIVATE flutter_linux)
   ```

### Rebuild the Application

After making the changes, rebuild your Flutter application to apply the modifications:

```sh
flutter clean
flutter pub get
flutter build linux
```

### Recap

1. Modify the `Name` entry in the `linux/flutter/ephemeral/flutter.desktop` file to your new app name.
2. (Optional) Verify or set the application name in `linux/CMakeLists.txt` if required.
3. Rebuild your Flutter application to reflect the changes.

By following these steps, you will successfully change the display name of your Flutter application on the Linux platform.

User:
dont have ephemeral directory

Assistant:
If the `ephemeral` directory does not exist in the suggested path, you can create a `.desktop` file in the appropriate location and configure it accordingly. This file is essential for defining how the application should appear and behave within the Linux desktop environment.

### Steps to Create and Update the `.desktop` File

1. **Create the `.desktop` File:**

   Navigate to the `linux` directory of your Flutter project or create the directories as needed:

   ```sh
   mkdir -p linux/flutter
   cd linux/flutter
   touch your_app.desktop
   ```

2. **Configure the `.desktop` File:**

   Open the newly created `.desktop` file and update its contents with the necessary information:

   ```ini
   # Path: linux/flutter/your_app.desktop
   [Desktop Entry]
   Name=Your New App Name
   Comment=A description of your application
   Exec=path/to/your/executable
   Icon=path/to/your/icon
   Terminal=false
   Type=Application
   ```

    - `Name`: The name of the application as it will appear in your application launcher.
    - `Comment`: A brief description of your application.
    - `Exec`: The path to your executable file.
    - `Icon`: The path to the icon file for your application.
    - `Terminal`: Set to `false` if your application does not require a terminal window.
    - `Type`: Should be set to `Application`.

3. **Update the Build Process (Optional):**

   If your build process requires it, ensure that the `.desktop` file is copied to the appropriate location during the build. You can configure your `CMakeLists.txt` to include these steps.

   Open `linux/CMakeLists.txt` and add the necessary steps:

   ```cmake
   # Path: linux/CMakeLists.txt
   cmake_minimum_required(VERSION 3.10)
   project(YourNewAppName)

   set(BINARY_NAME "your_new_app_name")

   add_executable(${BINARY_NAME} "my_application.cc" "main.cc")

   target_link_libraries(${BINARY_NAME} PRIVATE flutter_linux)

   # Optionally copy the .desktop file to the build directory
   configure_file(flutter/your_app.desktop ${CMAKE_BINARY_DIR}/your_app.desktop COPYONLY)
   ```

4. **Rebuild Your Application:**

   Clean and rebuild your Flutter application to ensure the changes take effect:

   ```sh
   flutter clean
   flutter pub get
   flutter build linux
   ```

### Recap

1. Create and configure the `.desktop` file in the `linux/flutter` directory.
2. (Optional) Update `CMakeLists.txt` to include the `.desktop` file in the build process.
3. Rebuild your Flutter application to apply the changes.

By following these steps, you can successfully set and change the display name of your Flutter application on the Linux platform.
