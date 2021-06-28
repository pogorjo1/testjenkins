pipeline {
    agent any
    triggers {
        cron('5 * * * *')
    }
    environment {
    DOT_NET="C:\\Program Files\\dotnet\\dotnet.exe"
    DOT_NET_COMPILER="dotnet"
    }
    stages {
        stage('Setup parameters') {
            steps {
                script { 
                    properties([
                        parameters([
                            choice(
                                choices: ['x32', 'x64'], 
                                name: 'CPU'
                            ),
                            choice(
                                choices: ['8', '16','32'], 
                                name: 'RAM'
                            )
                        ])
                    ])
                }
            }
        }
        stage('Check Compiler') {
            steps {
                echo 'Checking env...'
                bat """$DOT_NET_COMPILER --version"""
            }
        }
        stage('Check env') {
            when { anyOf {branch 'main'} }
            environment { 
                INSTANCE = "PROD"
            }
            steps {
                bat "echo $INSTANCE"
            }
        }
        stage('Pre-Build') {
            parallel {
                stage('Pre-build 1') {
                    steps {
                        script {
                            for(x in 1..5){
                                println x
                            }
                        }
                    }
                }
                stage('Pre-build 2') {
                    steps {
                        script {
                            try {
                                def arr = new int[3];
                                arr[5] = 5;
                            } catch(Exception ex) {
                            println("Catching the exception");
                            }
                            println("Let's move on after the exception");
                        }
                    }
                }
            }
        }
        stage('Build Tests') {
            steps {
                bat """$DOT_NET_COMPILER test CalculatorTests"""
            }
        }
        stage('Build App') {
            when {
                branch 'main'
            }
            steps {
                bat """$DOT_NET_COMPILER build Calculator"""
            }
        }
        stage('Clean') {
            steps {
                echo 'Cleaning Calculator bin catalog...'
                bat """del /S/Q Calculator\\bin"""
                echo 'Cleaning CalculatorTests catalog...'
                bat """del /S/Q CalculatorTests\\bin"""
            }
        }
    }
    post {
        failure {
            mail to: 'neeekou@gmail.com',
                 subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                 body: "Build finished FAILURE"
        }
        success {
            echo 'Build finished SUCCESS'
        }
    }
}