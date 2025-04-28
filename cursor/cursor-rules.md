# Cursor IDE Rules: The Complete Guide

<a id="introduction"></a>
## 1. Introduction to Cursor Rules

Cursor Rules are configuration files that define how the AI in Cursor IDE should behave. They provide context-specific guidance for AI features, allowing you to tailor code generation, suggestions, and responses to your project's specific needs.

### Why Use Cursor Rules?

- **Customized AI Behavior**: Tailor AI responses to your project's specific needs
- **Consistency**: Ensure generated code follows your project's standards
- **Context Awareness**: Provide the AI with important project context
- **Reduced Editing**: Get code that requires less manual adjustment
- **Team Alignment**: Ensure consistent AI assistance across team members

### Rule File Locations

- **Project Rules**: `.cursor/rules/*.mdc` files
- **User Rules**: Cursor Settings → Rules

<a id="structure"></a>
## 2. Rule File Structure

### Basic Structure

```markdown
---
description: Brief description of the rule's purpose
globs: pattern/matching/files/*  # File patterns where the rule applies
alwaysApply: false  # Whether the rule should always be applied
---

# Rule Title

## Section 1
- Guideline 1
- Guideline 2

## Section 2
- More instructions...

## Examples
```code
// Example code
```

## References
@template-file.ext
```

### MDC Format

Cursor rules use MDC (Markdown with Context) format, which consists of:

1. **Frontmatter**: Metadata between `---` markers
2. **Content**: Standard Markdown formatted text
3. **References**: File references using `@filename` syntax

<a id="frontmatter"></a>
## 3. Frontmatter Configuration

The frontmatter section contains metadata about when and how the rule should be applied.

### Required Properties

- **description**: A concise explanation of the rule's purpose
  ```
  description: General coding standards
  ```

### Optional Properties

- **globs**: File patterns that trigger the rule
  ```
  globs: src/**/*.ext, src/**/*.otherext
  ```

- **alwaysApply**: Whether to include the rule in all contexts
  ```
  alwaysApply: true
  ```

### Advanced Glob Patterns

```
# Multiple specific file types
globs: src/**/*.{ext1,ext2,ext3}

# Specific component patterns
globs: src/components/**/*{Pattern,OtherPattern}.ext

# Exclusion patterns
globs: !node_modules/**,!dist/**,*.ext

# Multiple specific directories
globs: src/{dir1,dir2,dir3}/**/*.ext
```

### Proper Use of `alwaysApply`

The `alwaysApply` property is frequently misunderstood:

- **alwaysApply: true**: The rule will **always** be included in the AI context, regardless of which files are open
- **alwaysApply: false** (default): The rule will be included **only** when a file matching the `globs` patterns is open

#### When to Use `alwaysApply: true`

Use `alwaysApply: true` for:

- Fundamental rules that should apply across the entire project
- General AI behavior guidelines
- Global coding conventions
- Common error prevention rules

#### When to Use `alwaysApply: false`

Use `alwaysApply: false` for:

- Rules specific to certain file types
- Language or framework-specific patterns
- Conventions for specific components or modules

#### Critical Behavior Note

When `alwaysApply: true` is set, the glob patterns are **completely ignored**. This is a common source of confusion.

| Configuration | Behavior | Note |
|--------------|---------------|------------|
| `alwaysApply: true` + `globs: ...` | Glob patterns are **ignored** | The glob has no effect when alwaysApply is true |
| Only `globs: ...` | Applies only to matching files | Default behavior (alwaysApply: false) |
| Only `alwaysApply: true` | Applies to all contexts | Use sparingly to avoid overloading |
| Neither globs nor alwaysApply | Manual rule, never applied automatically | Must be manually referenced |

<a id="content"></a>
## 4. Rule Content Organization

The content section uses standard Markdown syntax to provide instructions to the AI.

### Title and Headings

```markdown
# Main Rule Title

## Major Section

### Subsection
```

### Lists and Bullet Points

```markdown
- Use this pattern
- Avoid that pattern

1. First step
2. Second step
```

### Code Examples

````markdown
```code
// Preferred approach
function goodExample() {
  // Implementation
}

// Avoid this
function badExample() {
  // Implementation
}
```
````

### Best Practices for Content Organization

1. **Start with Context**: Explain the purpose and scope of the rule
2. **Group Related Guidelines**: Organize instructions into logical sections
3. **Include Both Dos and Don'ts**: Show both good and bad examples
4. **Be Specific**: Use concrete examples rather than vague guidance
5. **Keep it Concise**: Aim for clarity and brevity

### Do and Don't Pattern

A clear pattern for rule content is the "Do and Don't" approach:

```markdown
## Function Structure

✅ DO:
- Keep functions small and focused on a single task
- Use descriptive names that explain what the function does
- Return early for guard clauses

❌ DON'T:
- Create functions with multiple responsibilities
- Use generic names that don't indicate purpose
- Use deeply nested conditionals
```

<a id="references"></a>
## 5. File References and Inclusions

One of Cursor Rules' most powerful features is the ability to reference files from your project.

### Basic File References

Use the `@` symbol followed by a file path to reference a file:

```markdown
@src/components/example.ext
```

When the rule is triggered, the referenced file will be included as additional context for the AI.

### When to Use File References

1. **Template Files**: Reference template files as examples
   ```markdown
   @templates/component-template.ext
   ```

2. **Well-Implemented Examples**: Show existing well-written code
   ```markdown
   @src/components/well-structured-component.ext
   ```

3. **API Contracts**: Include interface or type definitions
   ```markdown
   @src/types/api-types.ext
   ```

4. **Documentation**: Reference relevant documentation
   ```markdown
   @docs/architecture.md
   ```

### File Reference Best Practices

1. **Be Selective**: Only reference essential files (context window is limited)
2. **Use Absolute Paths**: Start paths from the project root for clarity
3. **Reference Small Files**: Prefer smaller files to avoid context overflow
4. **Update References**: Keep referenced files updated as your project evolves

### Checking Applied References

If you suspect file references aren't being applied, try:

```
# In a Cursor chat
"Which files are currently included in your context?"
```

<a id="hierarchy"></a>
## 6. Rule Hierarchy and Organization

Creating a hierarchical structure for your rules helps maintain organization as your rule set grows.

### Directory Structure

```
.cursor/rules/
  ├── 000-base/
  │   ├── formatting.mdc
  │   └── naming.mdc
  ├── 100-technologies/
  │   ├── language1.mdc
  │   └── framework1.mdc
  ├── 200-components/
  │   ├── component-type1.mdc
  │   └── component-type2.mdc
  └── 900-errors/
      └── common-mistakes.mdc
```

### Rule Naming Conventions

Use a consistent naming convention for rule files:

```
{priority}-{category}-{purpose}.mdc
```

Examples:
- `000-base-formatting.mdc`
- `100-language-conventions.mdc`
- `200-component-patterns.mdc`
- `900-error-prevention.mdc`

### Rule Loading Order

Rules are loaded in alphabetical order, so using numeric prefixes helps control rule priority:

1. Base rules (000-*)
2. Technology rules (100-*)
3. Component/feature rules (200-*)
4. Error prevention rules (900-*)

### Cross-Referencing Rules

You can reference other rule files to create hierarchy:

```markdown
---
description: Component standards
globs: src/components/**/*.ext
---

# Component Standards

@.cursor/rules/000-base-formatting.mdc
@.cursor/rules/100-language-conventions.mdc

## Component-Specific Guidelines
...
```

### Rule Override Priority

When rules conflict, generally:

1. More specific rules override more general rules
2. Rules with narrower glob patterns override wider patterns
3. Later-loaded rules override earlier-loaded rules 

<a id="optimization"></a>
## 7. Context Window Optimization

The AI's context window is limited, so optimizing your rules is crucial for effectiveness.

### Context Window Limitations

- Each rule consumes tokens in the AI's context window
- Overloaded context windows lead to rules being ignored
- Referenced files also consume context window space

### Rule Size Guidelines

1. **Keep Rules Concise**: Under 500 lines per rule file (official Cursor documentation recommendation)
2. **Split Large Concepts**: Use multiple focused rules instead of one large rule
3. **Prioritize Important Rules**: Use `alwaysApply: true` only for critical rules

### Optimization Strategies

1. **Rule Decomposition**: Break monolithic rules into smaller, focused rules
   ```
   // Instead of one large rule:
   general-standards.mdc  // 1000+ lines
   
   // Use multiple focused rules:
   formatting-standards.mdc  // 200 lines
   naming-conventions.mdc    // 150 lines
   error-handling.mdc        // 200 lines
   ```

2. **Reference vs. Include**: Reference external files instead of including content
   ```markdown
   // Instead of including code examples in the rule:
   @examples/well-formatted-component.ext
   ```

3. **Rule Targeting**: Use specific glob patterns to apply rules only when needed
   ```
   globs: src/components/**/*.ext
   ```
   instead of
   ```
   globs: *
   ```

4. **Rule Layering**: Build rules in layers, with specialized rules inheriting from base rules

### Signs of Context Window Overload

Watch for these signals that your rules are exceeding context limits:

1. The AI ignores parts of your rules consistently
2. Responses end abruptly
3. The AI mixes patterns from different rules
4. The AI "forgets" about previously mentioned guidelines
5. Rule-specified patterns are only partially implemented

<a id="advanced"></a>
## 8. Advanced Rule Patterns

### Conditional Rules

Create rules that adapt to different situations:

```markdown
---
description: Backend service patterns
globs: src/services/**/*.ext
---

# Backend Service Patterns

## REST API Services
When implementing REST API services:
- Use HTTP verbs semantically
- Implement pagination for collections

## GraphQL Services
When implementing GraphQL services:
- Use batch loading for related data
- Implement field-level permissions
```

### Template-Based Rules

Create rules with reusable patterns:

```markdown
---
description: Component template rules
globs: src/components/**/*.ext
---

# Component Patterns

## Standard Component Template
```code
// Standard component structure
import Framework from 'framework';

// Interface/type definitions

// Component definition

// Export statement
```

@component-templates/basic-component.ext
```

### Layered Rules

Build complex rule systems through layering:

```markdown
---
description: Base coding standards
globs: *
alwaysApply: true
---

# Base Coding Standards
...
```

```markdown
---
description: Language-specific standards
globs: *.ext
---

# Language Standards

@.cursor/rules/base-standards.mdc

## Language-Specific Standards
...
```

### Safety Guard Rules

Create rules specifically to prevent common errors:

```markdown
---
description: Security guard rules
globs: *
alwaysApply: true
---

# Security Guards

## Never Do These
- Never store credentials in code
- Never use outdated cryptographic methods
- Never disable input validation
- Never use hardcoded IPs or URLs

## Always Do These
- Always validate user input
- Always use parameterized queries
- Always log security events
- Always use HTTPS for external requests
```

<a id="troubleshooting"></a>
## 9. Testing and Troubleshooting

### Verifying Rule Application

1. **Ask the AI Directly**: 
   ```
   "What rules are currently applied?"
   "Are you following the rule about [specific guideline]?"
   "Which rule files are in your current context?"
   ```

2. **Check Rule Indicators**: Look for rule-specific patterns in AI responses

3. **Test with New Files**: Create test files matching your glob patterns

### Common Issues and Solutions

| Issue | Possible Cause | Solution |
|-------|---------------|----------|
| Rule not applied | Glob pattern doesn't match | Check file path and glob syntax |
| Rule partially ignored | Context window overload | Split into smaller rules |
| Rule conflicts | Contradictory guidance | Establish clear hierarchy |
| All rules ignored | Context corruption | Start a new chat session |
| globs not working | Using `alwaysApply: true` | Remove `alwaysApply` if you want glob matching |
| Rules seem outdated | Caching issues | Restart Cursor or clear rule cache |

### Debugging Workflow

1. **Isolate the Issue**: Test with a single rule to identify problems
2. **Check Syntax**: Verify frontmatter format and Markdown syntax
3. **Verify Paths**: Ensure file references use correct paths
4. **Start Fresh**: Begin a new chat session to clear context
5. **Rule Simplification**: Temporarily simplify the rule to isolate issues

### Debugging Commands

Specific prompts to help debug rule issues:

```
# Detecting applied rules
"What rule files are currently active in your context?"

# Detecting rule conflicts
"You seem to be ignoring guideline X. Is there a conflicting rule?"

# Checking context window
"Do you have enough context window space to process all rules?"

# Testing rule application
"For this file type, which rules should apply?"
```

<a id="collaboration"></a>
## 10. Team Collaboration with Rules

### Sharing Rules Across Teams

1. **Version Control**: Include `.cursor/rules/` in your repository
2. **Documentation**: Add a README.md explaining your rule structure
3. **Rule Reviews**: Include rule updates in code reviews
4. **Standardization**: Establish team conventions for rule creation

### Team Rule Management

1. **Rule Ownership**: Assign owners to specific rule categories
2. **Rule Versioning**: Track rule changes with version numbers
3. **Rule Testing**: Test rules before sharing with the team
4. **Feedback Loop**: Regularly update rules based on team input

### Measuring Rule Effectiveness

Track these metrics to evaluate the impact of your rules:

1. **Code Quality**: Reduction in code review comments
2. **Consistency**: Code similarity across team members
3. **Productivity**: Reduced time for common tasks
4. **Onboarding**: Faster productivity for new team members

### Rules as Living Documentation

Rules can serve as executable documentation of your team's standards:

```markdown
---
description: Team process standards
globs: *
alwaysApply: true
---

# Team Process Standards

## Commit Guidelines
- Use conventional commit format
- Reference issue numbers in commits

## Pull Request Process
- PRs must have passing tests
- PRs require one technical review
- Large PRs should be broken down
```

<a id="examples"></a>
## 11. Practical Examples

### Universal Formatting Rule

```markdown
---
description: Universal code formatting standards
globs: *
alwaysApply: true
---

# Code Formatting Standards

## General Formatting

✅ DO:
- Use consistent indentation (2 or 4 spaces)
- Limit line length to 80-100 characters
- Use descriptive variable and function names
- Add spacing around operators
- Include meaningful comments for complex logic

❌ DON'T:
- Mix tabs and spaces
- Leave trailing whitespace
- Use single-letter variable names (except for loops)
- Write overly complex one-liners
- Add unnecessary comments for obvious operations
```

### Architecture Pattern Rule

```markdown
---
description: Architecture patterns
globs: src/**/*.ext
---

# Architecture Patterns

## Separation of Concerns

✅ DO:
- Separate business logic from presentation
- Create modular, reusable components
- Use appropriate design patterns for your problems
- Document architectural decisions

❌ DON'T:
- Mix data access, business logic, and UI in one component
- Create tightly coupled modules
- Reinvent patterns without good reason
- Leave architecture decisions undocumented

## Best Practices

- Follow the principle of least privilege
- Design for extensibility
- Create clear boundaries between layers
- Use dependency injection where appropriate
```

### Error Prevention Rule

```markdown
---
description: Common error prevention
globs: *
alwaysApply: true
---

# Error Prevention

## Data Handling

✅ DO:
- Validate all inputs
- Handle edge cases explicitly
- Check for null/undefined values
- Use proper error handling

❌ DON'T:
- Trust external data sources
- Ignore error conditions
- Use implicit type conversions
- Leave error handling for later

## Security Practices

✅ DO:
- Use parameterized queries
- Implement proper authentication
- Follow secure coding standards
- Use secure dependencies

❌ DON'T:
- Store sensitive data in client-side code
- Use outdated security methods
- Ignore security warnings
- Implement custom cryptography
```

<a id="resources"></a>
## 12. Resources and Tools

### Official Resources
- [Cursor Documentation](https://docs.cursor.com/context/rules)
- [Cursor Community Forum](https://forum.cursor.com)

### Community Resources
- [Cursor Directory](https://cursor.directory) - Collection of example rules
- [Awesome CursorRules](https://github.com/PatrickJS/awesome-cursorrules) - Curated list of cursor rules files
- [dotcursorrules.com](https://dotcursorrules.com) - Tips and examples for Cursor rules

### Tools and Extensions
- VSCode Cursor Rules Extension - For managing rules
- Rule Generator - Generate rules from existing code

### Further Reading
- [Mastering Cursor Rules](https://dotcursorrules.com/blog/best-cursor-rules-mastering-cursorrules-for-cursor-ide) - In-depth guide
- [Top Cursor Rules for Coding Agents](https://www.prompthub.us/blog/top-cursor-rules-for-coding-agents) - Patterns analysis
- [Geoffrey Huntley's Stdlib Approach](https://ghuntley.com/stdlib/) - Advanced rule composition