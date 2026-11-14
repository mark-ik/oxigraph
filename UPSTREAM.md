# mark-ik/oxigraph provenance and patch ledger

`mark-ik/oxigraph` is the family's fork of [Oxigraph](https://github.com/oxigraph/oxigraph),
kept under the upstream licence with the upstream authors. Mere's engine split
rules it (D155, D159): the 0.5 line with upstream's newer `oxiri`, `rand` and
`quick-xml` and its RDF/XML literal fix, so the family holds one version of each.

## Base

- Branch `mark-ik/0.5`, cut from upstream's 0.5 branch tip `8cdb53e6f`, one commit
  past `v0.5.13` (a sparopt correctness fix).
- Crate versions and signatures stay on the 0.5 line; the re-exported `oxiri`
  types are 0.3 types.
- Retires when the family moves to an upstream release that carries all of this
  (expected: Oxigraph 0.6).

## Carried commits

Oldest first. Each is a `cherry-pick -x` of the upstream commit named, resolved
by hand where it conflicted with the 0.5 line.

| Commit | Upstream | Purpose |
|---|---|---|
| 3556a996 | 8df60614e | rand 0.10 and getrandom 0.4 rows only; that commit's parser changes belong to main's new parser and are not taken. |
| a348ae6f | 7b37e7eb3 | quick-xml 0.39. |
| f1a84355 | 822b7c946 | quick-xml 0.40. |
| de48802b | 348f8e020 | quick-xml 0.42. |
| 9776968c | d14ac0b5c | oxiri 0.3.1, with oxjsonld's `resolve_iri` in its post-79554fa05 form. |
| 8a5240d0 | d4c8140b9 | RDF/XML: stop adding all prefixes to XML literals. |
| ffd2b3c0 | 79554fa05 | The JSON-LD testsuite un-ignores (the code is in 9776968c). |
| 8dad126f | none | Lock update for the moves above. |
| c4575594 | d4c8140b9 | The `testsuite/rdf-tests` submodule half of the RDF/XML fix (to `3d0b0613d`). |
| f33a89c4 | none | Ignores six SPARQL tests the newer rdf-tests adds and the 0.5 parser fails; see below. |

## Known gaps

The rdf-tests move fails six SPARQL tests that upstream fixes only on main's
rewritten (Chumsky) parser, so the fixes do not cherry-pick onto 0.5. They are in
`testsuite/tests/sparql.rs`'s ignore lists, each naming its upstream fix:
`case-insensitive-booleans` (c29be036), `codepoint-esc-01-bad`, `-03-bad`, `-08`
and `-09` (3a80a8b9), and `select-variable-reuse` (c0f77340). Porting them to the
0.5 parser is being sized (D160).

## Moving the fork

1. Take a new 0.5.x tip by rebasing `mark-ik/0.5`; drop what upstream now carries.
2. Run `cargo check --workspace --all-targets`, each library crate's tests with
   `--all-features`, and `cargo test -p oxigraph-testsuite`. A rocksdb build needs
   `LIBCLANG_PATH` pointing at a host clang.
3. Mere's conformance harness is the family's measure: its ledger rows must not move
   backwards.
4. Update this file.
