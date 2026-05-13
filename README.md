# glu-action

GitHub Action that downloads a [glu](https://github.com/boxesandglue/glu) release and
renders Markdown, HTML, or Lua to PDF.

## Usage

```yaml
- uses: actions/checkout@v6
- uses: boxesandglue/glu-action@v1
  with:
    input: docs/manual.md
    output: dist/manual.pdf
- uses: actions/upload-artifact@v7
  with:
    name: manual
    path: dist/manual.pdf
```

## Inputs

| Name      | Required | Default  | Description |
|-----------|----------|----------|-------------|
| `input`   | yes      | —        | Input file: `.md`, `.html`, or `.lua`. |
| `output`  | no       | `<input>.pdf` next to input | Output PDF path. Parent directory is created if missing. |
| `css`     | no       | —        | Additional CSS file (passed to glu as `--css`). |
| `version` | no       | `latest` | glu release tag, e.g. `v0.2.0`. `latest` resolves the most recent release at runtime. |
| `args`    | no       | —        | Extra arguments passed verbatim to glu — escape hatch for flags not modelled above. Word-split on whitespace (no shell quoting). |

PDF/UA, document title, language and other metadata are set in the Markdown
frontmatter or via Lua, **not** through this action. See the
[glu README](https://github.com/boxesandglue/glu#readme).

## Outputs

| Name          | Description                                   |
|---------------|-----------------------------------------------|
| `output_path` | Resolved path of the produced PDF.            |

## Examples

### Pin a glu version

```yaml
- uses: boxesandglue/glu-action@v1
  with:
    input: docs/manual.md
    version: v0.2.0
```

### Pass extra flags

```yaml
- uses: boxesandglue/glu-action@v1
  with:
    input: docs/manual.md
    output: dist/manual.pdf
    args: --max-passes 5 --source-date-epoch 0
```

### Use the output in a later step

```yaml
- id: build
  uses: boxesandglue/glu-action@v1
  with:
    input: docs/manual.md

- run: ls -l "${{ steps.build.outputs.output_path }}"
```

## Platforms

Linux (amd64, arm64) and macOS (amd64, arm64). Windows is not supported by the action
yet — glu's Windows release builds, but pdftoppm and other test dependencies make CI
coverage uneven. Open an issue if you need it.

## How it works

The action downloads the matching `glu-<os>-<arch>.tar.gz` release asset from
`boxesandglue/glu`, extracts it into `$RUNNER_TEMP/glu-bin`, prepends that directory
to `$GITHUB_PATH`, and invokes `glu` with the resolved arguments. No system-level
installation, no sudo.

## License

MIT — see [LICENSE](LICENSE).
