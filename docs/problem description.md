# Automatic Subcommand Discovery for Typer

## Problem Brief
Manual glue in large Typer CLIs can miss modules, double‑register commands, and create uneven group hierarchies. We need deterministic discovery that walks a package, attaches groups mirroring the module tree, and coexists with hand‑authored registrations. Adoption should be predictable, collision‑safe, and require no reorganization of existing code.

## Agent Instructions
1) Scan a Python package, import matching modules, and register any module‑level Typer application discovered.
2) Build command groups that mirror dotted module paths; optionally nest everything under a provided top‑level namespace.
3) Guarantee idempotency and coexistence: no duplicates on re‑runs; do not alter manually registered commands; on name conflicts, raise a discovery error unless a namespace is supplied.
4) Support Unix‑style glob filters evaluated against dotted module paths. A pattern ending with ".*" matches the package itself and all descendants.
5) When an import fails, raise a discovery error whose message includes the fully qualified module path that triggered the failure.

## Test Assumptions
- Public API: `typer.discovery.register_package(...) -> None` and `typer.discovery.TyperDiscoveryError`.
- Discovery targets module‑level Typer apps; packages exposing such apps become groups; command names and argument semantics remain as defined by Typer.
- Filters restrict which modules are imported and registered; parents may be imported to attach children. Tests demonstrate both an unfiltered failure and a filtered success for the same package.
- Collisions surface as errors unless namespaced; rerunning discovery is safe (no duplicates) and manual registrations remain intact.
- Import‑failure messages must include the fully qualified (dotted) module path; exact wording is not enforced.