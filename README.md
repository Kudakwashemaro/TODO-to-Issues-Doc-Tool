# TODO → GitHub Issues Automation

### Title-Based Anchoring with Rich Metadata

Automated conversion of structured TODO comments into **fully managed GitHub Issues**, using a **title-based anchoring system** that supports cross-file references, prioritization, categorization, and assignment — *without requiring issue numbers in code*.

This project is designed for teams that want disciplined, scalable, and auditable technical debt tracking directly from their codebase.

---

## Why This Exists

Traditional TODO comments are:

* Invisible to project planning
* Hard to track across files
* Easy to forget and never resolve

This tool turns TODOs into **first-class project artifacts**:

* One issue per concern
* Multiple code locations linked automatically
* Rich metadata for planning and prioritization
* Zero manual bookkeeping

---

## Core Concept: Title-Based Anchoring

**TODOs are bundled by a shared `TITLE` string.**

* One **Canonical TODO** creates the GitHub Issue
* Multiple **Reference TODOs** link to the same issue by matching the title
* Metadata drives labels, assignment, and categorization
* You do *not* need to know the issue number beforehand

This enables multi-file, cross-cutting TODOs without fragmentation.

---

## TODO Formats

### Canonical TODO (Creates the Issue)

Use **once per concern**, ideally where the primary work will occur.

```python
# TODO(TITLE: Fix race condition in user update): Happens when two requests run concurrently
```

With metadata:

```python
# TODO(
#   TITLE: Fix race condition in user update,
#   PRIORITY: high,
#   TYPE: bug,
#   ASSIGNEE: johndoe
# ): Concurrent updates cause data corruption
```

**Syntax**

```
TODO(TITLE: <Unique Title>, [METADATA...]) : [Optional Description]
```

---

### Reference TODO (Links to the Issue)

Use anywhere else the same concern applies.

```python
# TODO(REF: Fix race condition in user update): Also check this write path
```

**Rules**

* The `REF` title **must match the canonical TITLE exactly**
* The reference context is added to the issue checklist

---

## Supported Metadata

Metadata is read **only from the Canonical TODO**.

| Tag        | Purpose                  |         |          |               |      |             |          |                |
| ---------- | ------------------------ | ------- | -------- | ------------- | ---- | ----------- | -------- | -------------- |
| `PRIORITY` | `critical                | high    | medium   | low`          |      |             |          |                |
| `TYPE`     | `bug                     | feature | refactor | documentation | test | performance | security | accessibility` |
| `EFFORT`   | `small                   | medium  | large    | xlarge`       |      |             |          |                |
| `EPIC`     | Groups related issues    |         |          |               |      |             |          |                |
| `ASSIGNEE` | Auto-assigns GitHub user |         |          |               |      |             |          |                |

---

## Automatic Labeling

### Priority

* `priority:critical`
* `priority:high`
* `priority:medium` (default)
* `priority:low`

### Type

* `type:bug`
* `type:feature`
* `type:refactor`
* `type:documentation`
* `type:test`
* `type:performance`
* `type:security`
* `type:accessibility`

### Effort

* `effort:small` (< 2h)
* `effort:medium` (2–8h)
* `effort:large` (1–3 days)
* `effort:xlarge` (> 3 days)

### Always Applied

* `todo`
* `tech-debt`

Epic metadata generates labels like:

```
epic:oauth-migration
```

---

## Workflow Behavior

1. **Scan**

   * Runs on push to `main` or manually
   * Scans supported code files only

2. **Match**

   * Groups TODOs by `TITLE`
   * Resolves `REF` entries to their canonical anchor

3. **Create**

   * Creates a GitHub Issue if none exists for the title
   * Issue title:

     ```
     TODO: <Title>
     ```
   * Applies labels and assignee automatically

4. **Update**

   * Aggregates **all TODO locations** into a checklist
   * Updates the issue as new references are added

---

## Example: One Concern, Multiple Files

**Canonical**

```python
# services/user_service.py
# TODO(TITLE: Add Redis caching layer, PRIORITY: high, TYPE: performance, EFFORT: medium)
```

**References**

```python
# services/product_service.py
# TODO(REF: Add Redis caching layer): Product lookups need caching
```

```python
# config/cache_config.py
# TODO(REF: Add Redis caching layer): Configure Redis backend
```

**Result**

* One GitHub Issue
* Three tracked locations
* Single source of truth
* Progress tracked via checkboxes

---

## Epic Grouping Example

```python
# TODO(TITLE: Implement OAuth2 authorization, EPIC: oauth-migration, PRIORITY: critical)
# TODO(TITLE: Add JWT token validation, EPIC: oauth-migration, PRIORITY: high)
# TODO(TITLE: Configure OAuth providers, EPIC: oauth-migration)
```

Creates three issues, all grouped under:

```
epic:oauth-migration
```

---

## Best Practices

### Do

* Keep titles **stable and exact**
* Use one canonical TODO per concern
* Add meaningful context to references
* Use metadata for planning
* Remove TODOs when work is complete
* Close issues once all TODOs are resolved

### Don’t

* Duplicate canonical TODOs with the same title
* Change titles after issue creation
* Use REF without a matching TITLE
* Use special characters in titles
* Omit the colon before descriptions

---

## Supported File Types

Scanned extensions include:

```
.py .js .ts .jsx .tsx .java .c .cpp .h .hpp
.cs .go .rs .rb .php .sh .bash
```

Excluded:

* Documentation files
* Virtual environments
* Dependency directories (`node_modules`, `.venv`, etc.)

---

## Maintenance & Customization

* **Workflow**: `.github/workflows/todo-to-issues.yml`
* Customize:

  * Scanned file extensions
  * Excluded directories
  * Label mappings
  * Issue body template

**Troubleshooting**

* Check GitHub Actions logs
* Ensure `GITHUB_TOKEN` has `issues: write`
* Verify TODO syntax exactly matches supported patterns

---

## Roadmap

Planned enhancements:

* Auto-close issues when all TODOs are removed
* Parent/child issue relationships
* Slack / Discord notifications
* Weekly TODO digest
* Metrics dashboard (age, density, resolution time)
* Due dates (`DUE: YYYY-MM-DD`)
* Deeper GitHub Projects automation

---

## Author

**Kudakwashe Marongedza**
Backend Developer | Django & API Specialist | SaaS Builder

* Portfolio: [https://kudakwashem.is-a.dev](https://kudakwashem.is-a.dev)
* Focus: scalable backends, workflow automation, developer tooling

---

## Contributing & Collaboration

This project is **open to collaboration**.

If you are interested in:

* Improving the parser
* Adding CI enforcement
* Extending metadata support
* Building analytics or dashboards
* Hardening the workflow for large teams

Please:

1. Open an issue with ideas or proposals
2. Submit a pull request
3. Or start a discussion

Use the label:

```
workflow-enhancement
```

Contributions, critiques, and real-world feedback are welcome.


