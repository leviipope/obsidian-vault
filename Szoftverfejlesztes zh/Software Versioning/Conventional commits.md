Számos szoftvereszköz (például a semantic-release és a changelog generátorok) egy commit üzenet konvención alapul.

Létező konvenciók: Angular Commit Message Format, Conventional Commits

**Conventional Commit üzenet felépítés:**
`<típus> ['(' hatáskor ')'] ['!'] ': ' <leírás>`

Típusok: build, chore, ci, docs, feat, fix, style, refactor, perf, test, stb.

**Példák:**
- `build: update dependency versions in pom.xml`
- `build!: bump minimum JDK version to 21`
- `docs: fix broken links in README.md`
- `feat(i18n): add Hungarian language support`