
---
title: "pre-comit"
---

## pre-commit via python

```shell
pip install pre-commit
pre-commit install
touch .pre-commit-config.yaml

pre-commit run --all-files
```

**.pre-commit-config.yaml**

```yaml
default_language_version:
    python: python3
repos:
  - repo: https://github.com/PyCQA/isort
    rev: 5.12.0
    hooks:
      - id: isort
  - repo: https://github.com/psf/black
    rev: 22.12.0
    hooks:
      - id: black
        additional_dependencies: ['click==8.0.4']
  - repo: https://github.com/PyCQA/flake8
    rev: 5.0.4
    hooks:
      - id: flake8
        # language_version: python2.7
        stages: [manual]
```
