# AGENTS.md - Developer Guide for Deep Research From Scratch

This guide provides essential information for agentic coding assistants working in this repository, including build commands, code style guidelines, and development workflows.

## Repository Structure

```
deep_research_from_scratch/
├── notebooks/              # Interactive tutorial notebooks (SOURCE OF TRUTH - MODIFY THESE)
│   ├── 1_scoping.ipynb     # User clarification and brief generation
│   ├── 2_research_agent.ipynb       # Research agent with custom tools
│   ├── 3_research_agent_mcp.ipynb   # Research agent with MCP servers
│   ├── 4_research_supervisor.ipynb  # Multi-agent supervisor coordination
│   ├── 5_full_agent.ipynb  # Complete end-to-end system
│   └── utils.py            # Shared utilities for notebooks
├── src/deep_research_from_scratch/  # Generated source code (DO NOT MODIFY DIRECTLY)
│   ├── multi_agent_supervisor.py
│   ├── prompts.py
│   ├── research_agent.py
│   ├── research_agent_mcp.py
│   ├── state_*.py
│   └── utils.py
└── README.md              # Comprehensive documentation
```

**Note**: No Cursor rules (`.cursor/rules/` or `.cursorrules`) or Copilot rules (`.github/copilot-instructions.md`) found in this repository.

## Development Workflow

**CRITICAL**: The notebooks in `notebooks/` are the source of truth and should be the ONLY files modified.

Source files in `src/deep_research_from_scratch/` are automatically generated from notebooks using `%%writefile` magic commands. After editing notebooks, run the cells to regenerate source code.



## Build/Lint/Test Commands

### Package Management (uv)
```bash
# Install dependencies and create virtual environment
uv sync

# Run commands within the virtual environment
uv run python -c "import deep_research_from_scratch"

# Start Jupyter notebooks
uv run jupyter notebook
```

### Linting and Formatting (Ruff)
```bash
# Check for formatting issues
ruff check src/

# Auto-fix formatting issues where possible
ruff check src/ --fix

# Check specific file
ruff check src/deep_research_from_scratch/research_agent.py

# Common fixes: D212, I001, F401, D415
```

### Type Checking (mypy)
```bash
# Type check the source code (requires dev dependencies)
uv run mypy src/
# Check single file: uv run mypy src/deep_research_from_scratch/research_agent.py
```

### Testing
Currently no formal test suite. Tests are run interactively through notebook execution.

## Code Style Guidelines

### Imports Order
- Standard library imports first (`import operator`, `from typing import ...`)
- Third-party imports second (`from pydantic import ...`, `from langgraph.graph import ...`)
- Local imports last (`from deep_research_from_scratch.state_research import ...`)

### Type Annotations
- Use Python type hints extensively
- Prefer `TypedDict` for state definitions with `Annotated` for reducers
- Use Pydantic `BaseModel` for structured outputs
- Leverage `typing_extensions` for forward compatibility

### Naming Conventions
- **Classes**: `CamelCase` (e.g., `ResearcherState`, `ClarifyWithUser`)
- **Functions/Variables**: `snake_case` (e.g., `llm_call`, `tool_node`)
- **Constants**: `UPPER_SNAKE_CASE` (e.g., `RESEARCH_AGENT_PROMPT`)
- **Private members**: Prefix with underscore (e.g., `_internal_helper`)

### Docstrings
Use Google-style docstrings with proper formatting:
```python
def llm_call(state: ResearcherState):
    """Analyze current state and decide on next actions.

    The model analyzes the current conversation state and decides whether to:
    1. Call search tools to gather more information
    2. Provide a final answer based on gathered information

    Returns updated state with the model's response.
    """
```

### Error Handling
- Use explicit error messages for tool failures
- Validate inputs with Pydantic models
- Let LangGraph handle flow control errors
- Use descriptive variable names to aid debugging

### LangGraph Patterns
- Use `StateGraph` with explicit state schemas
- Define conditional edges with clear routing logic
- Use `add_messages` reducer for conversation history
- Implement subgraphs for complex workflows

### Prompt Engineering
- Store prompts in `prompts.py` as module-level strings
- Use clear XML-style tags for structure (`<Task>`, `<Instructions>`)
- Include date context with `{date}` placeholders
- Provide specific examples for complex formats
- Use markdown formatting for readability

## Quick Reference

```bash
# Regenerate source from notebooks (run notebook cells)
uv run jupyter notebook

# Lint generated code
ruff check src/ --fix

# Type check
uv run mypy src/

# Verify package installation
uv run python -c "import deep_research_from_scratch; print('OK')"
```

### When Making Changes
1. Edit the appropriate notebook in `notebooks/`
2. Run the modified cells to regenerate source code
3. Test changes by running subsequent notebook cells
4. Run linting: `ruff check src/ --fix`
5. The generated code in `src/` will automatically reflect changes

**Never** directly edit files in `src/deep_research_from_scratch/` - they are generated artifacts.