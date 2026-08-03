# Articles

Writing on data engineering, data product development, and how AI is changing both. Each article lives in its own folder, with any supporting diagrams alongside it.

## Index

| Article | Series | Status |
|---|---|---|
| [Spec-Driven Data Product Engineering — Part 1: Why It Matters, and Why Now](./spec-driven-data-product-engineering/part-1-why-it-matters-and-why-now.md) | Spec-Driven Data Product Engineering (1 of 2) | Published |
| Spec-Driven Data Product Engineering — Part 2: Inside a Real Build | Spec-Driven Data Product Engineering (2 of 2) | Coming soon |

## Structure

Each article gets its own folder, named for the article or series in `kebab-case`:

```
article-folder-name/
├── part-1-title.md
├── part-2-title.md          (if part of a series)
└── images/
    └── diagram-name.png
```

- Multi-part series share one folder, with each part as its own file inside it.
- All diagrams and supporting images for an article live in that article's `images/` subfolder, referenced with relative paths so they render correctly on GitHub.
- The index table above is updated as new articles are added.
