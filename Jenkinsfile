#!/usr/bin/env groovy


pipeline {
    //実行ノード
    agent any

    stages{
        //チェックアウト
        stage('Checkout'){
            steps{
                script{
                    //チェックアウト
                    checkout scm
                }
            }
        }

        //ビルド
        stage('Build'){
            steps{
                script{
                    bat "cpanm --quiet --notest --skip-satisfied Devel::Cover::Report::Codecov"
                    bat "perl Build.PL"
                    bat "Build build"
                }
            }
        }

        //テスト&レポート
        stage('Test & Report'){
            steps{
                script{
                    bat "cover -test"
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'cover_db', reportFiles: 'coverage.html', reportName: 'Coverage Report', reportTitles: 'Coverage Report'])
                }
            }
        }

        //SonarQube
        stage('SonarQube'){
            steps{
                script{
                    bat "cover -test -report SonarGeneric"
                    bat "sonar-scanner"
                }
            }
        }
    }

    post{
        always{
            //成果物保存
            archiveArtifacts artifacts: 'blib/lib/*', fingerprint: true
        }
        failure{
            echo "エラー"
            //mail bcc: 'BCCメールアドレスを記載する', body: '通知内容を記載する', cc: 'CCメールアドレスを記載する', from: '', replyTo: '', subject: '件名を記載する', to: '宛先メールアドレスを記載する'
        }
        success{
            echo "正常"
            //mail bcc: 'BCCメールアドレスを記載する', body: '通知内容を記載する', cc: 'CCメールアドレスを記載する', from: '', replyTo: '', subject: '件名を記載する', to: '宛先メールアドレスを記載する'
        }
    }
}
