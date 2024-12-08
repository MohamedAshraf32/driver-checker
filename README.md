# driver-checker
#!/bin/bash

# Log file location
LOG_FILE="/var/log/driver_checker.log"

# Ensure the log file exists
touch $LOG_FILE

# Function to check eligibility
check_eligibility() {
    if [[ $2 -ge 18 && $3 -ge 4 ]]; then
        echo "eligible"
    else
        echo "not eligible"
    fi
}

# Handle "new" command
if [[ $1 == "new" ]]; then
    read -p "Enter your name: " name
    read -p "Enter your age: " age
    read -p "Enter your vision rate (1-6): " vision_rate

    if [[ ! $age =~ ^[0-9]+$ || ! $vision_rate =~ ^[1-6]$ ]]; then
        echo "Invalid input. Try again."
        exit 1
    fi

    result=$(check_eligibility "$name" "$age" "$vision_rate")
    echo "$name:$age:$vision_rate:$result" >> $LOG_FILE
    echo "You are $result for a driver's license."

# Handle "get" command
elif [[ $1 == "get" ]]; then
    read -p "Enter the name of the user: " name
    result=$(grep "^$name:" $LOG_FILE | tail -n 1 | awk -F: '{print $NF}')
    if [[ -z $result ]]; then
        echo "No result found for $name."
    else
        echo "$name:$result"
    fi

# Handle "list" command
elif [[ $1 == "list" ]]; then
    if [[ ! -s $LOG_FILE ]]; then
        echo "No users found."
    else
        awk -F: '{print $1 ":" $NF}' $LOG_FILE
    fi

# Handle invalid arguments
else
    echo "Usage: driver-checker [new|get|list]"
    exit 1
fi
