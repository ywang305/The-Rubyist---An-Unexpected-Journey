# The-Rubyist---An-Unexpected-Journey
the learning path to become a rubyist

### 1. [Ruby Version Manager](https://rvm.io/)
To install this Ruby Version Manager please issue the following commands on your machine:
```bash
gpg --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
\curl -sSL https://get.rvm.io | bash -s stable
```
First command might produce few different errors. If you receive following error:

> zsh: command not found: gpg

You will need to install gpg(s):
```sh
brew install gnupg gnupg2
```

If you receive following error:

> gpg: keyserver receive failed: No name

You will need to use --keyserver option with one of the newer GPG servers. 

Try one of the following:
```sh
gpg --keyserver hkp://keyserver.ubuntu.com --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB

gpg --keyserver hkp://keys.openpgp.org --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB

gpg --keyserver hkp://pgp.mit.edu --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
```


### 2. VSCode settings
- extension
  - Ruby ( enhance, use with other extensions )
  - Ruby on Rails ( rails snippets )
  - VSCode Ruby ( auto added ? )
  - ruby-rubocop ( linting, formating on save )

[install Rubocop For Code Formatting](https://deanin.com/blog/configure-visual-studio-code-for-ruby-on-rails-6-with-this-quick-guide/)

```sh
rvm @global do gem install rubocop # only for ruby-in-use version
```
 - note this is installed per rvm ruby version, if you use another version of ruby, execute install again 
 - possibly there are anoying warnings, avoid by checking "Ruby › Rubocop: Suppress Rubocop Warnings" in the VSCode settings.

Designate formatter in settings.json
```json
"[ruby]": {
    "editor.defaultFormatter": "misogi.ruby-rubocop"
  }
```
