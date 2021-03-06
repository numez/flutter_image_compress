# flutter_image_compress

[![pub package](https://img.shields.io/pub/v/flutter_image_compress.svg)](https://pub.dartlang.org/packages/flutter_image_compress)
![GitHub](https://img.shields.io/github/license/OpenFlutter/flutter_image_compress.svg)
[![GitHub stars](https://img.shields.io/github/stars/OpenFlutter/flutter_image_compress.svg?style=social&label=Stars)](https://github.com/OpenFlutter/flutter_image_compress)
[![Awesome](https://img.shields.io/badge/Awesome-Flutter-blue.svg?longCache=true&style=flat-square)](https://stackoverflow.com/questions/tagged/flutter?sort=votes)

Compresses image as native plugin (Obj-C/Kotlin)

This library can works on Android and iOS.

## Why

Q：Dart already has image compression libraries. Why use native?

A：For unknown reasons, image compression in Dart language is not efficient, even in release version. Using isolate does not solve the problem.

## Android

You may need to update Kotlin to version `1.2.71` or higher.

## iOS

No problems currently found.

## Usage

```yaml
dependencies:
  flutter_image_compress: ^0.2.4
```

```dart
import 'package:flutter_image_compress/flutter_image_compress.dart';
```

Use as:

[See full example](https://github.com/OpenFlutter/flutter_image_compress/blob/master/example/lib/main.dart)

```dart
  Future<List<int>> testCompressFile(File file) async {
    var result = await FlutterImageCompress.compressWithFile(
      file.absolute.path,
      minWidth: 2300,
      minHeight: 1500,
      quality: 94,
      rotate: 90,
    );
    print(file.lengthSync());
    print(result.length);
    return result;
  }

  Future<File> testCompressAndGetFile(File file, String targetPath) async {
    var result = await FlutterImageCompress.compressAndGetFile(
        file.absolute.path, targetPath,
        quality: 88,
        rotate: 180,
      );

    print(file.lengthSync());
    print(result.lengthSync());

    return result;
  }

  Future<List<int>> testCompressAsset(String assetName) async {
    var list = await FlutterImageCompress.compressAssetImage(
      assetName,
      minHeight: 1920,
      minWidth: 1080,
      quality: 96,
      rotate: 180,
    );

    return list;
  }

  Future<List<int>> testComporessList(List<int> list) async {
    var result = await FlutterImageCompress.compressWithList(
      list,
      minHeight: 1920,
      minWidth: 1080,
      quality: 96,
      rotate: 135,
    );
    print(list.length);
    print(result.length);
    return result;
  }
```

## About `List<int>` vs `Uint8List`

You may need to convert `List<int>` to `Uint8List` to display images.

To use `Uint8List`, you need import package to your code like so:

![](https://ws1.sinaimg.cn/large/844036b9ly1fxhyu2opqqj20j802c3yr.jpg)

```dart
final image = Uint8List.fromList(imageList)
ImageProvider provider = MemoryImage(Uint8List.fromList(imageList));
```

Usage in `Image` Widget:

```dart
List<int> image = await testCompressFile(file);
ImageProvider provider = MemoryImage(Uint8List.fromList(image));

Image(
  image: provider ?? AssetImage("img/img.jpg"),
),
```

Write to file usage:

```dart
void writeToFile(List<int> image, String filePath) {
  final file = File(filePath);
  file.writeAsBytes(image, flush: true, mode: FileMode.write);
}
```

## Troubleshooting

### Compressing returns `null`

Sometimes, compressing will return null. You should check if you can read/write the file, and the parent folder of the target file must exist.

For example, use the [path_provider](https://pub.dartlang.org/packages/path_provide) plugin to access some application folders, and use a permission plugin to request permission to access SD cards on Android/iOS.

## Android build error

```
Caused by: org.gradle.internal.event.ListenerNotificationException: Failed to notify project evaluation listener.
        at org.gradle.internal.event.AbstractBroadcastDispatch.dispatch(AbstractBroadcastDispatch.java:86)
        ...
Caused by: java.lang.AbstractMethodError
        at org.jetbrains.kotlin.gradle.plugin.KotlinPluginKt.resolveSubpluginArtifacts(KotlinPlugin.kt:776)
        ...
```

See [flutter/flutter/issues#21473](https://github.com/flutter/flutter/issues/21473#issuecomment-420434339)

You need to upgrade your Kotlin version to `1.2.71+`.

If Flutter supports more platforms (Windows, Mac, Linux, etc) in the future and you use this library, propose an issue or PR!

## About EXIF information

Using this library, EXIF information will be removed.
