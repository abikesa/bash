```bash
#!/bin/bash

# Usage: ./script.sh
# The script will ask for necessary inputs.

echo "Enter GitHub username:"
read USERNAME

echo "Enter repository name:"
read REPO

echo "Is the repository private? (yes/no)"
read IS_PRIVATE

if [[ "$IS_PRIVATE" == "yes" ]]; then
    echo "Enter your GitHub personal access token:"
    read TOKEN
    URL="https://${TOKEN}@github.com/${USERNAME}/${REPO}.git"
else
    URL="https://github.com/${USERNAME}/${REPO}.git"
fi

echo "Enter your email for git config:"
read EMAIL

echo "Enter the filename of your SSH key (e.g., id_rsa):"
read SSH

echo "Enter the exact filename to delete (e.g., obsolete_code.py):"
read FILENAME_TO_DELETE

# Clone the repository
git clone $URL
cd $REPO

# Perform actions on the repository
git checkout main
rm -f $FILENAME_TO_DELETE  # Deletes the exact file specified by the user

# Stage the deletions
git add -A 

# Commit the deletions
git commit -m "Removed $FILENAME_TO_DELETE from main directory" 

# Setup SSH (ensure you have configured SSH keys on your GitHub account)
ssh-add -D
chmod 600 "$(eval echo ~/.ssh/$SSH)"
git remote set-url origin "git@github.com:${USERNAME}/${REPO}"
ssh-add "$(eval echo ~/.ssh/$SSH)"

# Set local git configurations
git config --local user.name "$USERNAME"
git config --local user.email "$EMAIL"

# Push the changes
git push -u origin main

```
