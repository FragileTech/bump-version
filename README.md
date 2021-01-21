# bump-version

GitHub Action to increment the project's version using [bump2version](https://github.com/c4urself/bump2version), commit, tag, and push the change. This operation can be fine-tuned in `.bumpversion.cfg`, see `bumpversion --help` for the details.

### How to use

You need to obtain a [Personal Access Token](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token)
to push to your repository and save it in your [Secrets](https://docs.github.com/en/actions/reference/encrypted-secrets) as `BOT_TOKEN`.
Suppose that your GitHub account is @your-bot-login "Your Company Bot" \<bot@your-company.com\>.
Suppose that your version lies in file `version.md` like this:

```
# version 1.0.0
```

Create a new workflow (or append to an existing one) `.github/workflows/bump-version.yml`:

```yaml
name: Bump version workflow

on:
  push:
    branches:
    - main

jobs:
  bump-version:
    name: Bump package version
    if: "!contains(github.event.head_commit.message, 'Bump version')"
    runs-on: ubuntu-20.04
    steps:
    - name: actions/checkout
      uses: actions/checkout@v2
    - name: current_version
      run: echo "current_version=$(grep '# version' version.md | cut -d ' ' -f3)" >> $GITHUB_ENV
    - name: FragileTech/bump-version
      uses: FragileTech/bump-version@main
      with:
        current_version: "${{ env.current_version }}"
        files: version.md
        commit_name: Your Company Bot
        commit_email: bot@your-company.com
        login: your-bot-login
        token: "${{ secrets.BOT_TOKEN }}"
```

Now every time you merge something to `main` branch, you'll receive an additional direct commit to `main` that increments the version in `version.md`.

### License

MIT, see [LICENSE](LICENSE).

