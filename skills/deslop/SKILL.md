---
name: deslop
description: Remove unnecessary AI-generated code patterns from a diff. Use before committing or when asked to clean up a change.
---

# Deslop

Review the intended diff against the surrounding code. Remove unnecessary abstractions, redundant checks, and inconsistent style introduced by the change. Preserve required validation and intended behavior, and keep cleanup within the diff's scope.

Be aggressive about comments. Keep an explanatory comment only when it conveys something the code cannot express, such as a non-obvious reason or an external constraint. Delete narration, restatements of the code, section dividers, and comments explaining obvious behavior. When a comment compensates for unclear code, improve the names or structure and remove the comment. Keep necessary explanations short and specific. Preserve required legal notices and functional tool directives.

Run the relevant checks if cleanup changes executable code.
