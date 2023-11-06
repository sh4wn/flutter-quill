# Flutter Quill

<p align="center" style="background-color:#282C34">
  <img src="https://user-images.githubusercontent.com/10923085/119221946-2de89000-baf2-11eb-8285-68168a78c658.png" width="600px">
</p>
<h1 align="center">A rich text editor for Flutter</h1>

[![MIT License][license-badge]][license-link]
[![PRs Welcome][prs-badge]][prs-link]
[![Watch on GitHub][github-watch-badge]][github-watch-link]
[![Star on GitHub][github-star-badge]][github-star-link]
[![Watch on GitHub][github-forks-badge]][github-forks-link]

[license-badge]: https://img.shields.io/github/license/singerdmx/flutter-quill.svg?style=for-the-badge
[license-link]: https://github.com/singerdmx/flutter-quill/blob/master/LICENSE
[prs-badge]: https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=for-the-badge
[prs-link]: https://github.com/singerdmx/flutter-quill/issues
[github-watch-badge]: https://img.shields.io/github/watchers/singerdmx/flutter-quill.svg?style=for-the-badge&logo=github&logoColor=ffffff
[github-watch-link]: https://github.com/singerdmx/flutter-quill/watchers
[github-star-badge]: https://img.shields.io/github/stars/singerdmx/flutter-quill.svg?style=for-the-badge&logo=github&logoColor=ffffff
[github-star-link]: https://github.com/singerdmx/flutter-quill/stargazers
[github-forks-badge]: https://img.shields.io/github/forks/singerdmx/flutter-quill.svg?style=for-the-badge&logo=github&logoColor=ffffff
[github-forks-link]: https://github.com/singerdmx/flutter-quill/network/members

---

FlutterQuill is a rich text editor and a [Quill] component for [Flutter].

This library is a WYSIWYG editor built for the modern Android, iOS, web and desktop platforms. Check out our [Youtube Playlist] or [Code Introduction] to take a detailed walkthrough of the code base. You can join our [Slack Group] for discussion.

Pub: [FlutterQuill]

## Table of contents
- [Flutter Quill](#flutter-quill)
  - [Table of contents](#table-of-contents)
  - [Installation](#installation)
  - [Usage](#usage)
  - [Migration](#migration)
  - [Input / Output](#input--output)
  - [Configurations](#configurations)
    - [Using Custom App Widget](#using-custom-app-widget)
    - [Font Size](#font-size)
    - [Font Family](#font-family)
    - [Custom Buttons](#custom-buttons)
  - [Embed Blocks](#embed-blocks)
    - [Using the embed blocks from `flutter_quill_extensions`](#using-the-embed-blocks-from-flutter_quill_extensions)
    - [Custom Size Image for Mobile](#custom-size-image-for-mobile)
    - [Custom Size Image for other platforms (excluding web)](#custom-size-image-for-other-platforms-excluding-web)
    - [Custom Embed Blocks](#custom-embed-blocks)
    - [Custom Toolbar](#custom-toolbar)
    - [Translation](#translation)
    - [](#)
      - [Contributing to translations](#contributing-to-translations)
  - [Conversion to HTML](#conversion-to-html)
  - [Testing](#testing)
  - [License](#license)
  - [Contributors](#contributors)

## Installation

```yaml
dependencies:
  flutter_quill: ^<latest-version-here>
```

<p align="center">OR</p>

```yaml
dependencies:
  flutter_quill:
    git: https://github.com/singerdmx/flutter-quill.git
```

>
> Using the latest version and reporting any issues you encounter on GitHub will greatly contribute to the improvement of the library. Your input and insights are valuable in shaping a stable and reliable version for all our users. Thank you for being part of the open-source community!
>
> Please use the latest pre-release of [FlutterQuill Extensions] in order to work with the latest stable version of [FlutterQuill]
>

## Usage

See the `example` directory for a minimal example of how to use FlutterQuill.  You typically just need to instantiate a controller:

```dart
QuillController _controller = QuillController.basic();
```

and then embed the toolbar and the editor, within your app.  For example:

```dart
QuillProvider(
  configurations: QuillConfigurations(
    controller: _controller,
    sharedConfigurations: const QuillSharedConfigurations(
      locale: Locale('de'),
    ),
  ),
  child: Column(
    children: [
      const QuillToolbar(),
      Expanded(
        child: QuillEditor.basic(
          configurations: const QuillEditorConfigurations(
            readOnly: false,
          ),
        ),
      )
    ],
  ),
)
```

And depending on your use case, you might want to dispose the `_controller` in dispose mehtod

Check out [Sample Page] for more advanced usage.

## Migration
We have recently add [migration guide](/doc/migration.md) for migration from different versions

## Input / Output

This library uses [Quill] as an internal data format.

* Use `_controller.document.toDelta()` to extract the deltas.
* Use `_controller.document.toPlainText()` to extract plain text.

FlutterQuill provides some JSON serialization support, so that you can save and open documents.  To save a document as JSON, do something like the following:

```dart
var json = jsonEncode(_controller.document.toDelta().toJson());
```

You can then write this to storage.

To open a FlutterQuill editor with an existing JSON representation that you've previously stored, you can do something like this:

```dart
var myJSON = jsonDecode(r'{"insert":"hello\n"}');
_controller = QuillController(
          document: Document.fromJson(myJSON),
          selection: TextSelection.collapsed(offset: 0),
        );
```

## Configurations

The `QuillToolbar` class lets you customize which formatting options are available.
[Sample Page] provides sample code for advanced usage and configuration.

For **web development**, use `flutter config --enable-web` for flutter or use [ReactQuill] for React.

It is required to provide `EmbedBuilder`, e.g. [defaultEmbedBuildersWeb](https://github.com/singerdmx/flutter-quill/blob/master/example/lib/universal_ui/universal_ui.dart#L99).

### Using Custom App Widget

This project use some adaptive widgets like `AdaptiveTextSelectionToolbar` which require the following delegates:

1. Default Material Localizations delegate
2. Default Cupertino Localizations delegate
3. Defualt Widgets Localizations delegate

You don't need to include those since there are defined by default
 but if you are using Custom app or you are overriding the `localizationsDelegates` in the App widget
then please make sure it's including those:

```dart
localizationsDelegates: const [
    DefaultCupertinoLocalizations.delegate,
    DefaultMaterialLocalizations.delegate,
    DefaultWidgetsLocalizations.delegate,
],
```

And you might need more depending on your use case, for example if you are using custom localizations for your app, using custom app widget like `FluentApp` from [FluentUI]
which will also need

```dart
localizationsDelegates: const [
    // Required localizations delegates ...
    FluentLocalizations.delegate,
    AppLocalizations.delegate,
],
```

in addition to the required delegates by this library

Note: In the latest versions of `FluentApp` you no longer need to add the `localizationsDelegates` but this is just an example, for more [info](https://github.com/bdlukaa/fluent_ui/pull/946)

### Font Size

Within the editor toolbar, a drop-down with font-sizing capabilities is available. This can be enabled or disabled with `showFontSize`.

When enabled, the default font-size values can be modified via _optional_ `fontSizeValues`.  `fontSizeValues` accepts a `Map<String, String>` consisting of a `String` title for the font size and a `String` value for the font size.  Example:

```dart
fontSizeValues: const {'Small': '8', 'Medium': '24.5', 'Large': '46'}
```

Font size can be cleared with a value of `0`, for example:

```dart
fontSizeValues: const {'Small': '8', 'Medium': '24.5', 'Large': '46', 'Clear': '0'}
```

### Font Family

To use your own fonts, update your [assets folder](https://github.com/singerdmx/flutter-quill/tree/master/example/assets/fonts) and pass in `fontFamilyValues`. More details at [this change](https://github.com/singerdmx/flutter-quill/commit/71d06f6b7be1b7b6dba2ea48e09fed0d7ff8bbaa), [this article](https://stackoverflow.com/questions/55075834/fontfamily-property-not-working-properly-in-flutter) and [this](https://www.flutterbeads.com/change-font-family-flutter/).

### Custom Buttons

You may add custom buttons to the _end_ of the toolbar, via the `customButtons` option, which is a `List` of `QuillCustomButton`.

To add an Icon, we should use a new QuillCustomButton class

```dart
    QuillCustomButton(
        iconData: Icons.ac_unit,
        onTap: () {
          debugPrint('snowflake');
        }
    ),
```

Each `QuillCustomButton` is used as part of the `customButtons` option as follows:

```dart
QuillToolbar(
  configurations: QuillToolbarConfigurations(
    customButtons: [
      QuillCustomButton(
        iconData: Icons.ac_unit,
        onTap: () {
          debugPrint('snowflake1');
        },
      ),
      QuillCustomButton(
        iconData: Icons.ac_unit,
        onTap: () {
          debugPrint('snowflake2');
        },
      ),
      QuillCustomButton(
        iconData: Icons.ac_unit,
        onTap: () {
          debugPrint('snowflake3');
        },
      ),
    ],
  ),
),
```

## Embed Blocks

As of version 6.0, embed blocks are not provided by default as part of this package. Instead, this package provides an interface to all the user to provide there own implementations for embed blocks. Implementations for image, video and formula embed blocks is proved in a separate package [`flutter_quill_extensions`](https://pub.dev/packages/flutter_quill_extensions).

Provide a list of embed

### Using the embed blocks from `flutter_quill_extensions`

To see how to use the extensions package, please take a look at the [README](./flutter_quill_extensions/README.md) of [FlutterQuill Extensions]

### Custom Size Image for Mobile

Define `mobileWidth`, `mobileHeight`, `mobileMargin`, `mobileAlignment` as follows:

```dart
{
      "insert": {
         "image": "https://user-images.githubusercontent.com/122956/72955931-ccc07900-3d52-11ea-89b1-d468a6e2aa2b.png"
      },
      "attributes":{
         "style":"mobileWidth: 50; mobileHeight: 50; mobileMargin: 10; mobileAlignment: topLeft"
      }
}
```

### Custom Size Image for other platforms (excluding web)

Define `width`, `height`, `margin`, `alignment` as follows:

```dart
{
      "insert": {
         "image": "https://user-images.githubusercontent.com/122956/72955931-ccc07900-3d52-11ea-89b1-d468a6e2aa2b.png"
      },
      "attributes":{
         "style":"width: 50; height: 50; margin: 10; alignment: topLeft"
      }
}
```

### Custom Embed Blocks

Sometimes you want to add some custom content inside your text, custom widgets inside of them. An example is adding notes to the text, or anything custom that you want to add in your text editor.

Open this [page](./doc/custom_embed_blocks.md) for more info


### Custom Toolbar
If you want to use custom toolbar but still want the support of this libray

Open this [page](./doc/custom_toolbar.md) for more info

### Translation

The package offers translations for the quill toolbar and editor, it will follow the system locale unless you set your own locale with:

```dart
 QuillProvider(
  configurations: QuillConfigurations(
    controller: _controller,
    sharedConfigurations: const QuillSharedConfigurations(
      locale: Locale('fr'),
    ),
  ),
  child: Column(
    children: [
      const QuillToolbar(
        configurations: QuillToolbarConfigurations(),
      ),
      Expanded(
        child: QuillEditor.basic(
          configurations: const QuillEditorConfigurations(),
        ),
      )
    ],
  ),
)
```

###

Currently, translations are available for these 31 locales:

* `Locale('en')`, `Locale('ar')`, `Locale('bn')`, `Locale('bs')`
* `Locale('cs')`, `Locale('de')`, `Locale('da')`, `Locale('fr')`
* `Locale('he')`, `Locale('zh', 'cn')`, `Locale('zh', 'hk')`, `Locale('ko')`
* `Locale('ru')`, `Locale('es')`, `Locale('tk')`, `Locale('tr')`
* `Locale('uk')`, `Locale('ur')`, `Locale('pt')`, `Locale('pl')`
* `Locale('vi')`, `Locale('id')`, `Locale('it')`, `Locale('ms')`
* `Locale('nl')`, `Locale('no')`, `Locale('fa')`, `Locale('hi')`
* `Locale('sr')`, `Locale('sw')`, `Locale('ja')`

#### Contributing to translations

The translation file is located at [toolbar.i18n.dart](lib/src/translations/toolbar.i18n.dart). Feel free to contribute your own translations, just copy the English translations map and replace the values with your translations. Then open a pull request so everyone can benefit from your translations!

## Conversion to HTML

Having your document stored in Quill Delta format is sometimes not enough. Often you'll need to convert
it to other formats such as HTML in order to publish it, or send an email. One option is to use
[vsc_quill_delta_to_html](https://pub.dev/packages/vsc_quill_delta_to_html) to convert your document
to HTML. This package has full support for all Quill operations - including images, videos, formulas,
tables, and mentions. Conversion can be performed in vanilla Dart (i.e., server-side or CLI) or in Flutter.
It is a complete Dart part of the popular and mature [quill-delta-to-html](https://www.npmjs.com/package/quill-delta-to-html)
Typescript/Javascript package.

## Testing

Please use [flutter_quill_test](https://pub.dev/packages/flutter_quill_test) for testing

## License

[MIT](LICENSE)

## Contributors

Special thanks for everyone that have contributed to this project...

<a href="https://github.com/singerdmx/flutter-quill/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=singerdmx/flutter-quill" />
</a>

<br>

Made with [contrib.rocks](https://contrib.rocks).

We welcome contributions!

Please follow these guidelines when contributing to our project. See [CONTRIBUTING.md](./CONTRIBUTING.md) for more details.

[Quill]: https://quilljs.com/docs/formats
[Flutter]: https://github.com/flutter/flutter
[FlutterQuill]: https://pub.dev/packages/flutter_quill
[FlutterQuill Extensions]: https://pub.dev/packages/flutter_quill_extensions
[ReactQuill]: https://github.com/zenoamaro/react-quill
[Youtube Playlist]: https://youtube.com/playlist?list=PLbhaS_83B97vONkOAWGJrSXWX58et9zZ2
[Slack Group]: https://join.slack.com/t/bulletjournal1024/shared_invite/zt-fys7t9hi-ITVU5PGDen1rNRyCjdcQ2g
[Sample Page]: https://github.com/singerdmx/flutter-quill/blob/master/example/lib/pages/home_page.dart
[Code Introduction]: https://github.com/singerdmx/flutter-quill/blob/master/doc/CodeIntroduction.md
[FluentUI]: https://pub.dev/packages/fluent_ui

<hr/>

[中文文档](./doc/readme/cn.md)
