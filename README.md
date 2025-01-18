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
    - user_id: Your TryHackMe user ID (defaults to the value in a secret named THM_USER_ID). Eg) 1995656

```
name: Update TryHackMe Badge

on:
  schedule:
    - cron: '0 0 * * *' # Runs every day at midnight
  workflow_dispatch: # Allows manual triggering

jobs:
  update-badge:
    runs-on: ubuntu-22.04

    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    - name: Configure Git Identity
      run: |
        git config --global user.name "GitHub Actions Bot"
        git config --global user.email "actions@github.com"

    - name: Fetch TryHackMe Badge
      uses: DhanushNehru/tryhackme-badge-action-workflow@v1.1
      with:
        image_path: './assets/tryhackme-badge.png'
        user_id: 'your_tryhackme_user_id'
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```
