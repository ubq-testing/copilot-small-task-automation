# Copilot Small Task Automation

Automatically assigns GitHub Copilot to issues labeled with small time estimates. Perfect for automating quick tasks that don't warrant the overhead of manual assignment.

## How It Works

When an issue is labeled with a `Time:` label, this plugin:

1. Parses the time duration from the label (e.g., `Time: <1 Hour` → 1 hour)
2. Compares it against the configured `upperLimitLabel` threshold
3. If the label's time is **at or below** the threshold, assigns Copilot to the issue

This uses GitHub's GraphQL API with the `issues_copilot_assignment_api_support` feature flag.

## Configuration

```yml
plugins:
  - name: copilot-small-task-automation
    id: copilot-small-task-automation
    uses:
      - plugin: <plugin-url>
        with:
          # Any "Time:" label at or below this threshold triggers Copilot assignment
          upperLimitLabel: "Time: <1 Hour"
```

### Examples

With `upperLimitLabel: "Time: <1 Hour"` (default):

| Label Applied | Duration | Action |
|--------------|----------|--------|
| `Time: <15 Minutes` | 15m | ✅ Assigns Copilot |
| `Time: <1 Hour` | 1h | ✅ Assigns Copilot |
| `Time: <2 Hours` | 2h | ❌ Skipped (exceeds limit) |
| `Time: <4 Hours` | 4h | ❌ Skipped (exceeds limit) |
| `Priority: High` | N/A | ❌ Skipped (not a time label) |

### Supported Label Formats

Labels must follow the format `Time: <duration>` where duration can be:
- `15 Minutes`, `30 Minutes`
- `1 Hour`, `2 Hours`, `4 Hours`
- Any format supported by the [`ms`](https://github.com/vercel/ms) library

## Requirements

- **Copilot coding agent** must be enabled in the repository
- The GitHub App must have the following permissions:
  - Read access to metadata
  - Read and write access to issues and pull requests

## Development

### Running Locally

```bash
bun install
deno task dev:deno | bun dev:bun 
```

The worker runs on http://localhost:4000/

### Testing

```bash
bun test
```
