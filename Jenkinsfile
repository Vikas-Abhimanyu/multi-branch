pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/Vikas-Abhimanyu/multi-branch.git'
            }
        }

		stage('Test') {
            when { anyOf { branch 'main'; branch 'dev' } }
            steps {
				cd javaapp-pipeline
                echo "Running tests..."
                sh 'mvn test'
            }
        }
		
        stage('Build') {
            when { branch 'main' }    // Only run on main
            steps {
                cd javaapp-pipeline
				echo "Building application..."
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('SonarQube-Analysis') {
			steps {
			withSonarQubeEnv('sonar') {
				sh '''
				cd javaapp-pipeline
				mvn verify sonar:sonar \
				-Dsonar.projectKey=java-app \
				-Dsonar.projectName=java-app 
				'''
			}
		}
	}

        stage('Deploy') {
            when { branch 'main' }
            steps { sh '''
				cd javaapp-pipeline/target
        	if pgrep -f "java -jar java-sample-21-1.0.0.jar" > /dev/null; then
            	pkill -f "java -jar java-sample-21-1.0.0.jar"
            	echo "App was running and has been killed"
			else
				echo "App is not running"
			fi
            	JENKINS_NODE_COOKIE=dontKillme nohup java -jar java-sample-21-1.0.0.jar > app.log 2>&1 &
      	'''
			}
        }
    }
