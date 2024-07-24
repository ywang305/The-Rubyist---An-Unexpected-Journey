# The Rubyist: An Unexpected Journey
the learning path to become a rubyist

## 1. upgrade ruby version via asdf
```bash
asdf list ruby # check installed ruby version, e.g. current install ruby 3.3.0
asdf plugin update --all # update plugin to incorporate latest version   https://asdf-vm.com/manage/plugins.html#update
asdf list all ruby  # list all available ruby versions from the plugin  https://asdf-vm.com/manage/versions.html#list-all-available-versions
asdf install ruby 3.3.4 #  e.g. target 3.3.4
asdf uninstall ruby 3.3.0  # remove unneeded version

```

## 2. VSCode settings for extension RubyLSP
```json
{
  "search.exclude": {
    "**/node_modules": true,
    "**/public/assets": true
  },
  "editor.scrollBeyondLastLine": false,
  "editor.tabSize": 2,
  "editor.suggestSelection": "first",
  "editor.largeFileOptimizations": false,
  "files.insertFinalNewline": true,
  "files.trimTrailingWhitespace": true,
  "files.trimFinalNewlines": true,
  "extensions.ignoreRecommendations": true,
  "terminal.integrated.scrollback": 2000,
  "security.workspace.trust.untrustedFiles": "open",
  "javascript.updateImportsOnFileMove.enabled": "always",
  "yard.spacers.afterDescription": false,
  "yard.spacers.afterTags": false,
  "yard.spacers.beforeDescription": false,
  "yard.spacers.beforeTags": false,
  "yard.spacers.separateTags": false,
  "editor.formatOnSave": false,
  "[ruby]": {
    "editor.defaultFormatter": "misogi.ruby-rubocop",
    "editor.semanticHighlighting.enabled": true,
    "editor.formatOnSave": false
  },
  "javascript.preferences.autoImportFileExcludePatterns": [
    "**/node_modules"
  ],
  "rdbg.rubyVersionManager": "asdf",
  "ruby.rubocop.executePath": "/Users/ydwang/.asdf/shims/",
  "ruby.rubocop.useBundler": false,
  "ruby.rubocop.configFilePath": "/Users/ydwang/Developer/icn/.rubocop.yml"
}
```

## 3.RubyLSP error after upgrade ruby version
reason, .ruby-lsp still hold older version (e.g. 3.3.0) packages'
- rm -rf .ruby-lsp
- restart vscode, to let extension redump packages to .ruby-lsp
> **Note**
> All done

