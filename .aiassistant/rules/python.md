---
apply: always
---

# Python requirements

## Code style requirements

- Follow PEP 8 guidelines
- Use meaningful variable and function names
- Maintain consistent indentation (4 spaces)
- Limit lines to 99 characters where applicable
- All file interactions should use `pathlib.Path`
- Use f-strings for string formatting
- Always avoid wildcard imports
- Use list comprehensions where appropriate
- Use `enumerate()` instead of `range(len())` for loops
- Use `with` statements where context managers are appropriate
- Except for tests, always put imports at the start of the file
- use relative imports where possible
- if there is a caught exception before a raise statement always raise from that exception

## Type hinting requirements

- Type annotations should be in 3.13+ style
- Use type annotations for all functions and methods
- Use native types and avoid using the `typing` module (e.g., `list`, `dict`, `tuple`, etc.)
- Use union types with the `|` operator (e.g., `int | None` instead of `Optional[int]`)
- Use `TypedDict` for dictionaries with fixed structure
- Use `Literal` for fixed sets of string or numeric values
- Use `Final` for constants
- Use `Protocol` for structural subtyping
- Use `TypeVar` for generic types
- Use `Callable` for function signatures
- Use `Any` sparingly and only when absolutely necessary

## Testing requirements

- Use `pytest` for testing
- Write unit tests for all functions and methods
- Write integration tests for command workflows
- Mock external dependencies in tests
- Ensure tests cover edge cases and error handling
- Use fixtures for setting up test environments
- Prefer writing tests as functions
- Avoid using classes in tests as much as possible
- Mock required envvars with `os.environ.setdefault` in `tests/conftest.py`

## Tooling to avoid

- Avoid using `print()` for logging; use the `logging` module instead
- NEVER use the `requests` library; prefer standard libraries or `httpx` for HTTP requests

## Pydantic requirements

- Use Pydantic models for data validation and settings management
- Define Pydantic models with clear field types and default values
- Use Pydantic's built-in validators for common validation tasks
- Leverage Pydantic's `BaseSettings` for configuration management
- Ensure all data inputs are validated using Pydantic models
- Use Pydantic's `Field` for field metadata and constraints
- Use Pydantic's `ConfigDict` to customize model behavior
- Use Pydantic's alias generation for field names when necessary
- Prefer Pydantic's built-in alias generators
- Create a CustomBaseModel from Pydantic's BaseModel to contain common rules as much as possible
- Create custom Pydantic types where reasonable
