# ContentBlocks plugin for CakePHP

<!-- TOC -->
* [Getting started](#getting-started)
  * [Setup](#setup)
    * [Install plugin via composer](#install-plugin-via-composer)
    * [Load the plugin](#load-the-plugin)
    * [Load the View Helper](#load-the-view-helper)
  * [Usage](#usage)
    * [Define content blocks](#define-content-blocks)
    * [Link to the admin interface](#link-to-the-admin-interface)
    * [Use content blocks in views](#use-content-blocks-in-views)
* [Reference](#reference)
  * [HTML Block](#html-block)
    * [Adding a HTML block](#adding-a-html-block)
    * [Rendering a HTML block](#rendering-a-html-block)
  * [Text Block](#text-block)
    * [Adding an text block](#adding-an-text-block)
  * [Image Block](#image-block)
    * [Adding an image block](#adding-an-image-block)
<!-- TOC -->

Make previously-static parts of your website dynamic.
Allow administrators and end users to edit blocks of content on the website without having to edit template files.

The left and right screenshots below show the same view + layout, but the administrator was able to modify
the logo, title, main content, and footer text. All this with only a few lines of code in the CakePHP application:

<img src="./docs/screenshot.png" />

## Getting started

### Setup

The following steps are performed once per app that wishes to use the plugin.
After it is setup, then you can use the plugin (see [Usage](#usage)).

#### Install plugin via composer
You can install this plugin into your CakePHP application using [composer](https://getcomposer.org).

```
composer require ugie-cake/cakephp-content-blocks
```

#### Load the plugin

You can either use the `cake` CLI:

```php
bin/cake plugin load ContentBlocks
```

*or* add the following line to the `bootstrap()` function of your `src/Application.php` file:

```php
$this->addPlugin('ContentBlocks');
```

#### Load the View Helper

Add the following line to the `initialize()` function of your `src/View/AppView.php` file:

```php
$this->loadHelper('ContentBlocks.ContentBlock');
```

### Usage

#### Define content blocks

Prior to showing content in your templates, you must first define what blocks are available.
This is done by inserting records into the `content_blocks` table, which is most easily done via [Seeds](https://book.cakephp.org/phinx/0/en/seeding.html).

Here is an example seed to create one content block of each type (`html`, `text`, and `image`):

```php
<?php
declare(strict_types=1);

use Migrations\AbstractSeed;

class ContentBlocksSeed extends AbstractSeed
{
    public function run(): void
    {
        $data = [
            [
                'parent' => 'global',
                'key' => 'website-title',
                'type' => 'text',
                'value' => 'Awesome CakePHP Website',
            ],
            [
                'parent' => 'about-us',
                'key' => 'about-us-content',
                'type' => 'html',
                'value' => '<h2>Our History</h2><p>We began in 2023 as a tool to help make CakePHP websites more maintainable.</p>',
            ],
            [
                'parent' => 'home',
                'key' => 'logo',
                'type' => 'image',
                'value' => '',
            ],
        ];

        $table = $this->table('content_blocks');
        $table->insert($data)->save();
    }
}

```

#### Link to the admin interface

In order for your administrators to be able to access the content blocks admin page,
your template needs to link to the `ContentBlocks` controller `index` action:

```php
<?= $this->Html->link('Content Blocks', ['plugin' => 'ContentBlocks', 'controller' => 'ContentBlocks', 'action' => 'index']) ?>
```

#### Use content blocks in views

Instead of hard coding content into views, output relevant content blocks.
This means that administrators never need to make code changes (or employ someone to make code changes)
if they require changes to most parts of the template.

This is done using the relevant `ContentBlocksHelper` functions:

```php
<?= $this->ContentBlock->html('home-content'); ?>
<?= $this->ContentBlock->image('logo'); ?>
<?= $this->ContentBlock->text('website-title'); ?>
```

## Reference

### HTML Block

<img src="./docs/screenshot-block-html.png" />

#### Adding a HTML block

Add a new record to the `content_blocks` table with a `type` of `html`.

An [example seed can be found in the `HtmlBlockSeed.php` file](./config/Seeds/HtmlBlockSeed.php):
```php
<?php
# File: config/Seeds/HtmlBlockSeed.php
class HtmlBlockSeed extends \Migrations\AbstractSeed
{
    public function run(): void
    {
        $data = [
            [
                'parent' => 'about-us',
                'slug' => 'about-us-content',
                'label' => 'About Us - Main Content',
                'description' => 'Main block of code shown on the About Us page.',
                'type' => 'html',
                'value' => '
                    <h2>Our Story</h2>
                    <p>We are a small business, established in 2023 who sell candles to sick children.</p>
                ',
            ],
        ];

        $this->table('content_blocks')->insert($data)->save();
    }
}
```

#### Rendering a HTML block

In a layout or view template, add the following:

```php
<?= $this->ContentBlock->html('block-name') ?>
```

### Text Block

<img src="./docs/screenshot-block-text.png" />

#### Adding an text block

Add a new record to the `content_blocks` table with a `type` of `text`.

An [example seed can be found in the `TextBlockSeed.php` file](./config/Seeds/TextBlockSeed.php):
```php
<?php
# File: config/Seeds/TextBlockSeed.php
class TextBlockSeed extends \Migrations\AbstractSeed
{
    public function run(): void
    {
        $data = [
            [
                'parent' => 'home',
                'slug' => 'website-title',
                'label' => 'Website Title',
                'description' => 'Heading shown on the main page, and also in the browser tab.',
                'type' => 'text',
                'value' => 'CakePHP Content Blocks Plugin',
            ],
        ];

        $this->table('content_blocks')->insert($data)->save();
    }
}
```

### Image Block

<img src="./docs/screenshot-block-image.png" />

#### Adding an image block

Add a new record to the `content_blocks` table with a `type` of `image`.

An [example seed can be found in the `ImageBlockSeed.php` file](./config/Seeds/ImageBlockSeed.php):
```php
<?php
# File: config/Seeds/ImageBlockSeed.php
class ImageBlockSeed extends \Migrations\AbstractSeed
{
    public function run(): void
    {
        $data = [
            [
                'parent' => 'global',
                'slug' => 'logo',
                'label' => 'Logo',
                'description' => 'Shown on the home page, and also in the top left of each other page.',
                'type' => 'image',
                'value' => 'CakePHP Content Blocks Plugin',
            ],
        ];

        $this->table('content_blocks')->insert($data)->save();
    }
}
```