# Directory Structure

Keep skill packages as flat as the resource ownership permits. A skill entry point must be able to name each conditional resource directly without navigating a documentation tree.

```text
# BAD: taxonomy adds navigation without adding ownership.
agent-skill/
├── references/
│   └── sections/
│       └── routing/
│           └── narrative-links.md
└── assets/
    ├── fonts/
    │   └── brand.woff2
    └── templates/
        └── comment-template.md

# GOOD: resources stay directly discoverable.
agent-skill/
├── references/
│   └── narrative-links.md
└── assets/
    ├── brand.woff2
    └── comment-template.md
```

Put an output template that the agent fills/copies in `assets/`, and then hyperlink that asset from the caller. A template fenced inline in `SKILL.md` can easily be confused as instructions rather than merely the shape of the output.

Use a deeper directory only when it is a real owned artifact, such as a complete template copied as one unit. Do not reproduce a document outline in the filesystem.

Prefer another small skill over a deep reference hierarchy when a subtree has an independent trigger and reusable purpose.
