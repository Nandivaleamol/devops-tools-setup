# Linux Shell Script to run the java spring application

- Script File Name: app-server.sh

```
#!/bin/bash

# Check if the user is running the script as root (sudo)
if [ "$EUID" -ne 0 ]; then
    echo "This script must be run with sudo privileges."
    exit 1
fi

# Function to start the Spring Boot application
start_app() {
    echo "Starting Spring Boot application..."
    nohup java -jar app.jar --spring.profiles.active=test --logging.level.root=OFF --server.port=9191 &
    APP_PID=$!

    if [ $? -eq 0 ]; then
        echo "$APP_PID" > app.pid  # Write the PID to a file
        echo "Spring Boot application started with PID $APP_PID"
    else
        echo "Failed to start Spring Boot application."
        exit 1
    fi
}

# Function to stop the Spring Boot application
stop_app() {
    if [ -f "app.pid" ]; then
        APP_PID=$(cat app.pid)
        echo "Stopping Spring Boot application with PID $APP_PID..."
        kill $APP_PID
        rm -f app.pid  # Remove the PID file
        echo "Spring Boot application stopped."
    else
        echo "Spring Boot application PID file not found. It may not be running."
    fi
}

# Check for the command-line argument
if [ "$1" = "start" ]; then
    start_app
elif [ "$1" = "stop" ]; then
    stop_app
else
    echo "Usage: $0 [start|stop]"
    exit 1
fi
```
