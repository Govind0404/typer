# Automatic Subcommand Discovery for Typer

## Problem Brief
Large Typer CLIs grow fragile as projects scale: manual wiring can miss modules, duplicate commands, and create uneven hierarchies. We need deterministic automatic discovery that walks a package and attaches groups based on the module tree, while coexisting with manually registered commands. The result should be predictable, collision‑safe, and easy to adopt without changing existing layout.

## Agent Instructions
1) Implement a discovery utility that scans a Python package, imports matching modules, and registers any module‑scope Typer application it finds.
2) Build command groups that mirror dotted module paths; support an optional top‑level namespace to place all discovered groups beneath.
3) Ensure idempotency and coexistence: do not duplicate on re‑runs; leave existing manual commands untouched; if a discovered name conflicts with an existing command, raise a discovery error.
4) Accept glob‑style filters evaluated against dotted module paths. A pattern ending with ".*" matches the package itself and its descendants.
5) On import failure, raise a discovery error whose message includes the fully qualified module path that triggered the failure.

## Test Assumptions
- Public API required:
  - Function: `typer.discovery.register_package(app, package, namespace=None, filters=None) -> None`
  - Exception: `typer.discovery.TyperDiscoveryError`
- Discovery targets module‑scope Typer apps; packages exposing such apps become groups; underlying command names/arguments remain Typer‑defined.
- Filters restrict which modules are imported and registered; parents may be imported to attach children. Tests show both an unfiltered failure and a filtered success over the same package.
- Collisions with preexisting commands surface as errors; rerunning discovery is safe (no duplicates) and manual registrations remain intact.
- Import‑failure messages must include the fully qualified (dotted) module path; exact wording is not enforced.