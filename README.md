# devOpsChallenge
Atlantbh DevOps Challenge

As requested I added commit-msg message check.
I expect next types commit_types="feature (new feature)|fix (bug fix)|refactor (refactoring code)|wip (work in progress, commits to be pushed later)".
Also it is in format type:message.
Message need to have minimum od 10 or maximum of 50 characters.

I get user commit message with msg=$(cat "$1")
Then i split type and message with: first_element=$(echo "$msg" | cut -d':' -f1); second_element=$(echo "$msg" | cut -d':' -f2
I store the count of second message for chars check: second_element_count=${#second_element}
Then i loop through commit_types:
  here with ifs
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

  With "$i" = "$first_element" i check is type same as expected
  With "$first_element" == "$second_element" i check is there message because when user types like "feature (new feature)" first_element and second_element are same

  If everything is okay user gets green message: echo -e "\e[32mYour commit message meets Conventional Commit standards...\e[0m"
  If there are less than 10 or greater than 50 chars user gets red message echo -e "\e[31mFollow rules type:message and set message minimum to 10 chars\e[0m"

  If type is not okay user gets Conventional Commit standard message with explanation how to set right commit message
  

As addition I also added pre-commit hook, where i show user what they want to commit and where and ask them if they want that with Y/y or to discard with N/n.
