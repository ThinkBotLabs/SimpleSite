pipeline 
{
    agent any
    stages 
    {
        stage('Build') 
        {
            steps 
            {
                echo 'Running build automation...'
                echo 'Nothing to build, just showing this for information only..'
            }
        }
        stage('Build Docker Image') 
        {
            when 
            {
                branch 'master'
            }
            steps 
            {
                script 
                {
                    echo 'smoke test to see if it will run'
                    app = docker.build("procstar/simplesite")
                }
            }
        }
        stage('Push Docker Image') 
        {
            when 
            {
                branch 'master'
            }
            steps 
            {
                script 
                {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') 
                    {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('DeployToProduction') 
        {
            when 
            {
                branch 'master'
            }
            steps 
            {
                sh "docker pull procstar/simplesite:${env.BUILD_NUMBER}"
                try 
                {
                    sh "docker stop simple-site"
                    sh "docker rm simple-site"
                } 
                catch (err) 
                {
                    echo: 'caught error: $err'
                }
                sh "docker run --restart always --name simple-site -p 80:80 -d procstar/simplesite:${env.BUILD_NUMBER}"                
            }
        }
    }
}
