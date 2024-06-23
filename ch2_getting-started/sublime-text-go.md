# Sublime Text & Go

- LSP
- Goland Build
- GoTests
- SublimeLinter
- SublimeLinter-golangcilint

# LSP
```
{
    "show_diagnostics_count_in_view_status": true,
    "lsp_format_on_save": true,
    "lsp_code_actions_on_save": {
        "source.organizeImports": true
    },
    "clients": {
        "gopls": {
            "enabled": true,
            "settings": {
                "analyses": {
                    "fieldalignment": true,
                    "fillreturns": true,
                    "nilness": true,
                    "nonewvars": true,
                    "shadow": true,
                    "simplifycompositelit": true,
                    "structtag": true,
                    "undeclaredname": true,
                    "unreachable": true,
                    "unusedparams": true,
                    "unusedwrite": true
                },
                "formatting.gofumpt": true,
                "usePlaceholders": true
            }
        }
    }
}
```
# Sublime Linter
```
{
    "linters": {
        "golangci-lint": {
            "lint_mode": "background",
            "executable": "/home/b/go/bin/golangci-lint",
            "args": [
                "-E=revive",
                "-E=ifshort",
                "-E=gocritic",
                "-E=dogsled",
                "-E=gosec",
                "-E=misspell",
                "-E=goconst",
                "-E=sqlclosecheck",
                "-E=rowserrcheck",
                "-E=goconst",
                "-E=noctx"
            ]
        }
    }
}
```