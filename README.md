# Arizona Quickstart Comments Recipe

Provides the `bible_comment` comment type for AZ Quickstart Publications, the
comment views and ALTCHA protection, and ECA moderation/notification workflows.

## Requirements

- Arizona Quickstart `^3.6` with the `az_publication` content type and
  `az_content_editor` role
- Composer dependencies are declared in this package

## Install

From the AZ Quickstart site, add this package with Composer and apply it with
Drush:

```sh
composer config repositories.azqs_recipe_comment vcs \
  https://github.com/az-digital/azqs_recipe_comment.git
composer require az-digital/azqs_recipe_comment:dev-main
drush recipe vendor/az-digital/azqs_recipe_comment
drush cr
```

For local development with a checked-out package beside the site:

```sh
composer config repositories.azqs_recipe_comment path ../azqs_recipe_comment
composer require --no-update az-digital/azqs_recipe_comment:@dev
composer update az-digital/azqs_recipe_comment --with-dependencies
drush recipe ../azqs_recipe_comment
```

## What it configures

- The `bible_comment` comment type and `field_bible_comments` on Publications.
- The recent-comments view, RSS feed, ALTCHA challenge, and comment form assets.
- Anonymous posting with approval, authenticated users skipping approval, and
  comment administration permissions for `az_content_editor`.
- Four ECA workflows:

  | Model | Behavior |
  | --- | --- |
  | `azqs_comment_notify_pending` | Emails the site address when a comment awaits approval. |
  | `azqs_comment_notify_commenter_published` | Emails a commenter when their comment is approved. |
  | `azqs_comment_notify_node_author` | Emails the publication author when a comment is published, except when the author wrote it. |
  | `azqs_comment_link_spam_guard` | Holds new published comments containing `http` or `www.` for review. |

ECA models are listed at **Configuration → Workflow → ECA**. They open in the
BPMN.io editor; the first open converts the ECA model to a diagram. Save the
diagram to retain the editable BPMN representation.

Email notifications use Drupal's active mail system. On Pantheon, enabling
Quickstart Mail (`az_mail`) sends them through Amazon SES; this recipe does not
enable it or set environment-specific credentials.

## License

GPL-2.0-or-later
