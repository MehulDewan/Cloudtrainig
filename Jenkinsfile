pipeline {
    agent {
        node {
            label 'slave2'
        }
    }
    parameters {
    	string(name: 'serverIP', defaultValue: 'None', description: 'Enter target Host IP ')
	string(name: 'targetHost', defaultValue: 'None', description: 'Enter target host for deployment ')
	string(name: 'dockerUser', defaultValue: 'None', description: 'Enter Docker user name ')
	password(name: 'dockerPass', description: 'Enter docker login password ')	    
    }
    stages {
        stage('SCM checkout'){
        	steps {
			git "https://github.com/vistasunil/jenkins-cicd-webinar.git"
            	}
	}
	stage('Remove dockers'){
		steps {
			sh "if [ `sudo docker ps -a -q|wc -l` -gt 0 ]; then sudo docker rm -f \$(sudo docker ps -a -q);fi"
		}
	}
	stage('Build'){
		steps {
			sh "sudo docker build /home/ubuntu/jenkins/workspace/${JOB_NAME} -t ${dockerUser}/devopsdemo"
		}
	}
	stage('Docker Push'){
		steps {
			sh "sudo docker login --username ${dockerUser} --password ${dockerPass}"
			sh "sudo docker push ${dockerUser}/devopsdemo:latest"
		}
	}
	stage('Configure servers with Docker and deploy website') {
		steps {
			sh 'ansible-playbook docker.yaml -e "hostname=${targetHost}"'
		}
	}
	stage('Test the website') {
		steps {
			sh 'curl "http://${serverIP}:81/devopsIQ"'
			sh 'curl "http://${serverIP}:82/devopsIQ"'
			sh 'curl "http://${serverIP}:83/devopsIQ"'
			sh 'curl "http://${serverIP}:84/devopsIQ"'
			sh 'curl "http://${serverIP}:85/devopsIQ"'
		}
	}
    }
}
