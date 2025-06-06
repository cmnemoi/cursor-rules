---
trigger: model_decision
description: APPLY Python 3.13 best practices WHEN writing Python code to ensure type safety, readability, and maintainability.
globs: *.py
---

Type Annotations:
- Type every function arguments with type hints
- Avoid using Any
- Use `Type | None` for nullable types
- Use `dict` and `list` for type hints, instead of `typing.List` or `typing.Dict`

Error Handling:
- Use specific exceptions for error handling
- Avoid catching broad exceptions like Exception

Functions:
- Prefix non-public functions and methods with an underscore
- Put non-public functions at the end of the file

Code Structure:
- Use dataclasses for simple data structures
- Prefer NamedTuple for immutable data structures

Enumerations:
- Use Enum for enumerations
- Define enum values explicitly

Linting and Formatting:
- Follow PEP 8 for code style
- Use Ruff with `uv` for formatting and linting

Generics:
- Use TypeVar for generic types
- Use descriptive type parameter names