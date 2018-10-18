node {
    checkout scm

    stage('SonarQube Check') {
        sh '''sonar-scanner \\
    -Dsonar.projectKey=${gitlabSourceNamespace}-${gitlabSourceRepoName} \\
    -Dsonar.sources=/var/lib/jenkins/workspace/${JOB_NAME} \\
    -Dsonar.exclusions=public/**,storage/**,vendor/**,resources/views/**,**.css,**.scss,**.less,resources/assets/js/libraries/** \\
    -Dsonar.analysis.mode=preview \\
    -Dsonar.projectKey=relipa-hoainv-laravel-5v5-blog \\
    -Dsonar.github.pullRequest=$GITHUB_PR_NUMBER \\
    -Dsonar.github.repository=relipa-hoainv/laravel-5v5-blog \\
    -Dsonar.github.oauth=66a57223217e7f981e07b5574c4a34352a47a029 \\
    -Dsonar.login=dcaeb3a4f70c16176da4b271f97aae2907c1453c\\
    -Dsonar.sourceEncoding=UTF-8 \\
    -Dsonar.verbose=true \\
    -Dhudson.model.ParametersAction.keepUndefinedParameters=true'''

        sh 'cp -n /var/lib/jenkins/workspace/relipaPushIssues.jar /var/lib/jenkins/workspace/${JOB_NAME}/relipaPushIssues.jar'
        sh 'cp -n /var/lib/jenkins/workspace/relisonar-config.properties /var/lib/jenkins/workspace/${JOB_NAME}/relisonar-config.properties'
        sh 'java -jar /var/lib/jenkins/workspace/relipaPushIssues.jar ${gitlabMergeRequestTargetProjectId} ${gitlabMergeRequestIid} ${gitlabMergeRequestLastCommit} $(git rev-parse origin/${gitlabTargetBranch}) ${gitlabSourceNamespace}-${gitlabSourceRepoName} ${gitlabSourceRepoHomepage}'
    }

    stage('Build') {
        // checkout scm
        sh 'pwd && cd src && /usr/local/bin/composer install'
        docker.build("kyo88kyo/nginx", "-f Dockerfile-nginx .")
        docker.build("kyo88kyo/blog")
    }

    stage('Test') {
        docker.image('kyo88kyo/blog').inside {
            sh 'php --version'
            sh 'cd /var/www/blog && ./vendor/bin/phpunit --testsuite Unit'
        }
    }

    stage('Deploy') {
        sh 'cd src && docker-compose down'
        sh 'cd src && docker-compose up -d'
        sh 'sleep 10 && cd src && docker-compose run web php artisan migrate'
    }

    stage ('Test Feature') {
        sh 'cd src && docker-compose run web ./vendor/bin/phpunit --testsuite Feature'
    }
}