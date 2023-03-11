
---
title: "Git Hook"
---

## pre-commit

```shell
# https://pre-commit.com/#usage
pip install pre-commit pre-commit-hooks
# write to .git/hooks/pre-commit
pre-commit install
```

```yaml
exclude: >
  (?x)(
      ^a/|
      ^b/c/|
      ^d/e/__init__.py
  )
default_language_version:
    python: python3
repos:
  - repo: https://github.com/PyCQA/isort
    rev: 5.9.3 # tag
    hooks:
      - id: isort # sort imports
  - repo: https://github.com/psf/black
    rev: stable
    hooks:
      - id: black # code formatter
  - repo: https://github.com/pycqa/flake8
    rev: 4.0.1
    hooks:
      - id: flake8 # check the style and quality
        language_version: python2.7
        stages: [manual]
```

## pre-push
