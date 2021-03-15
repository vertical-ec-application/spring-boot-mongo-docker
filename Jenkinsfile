node ('master')
{
    def mavenHome = tool name: "maven3.6.3"
    def buildnumber = BUILD_NUMBER
    stage ('Checkout code')
    {
        git credentialsId: 'git', url: 'https://github.com/vertical-ec-application/spring-boot-mongo-docker.git'
    }
    stage ('Build application package')
    {
        sh "${mavenHome}/bin/mvn clean package"
    }
    stage ('Docker image')
    {
    sh "docker build -t ashish1412/spring-boot-mongo:${BUILD_NUMBER} ."
    }
    stage('Docker login & push')
    {
       withCredentials([string(credentialsId: 'ashish1412', variable: 'ashish1412')]) 
        {
            sh "docker login -u ashish1412 -p ${ashish1412}"
        }
        sh "docker push ashish1412/spring-boot-mongo:${BUILD_NUMBER}"

    }
    stage ('Remove local image')
    {
        sh "docker rmi -f ashish1412/spring-boot-mongo:${BUILD_NUMBER}"
    }
    stage ('update image version in compose')
    {
        sh "sed -i 's/VERSION/${BUILD_NUMBER}/g' docker-compose-1.yml"
    }
    stage ('Deploy in dev server')
    {
        sshagent(['Docker_Dev_SSH\\'])
        {
          sh 'scp -o StrictHostKeyChecking=no docker-compose-1.yml docker-compose-dev.yml ubuntu@13.235.103.87:'
          sh 'ssh -o StrictHostKeyChecking=no ubuntu@13.235.103.87 docker-compose -f docker-compose-1.yml -f docker-compose-dev.yml up -d'
        }
    }
    stage ('Deploy in QA server')
    {
        sshagent(['Docker_QA_SSH'])
        {
          sh 'scp -o StrictHostKeyChecking=no docker-compose-1.yml docker-compose-qa.yml ubuntu@35.154.228.164:'
          sh 'ssh -o StrictHostKeyChecking=no ubuntu@35.154.228.164 docker-compose -f docker-compose-1.yml -f docker-compose-qa.yml up -d'
        }
    }
}
