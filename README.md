# DevOps Challenge
### Atlantbh DevOps Challenge

As part of this DevOps challenge, I've implemented a commit-msg Git hook to enforce the following commit message standards:

- Commit messages must follow the format: <type>:<message>.
- The <type> must match one of the predefined types: feature (new feature), fix (bug fix), refactor (refactoring code), or wip (work in progress, commits to be pushed later).
- The <message> should have a minimum of 10 characters and a maximum of 50 characters.
  
Here's how the commit-msg hook works:

```
#!/usr/bin/env bash

commit_types="feature (new feature)|fix (bug fix)|refactor (refactoring code)|wip (work in progress, commits to be pushed later)"
msg=$(cat "$1")

first_element=$(echo "$msg" | cut -d':' -f1)
second_element=$(echo "$msg" | cut -d':' -f2)

count=${#msg}
#i have set limit of 50 chars max and 10 chars min on second element
second_element_count=${#second_element}

IFS='|' read -ra ADDR <<< "$commit_types"

for i in "${ADDR[@]}"; do
    if [ $second_element_count -lt 50 -a "$i" = "$first_element" ]; then
        #check if there is user message after type:
        if [[ $second_element_count -lt 10 || "$first_element" == "$second_element" ]]; then
            echo -e "\e[31mFollow rules type:message and set message minimum to 10 chars\e[0m"
            exit 1
        else
            echo -e "\e[32mYour commit message meets Conventional Commit standards...\e[0m"
            echo -e "\e[35mYour commit: $msg\e[0m"
            exit 0
        fi
    fi
done

echo -e "\e[31mYour commit message does not meet the Conventional Commit standard\e[0m"
echo "An example of a valid message is: "
echo "  feature (new feature):add the 'remember me' button"
echo "More details at: https://www.atlantbh.com/git-hooks/"
echo "***********************************************************************"                                                                                                                                                        
echo "Here are the  list of message type : ${commit_types}"                                                                                                                                                                           
echo "  <type>:<description>"
echo "  and to have a maximum of 50 characters. "
echo "***********************************************************************"

exit 1
```

1. It extracts the user's commit message using msg=$(cat "$1").

1. The hook then splits the message into the <type> and <message> using the colon separator. These are stored in first_element and second_element.

1. The length of the <message> is calculated and stored in second_element_count for character count validation.

1. The hook checks if the <type> in the commit message matches one of the expected types and if the character count of the <message> falls within the defined limits.

1. If the commit message meets the standards, the user receives a green success message. Otherwise, they receive a red error message with guidance.

1. If the <type> and <message> are the same, it suggests that the user may have missed adding a message to their commit.

1. In case of a mismatched <type> or invalid character count, a detailed error message is displayed, explaining the Conventional Commit standard, with examples and a link for further reference.

I've also added an additional `pre-commit` hook to provide a preview of the changes the user intends to commit. The hook asks for user confirmation with 'Y/y' to proceed or 'N/n' to discard the commit.

For more details, including examples and guidelines, please visit [Atlantbh's Git Hooks Documentation.](https://www.atlantbh.com/git-hooks/)

Feel free to adjust the formatting or provide additional details as needed for your GitHub README.

### Pre-commit hook code:
```
#!/bin/sh

echo "You are about to commit" $(git diff --cached --name-only --diff-filter=ACM)
echo "to" $(git branch --show-current)

while : ; do
    read -p "Do you really want to do this? [y/n] " RESPONSE < /dev/tty
    case "${RESPONSE}" in
        [Yy]* ) exit 0; break;;
        [Nn]* ) exit 1;;
    esac
done
```
