> :warning::warning::warning::warning::warning:
> 
> **This project is deprecated and obsolete.**
> 
> The baseline is too soft, prefer the use of this tool: [Angry bearded](https://github.com/johnatas-x/angry-bearded) by adapting the rules to your needs.
> 
> This project is based on [axelerant](https://packagist.org/packages/axelerant/drupal-quality-checker) and [vijaycs85](https://packagist.org/packages/vijaycs85/drupal-quality-checker) and the last version

# Package for Drupal Code Quality presets

This has been customised from [axelerant/drupal-quality-checker](https://packagist.org/packages/axelerant/drupal-quality-checker) which has been customised from [vijaycs85/drupal-quality-checker](https://packagist.org/packages/vijaycs85/drupal-quality-checker).
The goal is to have the initial project up to date while taking advantage of Axelerant's improvements. In order to be as global as possible, the Axelerant's logo has been replaced by Drupal's logo (from the initial project).

## Installation

_Upgrading from Beta 8?_ Read [the instructions for changes](#upgrading-from-beta-8) you need to make to grumphp.yml.dist.

### When using drupal/core-composer-scaffold (recommended)

In most cases, you would already be using the [`drupal/core-composer-scaffold`](https://packagist.org/packages/drupal/core-composer-scaffold) package if you have set up using the latest Drupal templates. This package uses `core-composer-scaffold` to set up configuration files in your project. To make this work, add this package name in your composer's `extra.drupal-scaffold.allowed-packages` section.

```json
 "name": "my/project",
  ...
  "extra": {
    "drupal-scaffold": {
      "allowed-packages": [
        "johnatas-x/drupal-quality-checker"
      ],
      ...
    }
  }
```

Now, run `composer require` to include the package in your application. Since the package is now allowed, the `core-composer-scaffold` package will copy the configuration files.

See [More about scaffolding](#more-about-scaffolding) for more details.

### Without drupal/core-composer-scaffold

If you're not using the scaffolding plugin, the package won't copy the configuration files as expected.

First, run `composer require` to include the package in your application.

```bash
composer require --dev johnatas-x/drupal-quality-checker
```

If you don't already have a `grumphp.yml` file in your project, GrumPHP would ask you to create one. Answer "No" to the prompt.

Then, copy all the `*.dist` from the library to your project root. The files copied are:

* grumphp.yml.dist
* phpcs.xml.dist
* phpmd.xml.dist

```bash
cp vendor/johnatas-x/drupal-quality-checker/*.dist .
```

## Usage

No additional steps required, but if git hooks aren't fired, run `php ./vendor/bin/grumphp git:init`. For additional commands, look at [grumhp's documentation](https://github.com/phpro/grumphp/blob/master/doc/commands.md).

## Customising

Almost all customising begins with first copying the `grumphp.yml.dist` file to your project. Make sure you have the file.

### Adding tasks

There are various tasks you can add and customise in your grumphp.yml. Read the [online documentation for GrumPHP tasks](https://github.com/phpro/grumphp/blob/master/doc/tasks.md) to see the tasks you can use and configure.

### Forcing commit message format

To configure commit message structure, use the [git_commit_message task](https://github.com/phpro/grumphp/blob/master/doc/tasks/git_commit_message.md). For example, to enforce the commit message contains the Jira issue ID, use the rule as the following snippet. More options are [documented online](https://github.com/phpro/grumphp/blob/master/doc/tasks/git_commit_message.md).

```yaml
# grumphp.yml
grumphp:
  tasks:
    git_commit_message:
      matchers:
        Must contain issue number: /JIRA-\d+/
```

### Disable commit banners

GrumPHP supports banners to celebrate (or scold) on your commit. This is fun but it is possible it gets on your nerves. If you don’t want it, edit the grumphp.yml file and replace the following parameters:

```yaml
# grumphp.yml
grumphp:
    ascii: ~
```

You could even disable specific ones like this:

```yaml
# grumphp.yml
grumphp:
    ascii:
        succeeded: ~
```

### Overwrite phpmd ruleset

Copy the ruleset to the project root first

```bash
cp vendor/johnatas-x/drupal-quality-checker/phpmd.xml.dist phpmd.xml
```

Edit it as per your needs and commit. Remember to modify the grumphp.yml file with the new path.

```yaml
# grumphp.yml
grumphp:
  tasks:
    phpmd:
      ruleset: ['phpmd.xml']
```

### Customise phpcs rules

Copy the ruleset to the project root first

```bash
cp vendor/johnatas-x/drupal-quality-checker/phpcs.xml.dist phpcs.xml
```

Edit it as per your needs and commit. Remember to modify the grumphp.yml file with the new path.

```yaml
# grumphp.yml
grumphp:
  tasks:
    phpcs:
      standard: ['phpcs.xml']
```

### Customise phpstan parameters

Copy the ruleset to the project root first

```bash
cp vendor/johnatas-x/drupal-quality-checker/phpstan.neon.dist phpstan.neon
```

Edit it as per your needs and commit.

## More about scaffolding

As described before, this package uses [`drupal/core-composer-scaffold`](https://github.com/drupal/core-composer-scaffold) plugin to scaffold a few files to the project root. This is not required but there is a good chance you are already using it if you're building a Drupal site.

The scaffolding operation runs with every composer operation and overwrites files. Only the file `grumphp.yml.dist` is not overwritten during subsequent operations. If you are customising any of the other configuration files and don't want the updates to overwrite your changes, you can override the behaviour in your composer.json file. For example, to skip `phpmd.xml.dist` from being overwritten, add this to your `composer.json`:

```json
  "name": "my/project",
  ...
  "extra": {
    "drupal-scaffold": {
      "file-mapping": {
        "[project-root]/phpmd.xml.dist": false
      }
    }
  }
```

For more details, read the ["Excluding Scaffold files"](https://github.com/drupal/core-composer-scaffold#excluding-scaffold-files) section of the [documentation](https://github.com/drupal/core-composer-scaffold/blob/8.8.x/README.md) for the core-composer-scaffold plugin.

## Upgrading from Beta 8

GrumPHP 0.19 introduced [a breaking change](https://github.com/phpro/grumphp/releases/tag/v0.19.0) to the structure of the YAML file. The template in this repository is updated as per the new structure. However, you would need to change the YML files on your projects before you update to Beta 9 or later.

Fortunately, the change is simple and in many cases would only require a one line change. Rename the `parameters` section to `grumphp`. Our default template contains two parameters which still need to remain under `parameters`. They are `git_dir` and `bin_dir`. Look at [the diff of the change](https://github.com/johnatas-x/drupal-quality-checker/commit/e8d9414ce6ea046b0386115764db68e5251d8a58#diff-94c8df1b4af91d80f7417cad14bbe0e5) to understand what needs to be changed in your grumphp.yml file. Also, read more at the [release page for GrumPHP 0.19](https://github.com/phpro/grumphp/releases/tag/v0.19.0).

Lastly, you can [watch this video](https://youtu.be/XoFJfBcZF58) where I upgrade this on a project. Link: https://youtu.be/XoFJfBcZF58
