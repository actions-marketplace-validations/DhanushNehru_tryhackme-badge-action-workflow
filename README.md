# tryhackme-badge-action-workflow
A github action for tryhackme to fetch and regenerate static badge image which can be used in the Readme

This is a GitHub Action that fetches your latest TryHackMe badge, downloads it, and commits it to your repository.

### Features
- Fetches the latest badge based on your TryHackMe user ID.
- Downloads the badge image to a specified file path.
- Commits the downloaded image to your repository with a custom message.
- Allows setting a custom committer username.

### Usage
- Add this script to your GitHub repository as a .yml file (e.g., update-tryhackme-badge.yml).
- Configure the action with the following inputs:
    - GITHUB_TOKEN: Your GitHub Personal Access Token (required, set as a secret).
    - image_path: The path to store the downloaded badge image (defaults to ./assets/tryhackme-badge.png).
    - username: Your TryHackMe username (defaults to the value in a secret named THM_USERNAME).
    - user_id: Your TryHackMe user ID (defaults to the value in a secret named THM_USER_ID). Eg) 1995656

```
name: Update TryHackMe Badge

# allow the workflow to push changes
permissions:
  contents: write

on:
  schedule:
    - cron: '0 0 * * *' # Runs every day at midnight
  workflow_dispatch: # Allows manual triggering

jobs:
  update-badge:
    runs-on: ubuntu-22.04
    steps:
    - name: Checkout repository
      uses: actions/checkout@v3
      with:
        # allow pushing back to the same repo using the GITHUB_TOKEN
        persist-credentials: true
        fetch-depth: 0

    - name: Configure Git Identity
      run: |
        git config --global user.name "GitHub Actions Bot"
        git config --global user.email "actions@github.com"

    - name: Fetch TryHackMe Badge
      uses: DhanushNehru/tryhackme-badge-action-workflow@v1.0
      # If this action returns non-zero when there's no change, allow the job to continue.
      # Remove continue-on-error if you fix the action to exit 0 when nothing changed.
      continue-on-error: true
      with:
        image_path: './assets/tryhackme-badge.png'
        username: 'your_tryhackme_username'
        user_id: 'your_tryhackme_user_id'
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

    - name: Commit and push badge if changed
      env:
        COMMIT_MESSAGE: "Updated THM profile badge"
      run: |
        git add ./assets/tryhackme-badge.png || true

        # If there are no staged changes, exit successfully
        if git diff --cached --quiet; then
          echo "No changes to commit"
          exit 0
        fi

        git commit -m "$COMMIT_MESSAGE"

        # Push current HEAD back to the branch that triggered the workflow
        git push origin "HEAD:${{ github.ref_name }}"
```
