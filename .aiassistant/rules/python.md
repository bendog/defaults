---
apply: always
---

# Python requirements

## Code style requirements

- Follow PEP 8 guidelines
- Use meaningful variable and function names
- ALWAYS avoid repeated file names to avoid namespace conflicts
- ALWAYS maintain indentation consistency
- ALWAYS write code which is compatible with `ruff format`
- All file interactions should use `pathlib.Path`
- Use f-strings for string formatting
- ALWAYS avoid wildcard imports
- Use list comprehensions where appropriate
- Use `enumerate()` instead of `range(len())` for loops
- Use `with` statements where context managers are appropriate
- Except for tests, always put imports at the start of the file
- use relative imports where possible
- ALWAYS use `raise` `from` for an exception if there is a caught exception before a raise statement

## Type hinting requirements

- ALWAYS use type hints for function and method arguments and keyword arguments
- ALWAYS use type hints for function and method return types
- ALWAYS add type hints for declared variables
- Type annotations should be in 3.13+ style
- Use native types and avoid using the `typing` module (e.g., `list`, `dict`, `tuple`, etc.)
- Use union types with the `|` operator (e.g., `int | None` instead of `Optional[int]`)
- Use `TypedDict` for dictionaries with fixed structure
- Use `Literal` for fixed sets of string or numeric values
- Use `Final` for constants
- Use `Protocol` for structural subtyping
- Use `TypeVar` for generic types
- Use `Callable` for function signatures
- Use `Generator` for functions which yield
- Use `Any` sparingly and only when necessary
- Where a type hint data structure is reused, create a type alias

### Logging requirements

- Avoid using `print()` for logging; use the `logging` module instead
- logger should be initialized with `logger = logging.getLogger(__name__)` after file imports.

### Package Management Requirements

- Use UV for package and project management for all python projects
- All executed commands should be done via `uv run`
- Avoid the use of tools which aren't already in the pyproject.toml dependencies section
- Tools only required for testing should be in [dependency-groups] in the group `dev`
- If a new dependency is reasonable, advise the user before using a dependency not listed in pyproject.toml
- Package build tooling must use `uv-build` as the build engine

### Testing requirements

- Use `pytest` for testing
- Write unit tests for all functions and methods
- Write integration tests for command workflows
- Ensure tests cover edge cases and error handling
- Prefer writing tests as functions
- Avoid using classes in tests unless writing tests for a class which is inherited from
- Where testing classes which inherit, refactor the parent classes tests into a class and inherit from the parent test class
- Use `pytest.fixture` functions for setting up test environments
- Mock external dependencies in tests
- Mock all AWS resources with the `moto` package
- Use the `pytest.MonkeyPatch.context` context manager to apply patches for mocking
- Where ever mocked tooling can be reused use a fixture which will yield within a `pytest.MonkeyPatch.context`
- ALWAYS mock all required envvars with a `pytest.MonkeyPatch.context` context manager inside a `@pytest.fixture(scope="session")` fixture function in `tests/conftest.py`
- For large static mock payloads add them as files in a `resources/` directory and import them as a fixture
- For dynamic mock payloads create helper functions which will return the payload inserting dynamic parts
- If AWS is used ALWAYS ensure a `@pytest.fixture(scope="session")` function in `tests/conftest.py` exists to yield fake credential environment into a `pytest.MonkeyPatch.context` to prevent access to real resources
- Use `importlib.reload` to reload required modules inside a `MonkeyPatch.context`

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
