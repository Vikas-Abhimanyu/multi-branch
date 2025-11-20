pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/Vikas-Abhimanyu/multi-branch.git'
            }
        }

        stage('Test') {
            when {
                anyOf {
                    branch 'main'
                    branch 'dev'
                }
            }
            steps {
                echo "Running tests..."
                sh 'mvn test'
            }
        }

        stage('Build') {
            when {
                branch 'main'
            }
            steps {
                echo "Building..."
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('SonarQube Analysis') {
            when {
                anyOf {
                    branch 'main'
                    branch 'dev'
                }
            }
            steps {
                withSonarQubeEnv('sonar') {
                    sh '''
                        mvn verify sonar:sonar \
                        -Dsonar.projectKey=java-app \
                        -Dsonar.projectName=java-app
                    '''
                }
            }
        }

        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                sh '''
                    if pgrep -f "java -jar java-sample-21-1.0.0.jar" > /dev/null; then
                        pkill -f "java -jar java-sample-21-1.0.0.jar"
                        echo "App stopped"
                    else
                        echo "App was not running"
                    fi
                    
                    JENKINS_NODE_COOKIE=dontKillme nohup java -jar java-sample-21-1.0.0.jar > app.log 2>&1 &
                '''
            }
        }
    }
}
