pipeline {
    agent any
 
    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "maven386"
         jdk 'jdk17'
    }
    stages {
		stage('checkout') {
            steps {
                echo '******* Deleting directory *******'
                echo '******* GIT checkout *******'
                git 'https://github.com/data84468/demo-jenkin.git'
                echo "******* Checkout complete*******"
            }
            post {
                success {
                    echo '******* Checkout SUCCESS. *******'
                }
                failure {
                    echo '******* ERROR CODE:  *******'
                    echo '******* Checkout FAILED. Check GIT credentials, git folder structure, git application name for discrepancy *******'
                }
            }
        }
		stage('scan') {
            steps {
                echo '******* GIT checkout *******'
                withSonarQubeEnv(installationName: 'sonarqube'){
				bat "mvn sonar:sonar -Dsonar.sources=src/ "
				}
                echo "******* Checkout complete*******"
            }
            post {
                success {
                    echo '******* Checkout SUCCESS. *******'
                }
                failure {
                    echo '******* ERROR CODE:  *******'
                    echo '******* Checkout FAILED. Check GIT credentials, git folder structure, git application name for discrepancy *******'
                }
            }
        }
		stage('compile') {
            steps {
                echo "******* Running project compilation *******"
                configFileProvider([configFile(fileId: "2f2496bd-d47e-47c4-8312-4d9b10745350", replaceTokens: true, variable: 'SETTINGS')]) {
                         bat "mvn -s $SETTINGS -U clean compile"
                    }

            }
            post {
                success {
                    echo '******* Compilation SUCCESS. Starting Automated Unit Testing *******'
                }
                failure {
                    echo '******* ERROR CODE: COE-JEN-02 *******'
                    echo '******* Compilation FAILED. Check Maven dependecies (POM.XML). Can also have code errors *******'
                }
            }
        }
		stage('package') {
            steps {
                echo "******* Running project packaging *******"
                configFileProvider([configFile(fileId: "2f2496bd-d47e-47c4-8312-4d9b10745350", replaceTokens: true, variable: 'SETTINGS')]) {
                         bat "mvn -s $SETTINGS -U package -DskipTests"
                    }
            }
            post {
                success {
                    echo '*******   Packaging SUCCESS. Sending Notifications *******'
                }
                failure {
                    echo '******* JCODE: JD04 *******'
                    echo '******* Packaging FAILED. Check POM for Deployment Plugin Error in Application *******'
                }
            }
        }
		
        stage('deploy') {
            steps {
                // Get some code from a GitHub repository
                //git 'https://namita-kachhawa-personal@bitbucket.org/nagarropoc/firstrepository.git'
 
                // Run Maven on a Unix agent.
                //bat "mvn -s settings.xml clean deploy"
                configFileProvider([configFile(fileId: "2f2496bd-d47e-47c4-8312-4d9b10745350", replaceTokens: true, variable: 'SETTINGS')]) {
					    bat "mvn -s $SETTINGS -U deploy mule:deploy -DskipTests"
                    }
 
                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }
 
           
        }
    }
}
