# Laravel Nova Editor JS Field

[![Latest Version on Github](https://img.shields.io/github/release/advoor/nova-editor-js.svg?style=flat-square)](https://packagist.org/packages/advoor/nova-editor-js)
[![Total Downloads](https://img.shields.io/packagist/dt/advoor/nova-editor-js.svg?style=flat-square)](https://packagist.org/packages/advoor/nova-editor-js)

A Laravel Nova implementation of [Editor.js](https://github.com/codex-team/editor.js)
by [@advoor](https://github.com/advoor).

## Installation

Install via composer:

```
composer require advoor/nova-editor-js
```

Publish the config file

```
php artisan vendor:publish --provider="Advoor\NovaEditorJs\FieldServiceProvider"
```

## Version Compatibility

Laravel Nova 4.x isn't backwards compatible with 3.x, so we had to make a version split.
Please use the below table to find which versions are suitable for your installation.

| Package version | Nova Version | Laravel Version | PHP version |
| --------------- | ------------ | --------------- | ----------- |
| `4.x`           | 4.x - 5.x    | 10.x - 12.x     | 8.2+        |
| `3.x`           | 4.x          | 8.x - 10.x      | 8.1+        |
| `2.x`           | 2.x - 3.x    | 5.x - 8.x       | 5.6 - 7.4   |

Note that we really pushed the PHP version up. If you're staying on
new versions of Laravel and Nova, we're expecting your PHP version to match that behaviour.

## Upgrade

See [the upgrade guide](./UPGRADING.md).

## Usage

To add EditorJS to your application, you'll need to modify your Nova resource.
For ease-of-use we also recommend to update your models, but that's optional.

### Updating your Nova resource

This package exposes a `NovaEditorJsField` that takes care of displaying the HTML contents
and providing the user with the EditorJS field.

To use it, simply import the field,

```php
use Advoor\NovaEditorJs\NovaEditorJsField;
```

use it in your fields array,

```php
return [
    // …
    NovaEditorJsField::make('about'),
];
```

And boom, you've got yourself a fancy editor.

### Updating your models (optional)

For ease-of-use, we recommend you add the `NovaEditorJsCast` to the `$casts` on your models.
This will map the value to a `NovaEditorJsData` model, which can be returned in Blade (rendering HTML), or sent
via API calls (rendering JSON, unless you call `toHtml` on it or cast it to a string).

```php
use Advoor\NovaEditorJs\NovaEditorJsCast;

class User extends Model {
    protected $casts = [
        'about' => NovaEditorJsCast::class,
    ];
}
```

Since the `NovaEditorJsData` model is an `Htmlable`, Blade will recognize it as
safe HTML. This means you don't have to use Blade "unescaped statements".

```blade
<article>
    <h1>About {{ $user->name }}</h1>
    {{ $user->about }}
</article>
```

### Rendering HTML without model changes

You can also use the `NovaEditorJs` facade to render HTML from stored data.

```php
NovaEditorJs::generateHtmlOutput($user->about);
```

The return value of `generateHtmlOutput` is an `HtmlString`, which is treated as
safe by Blade. This means you don't have to use Blade "unescaped statements".

```blade
<article>
    <h1>About {{ $user->name }}</h1>
    {{ NovaEditorJs::generateHtmlOutput($user->about) }}
</article>
```

## Customizing

You can configure the editor settings and what tools the Editor should use, by
updating the `editorSettings` and `toolSettings` property in the config file
respectively.

From the config, you can define the following editor settings:

- `placeholder` ([docs][placeholder-docs]) - The placeholder to show in an empty editor
- `defaultBlock` ([docs][defaultblock-docs]) - The block that's used by default
- `autofocus` ([docs][autofocus-docs]) - If the editor should auto-focus, only use if you never have multiple editors on
  a page and after considering the
  [accessibility implications][autofocus-accessibility]
- `rtl` ([docs][rtl-docs]) - Set to true to enable right-to-left mode, for languages like Arabic and Hebrew

[placeholder-docs]: https://editorjs.io/configuration#placeholder
[defaultblock-docs]: https://editorjs.io/configuration#change-the-default-block
[autofocus-docs]: https://editorjs.io/configuration#autofocus
[autofocus-accessibility]: https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/autofocus#accessibility_considerations
[rtl-docs]: https://editorjs.io/i18n#rtl-support

Furthermore, you can customize the tools the editor should use. The following tools are enabled by default:

- [Header](https://github.com/editor-js/header)
- [Image](https://github.com/editor-js/image)
- [Link](https://github.com/editor-js/link)
- [List](https://github.com/editor-js/list)
- [Code block](https://github.com/editor-js/code)
- [Inline code](https://github.com/editor-js/inline-code)
- [Checklist](https://github.com/editor-js/checklist)
- [Marker](https://github.com/editor-js/marker)
- [Embeds](https://github.com/editor-js/embed)<sup>†</sup>
- [Delimiter](https://github.com/editor-js/delimiter)
- [Table](https://github.com/editor-js/table)
- [Raw](https://github.com/editor-js/raw)

You can customize the views for each component, by changing the view in `resources/views/vendor/nova-editor-js/`.

<sup>†</sup> The _Embeds_ tool is triggered by pasting URLs to embeddable
content. It does not have an entry in the "Add" menu.

### Changing the image driver

You may change the image driver (default Imagick) via the configuration like so:

```php
return [
  'toolSettings' => [
    'image' => [
      'imagedriver' => Spatie\Image\Enums\ImageDriver::Gd,
      // ...
    ],
    // ...
  ],
  // ...
];
```

Any option set here must be of type `Spatie\Image\Enums\ImageDriver`.

### Registering custom components

Please refer to the [extending Nova EditorJS](./EXTENDING.md) guide on instructions on how to register custom
components.
