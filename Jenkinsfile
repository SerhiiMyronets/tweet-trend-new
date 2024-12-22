pipeline {
    agent {
        node {
            label "slave"
        }
    }

    stages {
        stage('Clone code') {
            steps {
                git branch: 'main', url: 'https://github.com/SerhiiMyronets/tweet-trend-new'
            }
        }
    }
}
