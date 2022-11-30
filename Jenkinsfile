pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'development') {
                    sh 'docker build -t grc.io/lbg-python-cohort-8/dylan-duo:latest -t grc.io/lbg-python-cohort-8/dylan-duo:$BUILD_NUMBER .'
                    } else {
                        sh "echo 'Build not required!'"
                    }
                }
            }
        }
        stage('Push') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'development') {
                        sh '''
                        docker push grc.io/lbg-python-cohort-8/dylan-duo:latest
                        docker push grc.io/lbg-python-cohort-8/dylan-duo:$BUILD_NUMBER
                        '''
                    } else {
                        sh "echo 'Push not required!'"
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'development') {
                        sh'''
                        kubectl apply -f . --namespace=development
                        kubectl rollout restart deployment backend --namespace=development
                        '''
                    } else if (env.GIT_BRANCH == 'main') {
                        sh'''
                        kubectl apply -f . --namespace=production
                        kubectl rollout restart deployment backend --namespace=production
                        '''
                    } else {
                        sh'''
                        echo "unrecognised branch"
                        '''
                    }
                }
            }
        }
        stage('Clean Up') { 
            steps {
                sh '''
                docker system prune -a --force
                '''
            }
        }
    }
}

