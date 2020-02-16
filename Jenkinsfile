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
                    bat """
                        set BEFORE_STRING=blib/lib/
                        set AFTER_STRING=lib/

                        set INPUT_FILE=cover_db/sonar_generic_bk.xml
                        set OUTPUT_FILE=cover_db/sonar_generic.xml
                        move %OUTPUT_FILE% %AFTER_STRING%
                        setlocal enabledelayedexpansion
                        for /f "delims=" %%a in (%INPUT_FILE%) do (
                        set line=%%a
                        echo !line:%BEFORE_STRING%=%AFTER_STRING%!>>%OUTPUT_FILE%
                        )
                    """
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
