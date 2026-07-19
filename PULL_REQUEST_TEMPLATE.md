<!-- Thank you for contributing to GENIUS GROUPS! Please fill out this template completely. -->
<!-- PRs that don't follow this template will be closed. -->

## Description

### What
<!-- Brief description of the change in 1-2 sentences. -->

### Why
<!-- Context and motivation. What problem does this solve? Link any relevant issues. -->

Closes #<!-- issue number -->

### How
<!-- Technical approach. What did you change and why did you choose this approach? -->

---

## Type of Change

<!-- Check all that apply -->

- [ ] 🐛 Bug fix (non-breaking change which fixes an issue)
- [ ] ✨ New feature (non-breaking change which adds functionality)
- [ ] 💥 Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] 📖 Documentation update
- [ ] 🎨 UI/UX improvement
- [ ] ♻️ Refactoring (no functional changes)
- [ ] ⚡ Performance improvement
- [ ] 🔒 Security improvement
- [ ] 🧪 Test addition or improvement
- [ ] 🔧 CI/CD or build configuration
- [ ] 🗑️ Chore / cleanup

---

## Testing

<!-- Describe how you tested your changes. -->

- [ ] Unit tests added or updated
- [ ] Functional/integration tests added or updated
- [ ] API tests added or updated (if applicable)
- [ ] E2E tests added or updated (if applicable)
- [ ] Manual testing performed (describe below)

### Manual Testing Notes
<!-- What did you test manually? What scenarios did you cover? -->

---

## Checklist

<!-- All items must be checked before requesting review. -->

### Code Quality
- [ ] Code follows the project's coding conventions (linter passes)
- [ ] Static analysis passes (zero critical issues)
- [ ] No dead code introduced
- [ ] No `console.log`, `dd()`, `dump()`, or debug statements left
- [ ] Cyclomatic complexity < 10 per function
- [ ] Functions < 50 lines, files < 500 lines

### Architecture
- [ ] Changes align with Clean Architecture / DDD principles
- [ ] No domain layer dependencies on infrastructure or frameworks
- [ ] Bounded contexts respected (no cross-context coupling)
- [ ] If architectural decision was made, an ADR is included or updated

### Documentation
- [ ] Documentation updated (README, API docs, guides, etc.)
- [ ] OpenAPI spec updated (if API changes)
- [ ] CHANGELOG.md updated (if applicable)
- [ ] Code comments added for complex logic

### Security
- [ ] No secrets, API keys, or credentials committed
- [ ] Input validation implemented for all user-facing inputs
- [ ] Output encoding / sanitization applied where needed
- [ ] OWASP Top 10 considerations reviewed
- [ ] No new dependencies without security review

### Tests
- [ ] All existing tests pass
- [ ] New tests pass
- [ ] Test coverage maintained or improved (≥ 70% business logic)
- [ ] Critical paths have 100% coverage

### Git Hygiene
- [ ] Commits follow Conventional Commits (`type(scope): message`)
- [ ] Branch follows naming convention (`feature/GI-NNN-description`)
- [ ] PR is rebased on latest target branch
- [ ] No merge commits (rebase, don't merge)

---

## Screenshots / Recordings

<!-- If this PR includes UI changes, attach before/after screenshots or screen recordings. -->

| Before | After |
|---|---|
| | |

---

## Related

<!-- Link any related issues, PRs, ADRs, discussions, or design docs. -->

- Issue: #
- ADR: [ADR-XXX](docs/adr/ADR-XXX.md)
- Discussion: #
- Design doc: [link]

---

## Reviewer Notes

<!-- Anything specific you want reviewers to focus on? Known limitations? Open questions? -->

---

> By submitting this pull request, I confirm that:
> - I have read and agree to the [Code of Conduct](./CODE_OF_CONDUCT.md)
> - I have read and followed the [Contributing Guide](./CONTRIBUTING.md)
> - This contribution is my own original work, or I have permission to submit it
> - I grant GENIUS GROUPS SAS the right to distribute this contribution under the project's license
