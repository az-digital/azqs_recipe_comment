# Arizona Quickstart Comments Recipe

Provides the `az_comment` comment type, the comment views and ALTCHA
protection, and ECA moderation/notification workflows. When the AZ Quickstart
Publication content type is enabled, comments are attached to it automatically.

## Requirements

- Drupal 11.3 or higher and Arizona Quickstart `^3.6` with the
  `az_content_editor` role
- The `az_publication` module is optional (see [Publications](#publications))
- Composer and Drush
- Quickstart provides ALTCHA, CAPTCHA, and Asset Injector; Composer installs
  the recipe's additional ECA and BPMN.io dependencies.

## Install

From the AZ Quickstart site, add this package with Composer and apply it with
Drush:

```sh
composer config repositories.azqs_recipe_comment vcs \
  https://github.com/az-digital/azqs_recipe_comment.git
composer require az-digital/azqs_recipe_comment:dev-main
drush recipe recipes/azqs_recipe_comment
drush cr
```

For local development with a checked-out package beside the site:

```sh
composer config repositories.azqs_recipe_comment path ../azqs_recipe_comment
composer require --no-update az-digital/azqs_recipe_comment:@dev
composer update az-digital/azqs_recipe_comment --with-dependencies
drush recipe ../azqs_recipe_comment
```

The installed recipe path above assumes Composer's default Drupal recipe
installer path. Use the site's configured installer path if it differs.

## Local development with Lando

The included `.lando.yml` builds an AZ Quickstart `3.6.x` site, enables
`az_demo` (which provides Publications and demo content), and then applies this
recipe so comments are attached to Publications:

```sh
lando start
lando install
lando drush status
```

## What it configures

- The `az_comment` comment type and the `field_az_comments` field storage.
  The field is added to Publications only if `az_publication` is enabled.
- The recent-comments view, RSS feed, ALTCHA challenge, and ECA workflow that
  hides the comment Homepage field.
- Anonymous posting with approval, authenticated users skipping approval, and
  comment administration permissions for `az_content_editor`.
- Permission for anonymous and authenticated users to use Restricted HTML,
  the comment body format. This permission also applies to other fields that
  allow that format.
- Five ECA workflows:

  | Model | Behavior |
  | --- | --- |
  | `azqs_comment_hide_homepage` | Hides the Homepage field on AZ comment forms. |
  | `azqs_comment_notify_pending` | Emails the site address when a comment awaits approval. |
  | `azqs_comment_notify_commenter_published` | Emails a commenter when their comment is approved. |
  | `azqs_comment_notify_node_author` | Emails the content author when a comment is published, except when the author wrote it. |
  | `azqs_comment_link_spam_guard` | Holds new published comments containing `http` or `www.` for review. |

ECA models are listed at **Configuration → Workflow → ECA**. They open in the
BPMN.io editor; the first open converts the ECA model to a diagram. Save the
diagram to retain the editable BPMN representation.

Email notifications use Drupal's active mail system. On Pantheon, enabling
Quickstart Mail (`az_mail`) sends them through Amazon SES; this recipe does not
enable it or set environment-specific credentials.

## Publications

The recipe does not install `az_publication`. Config actions prefixed with `?`
(Drupal 11.3+) add `field_az_comments` to the Publication form and display only
when that content type exists. If you enable `az_publication` later, apply the
recipe again to attach comments. To add comments to another content type, go to
**Manage fields** and reuse the existing `field_az_comments` field.

Until at least one content type has the comments field, the recent-comments
block and `/comments/feed` return errors, because Views only provides the
comment-to-content relationship they use when a node comment field exists.

The node author notification works on any content type that has the comments
field.

## Testing

On a development Quickstart site, apply the recipe and rebuild caches as above.
Place the recent-comments block, create a publication, and verify that anonymous
users see the ALTCHA challenge and can submit a comment for approval. Approve
it and check the notifications, block, and `/comments/feed`. Verify that a new
comment containing a link from an authenticated non-administrator is held for
review.

## Existing installations

The `az_comment` identifiers replace the earlier `bible_comment` identifiers.
Reapplying this recipe does not migrate existing comments or remove the old
configuration; migrate existing data and configuration before switching an
existing site.

## Maintainers

Arizona Digital

## License

GPL-2.0-or-later
