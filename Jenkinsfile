node('Windows10') {
    checkout scm

    stage('Build') {
        bat 'cd src && composer install'
        // docker.build("kyo88kyo/nginx", "-f Dockerfile-nginx .")
        bat 'docker build -t kyo88kyo/nginx -f Dockerfile-nginx .'
        // docker.build("kyo88kyo/blog")
        bat 'docker build -t kyo88kyo/blog .'
    }

    stage('Test') {
        // docker.image('kyo88kyo/blog').inside {
        //     sh 'php --version'
        //     sh 'cd /var/www/blog && ./vendor/bin/phpunit --testsuite Unit'
        // }
        echo 'skip'
    }

    stage('Deploy') {
        bat 'cd src && docker-compose down'
        bat 'cd src && docker-compose up -d'
        sleep 10
        bat 'cd src && docker-compose run web php artisan migrate'
    }

    stage ('Test Feature') {
        bat 'docker-compose run web ./vendor/bin/phpunit --testsuite Feature'
    }
}