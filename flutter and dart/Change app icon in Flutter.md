User:
How to change app icon in Flutter?

Assistant:
To change the app icon in a Flutter project, you can use the `flutter_launcher_icons` package. Here is a step-by-step guide to help you through the process:

1. **Add the `flutter_launcher_icons` package to your `pubspec.yaml` file:**

    ```yaml
    dev_dependencies:
      flutter_launcher_icons: ^0.10.0

    flutter_launcher_icons:
      android: true
      ios: true
      image_path: "assets/icon/icon.png"
    ```

   Here, `image_path` should point to your desired app icon. Make sure your icon is a square 512x512 PNG image for best results and place it in your Flutter project's assets directory.

2. **Ensure the flutter assets are correctly declared in the `pubspec.yaml`:**

    ```yaml
    flutter:
      assets:
        - assets/icon/icon.png
    ```

3. **Run the package:**

   Use the following command in your terminal to generate the app icons:

    ```sh
    flutter pub run flutter_launcher_icons:main
    ```

   This command will automatically replace the existing app icons in both the iOS and Android modules with your new icon.

4. **Verify the generated icons:**

   After running the command, navigate to the respective folders to verify the new icons:

    - **Android:** Navigate to `android/app/src/main/res/` and check the `mipmap-` folders.
    - **iOS:** Navigate to `ios/Runner/Assets.xcassets/AppIcon.appiconset/` and verify the icons.

5. **Run your Flutter application:**

    ```sh
    flutter run
    ```

   Make sure to test your app on both Android and iOS to see the new icon in action.

By following the above steps, you should be able to successfully change the app icon for your Flutter project.

User:
Why on MacOS default icon shown in taskbar when app is launched?

Assistant:
If your Flutter app shows the default icon in the taskbar when launched on macOS, it may be because the app icon wasn't correctly configured for the macOS platform. Here are steps to ensure that the correct icon is used:

1. **Prepare your icon assets:**
   Ensure you have your app icon prepared in the required sizes. You can use a 1024x1024 PNG image and then utilize tools like [Icon Set Creator](https://apps.apple.com/us/app/icon-set-creator/id1062131436?mt=12) to create the necessary icon set.

2. **Create the iconset:**
    - Use a tool or script to generate an `.iconset` folder from your 1024x1024 icon. For example, with Icon Set Creator, you can drag your 1024x1024 image into the app, and it will generate the necessary `.iconset` structure.
    - Alternatively, you can manually create an iconset by resizing your image to the required dimensions (16x16, 32x32, 128x128, etc.) and placing these images into a folder.

3. **Convert the iconset to ICNS:**
   Open Terminal and use the `iconutil` command to convert your iconset folder to an ICNS file:

   ```sh
   iconutil -c icns <path_to_iconset_folder>/AppIcon.iconset
   ```

   This will create an `AppIcon.icns` file.

4. **Place the ICNS file in your Flutter project:**
    - Copy the generated `AppIcon.icns` file into your Flutter project at `macos/Runner/Assets.xcassets/AppIcon.appiconset/`.

5. **Update the macOS project settings:**
   Open the `macos/Runner/Assets.xcassets/AppIcon.appiconset/Contents.json` file and ensure it has valid entries for your ICNS file. Here is a sample `Contents.json`:

    ```json
    {
      "images" : [
        {
          "filename" : "AppIcon.icns",
          "idiom" : "mac"
        }
      ],
      "info" : {
        "version" : 1,
        "author" : "xcode"
      }
    }
    ```

6. **Ensure `pubspec.yaml` includes macos assets:**
   Add to your `pubspec.yaml` if not already done:

    ```yaml
    flutter:
      assets:
        - macos/Runner/Assets.xcassets/AppIcon.appiconset/AppIcon.icns
    ```

7. **Clean and rebuild your project:**

    ```sh
    flutter clean
    flutter pub get
    flutter run
    ```

Following these steps should replace the default icon with your custom app icon in the macOS taskbar when your Flutter app is launched.
