podTemplate(label: 'jenkins-slave', 
  containers: [
    containerTemplate(
      name: 'jnlp',
      image: 'jenkinsci/jnlp-slave:3.10-1-alpine',
      args: '${computer.jnlpmac} ${computer.name}'
    ),
    containerTemplate(
      name: 'maven',
      image: 'satishrawat/jenkins:8',
      command: 'cat',
      ttyEnabled: true
    ),
  ],
  volumes: [ 
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'), 
  ]
)
{
  node ('jenkins-slave') {
   container('maven') {

parameters {
        string (defaultValue: "10", description: 'Deployment Version', name: 'version')
        choice choices: ['prod', 'dev', 'stag'], description: 'deploy environment', name: 'environment'
    }
stages {
        stage ('checkout code') {
            steps {
                git branch: 'promote',
                    credentialsId: 'github',
                    url: 'https://github.com/techmartguru/kube-pipeline'
                          
            
        }               
    }
    stage ('Connect Kubernetes Cluster ') {
            steps {
            sh 'gcloud auth activate-service-account --key-file devops-224605-0466f18f3159.json'
            sh 'gcloud container clusters get-credentials sk-cluster --zone us-central1-a --project devops-224605'
            }
                
        }
        stage ('Deploy the App ') {
            steps {
            sh 'sed -i s/hello-docker:10/hello-docker:${version}/g test-app.yaml'
            sh 'kubectl apply -f test-app.yaml -n ${environment}'
            
            }
                
        }
    }
    
    
