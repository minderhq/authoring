---
description: Scaffold a new Minder product plugin from the public template and SDK, ready to implement and validate.
---

Start a new Minder plugin. Load the [[authoring-guide]] skill first, then:

1. **Choose the shape:** a **code plugin** (Python, most cases) or a **manifest
   plugin** (declarative webhook→store-vector, no code). See authoring-guide.
2. **Scaffold:** from a checkout, `minder-plugin scaffold <name>` (SDK CLI) or copy
   [`plugin-template`](https://github.com/minderhq/plugin-template) via "Use this
   template". Rename the class and fill `register()` metadata (name, semver version,
   description, author).
3. **Implement:** `collect_data()` and/or `ACTIONS` + `AI_TOOLS`; declare
   `CONFIG_SCHEMA` (mark credentials `secret: true`), `REQUIRES`, and `DISPLAY`
   (`logo` = a lucide icon name).
4. **Before writing any fetch/parse/interpolation code**, load [[authoring-security]].
5. When it runs, validate with `/minder-plugin-check`.

$ARGUMENTS
