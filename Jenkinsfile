pipeline {
    agent any
    
// 하나의 파이프라인은 여러 단계의 stage로 이루어져 있음
// 각 stage는 여러 step으로 구성됨 
    stages {
        stage('Build Start') {
          steps {
            script {
              // Jenkins Credentials에서 Secret Text 가져오기
              // credentialsId : credentials 생성 당시 작성한 
              // variable : 스크립트 내부에서 사용할 변수 이름 
              withCredentials([string(credentialsId: 'discord-webhook', variable: 'discord_webhook')]) {
                    // 디스코드 알림 메세지 작성 
                    // description : 메세지 설명문
                    // link : Jenkins BUILD 주소
                    // title : 메세지 제목
                    // webhookURL : 메세지 전송 URL
                    discordSend description: """
                    Jenkins Build Start
                    """,
                    link: env.BUILD_URL, 
                    title: "${env.JOB_NAME} : ${currentBuild.displayName} 시작", 
                    webhookURL: "$discord_webhook"
                }
            }
          }
        }

        stage('Copy Enviroment Variable File') {
            steps {
                script {
                  //whtiCredentials : Credentials 서비스를 활용하겠다
                  //file : secret file을 불러오겠다
                  //credentialsId는 젠킨스에 등록된 불러올 파일 식별자(id)
                  //vairable : 블록 내부에서 사용할 변수명 
                  withCredentials([file(credentialsId:'env-file', variable: 'env_file')]) {
                    // 젠킨스 서비스 내 .env 파일 (env_file)을
                    // 파이프라인 프로젝트 내부로 복사 (현재 위치에 .env 라는 이름으로 복사)
                    sh 'cp $env_file ./.env'

                    // 파일 권한 설정
                    // 소유자 : 읽기 + 쓰기
                    // 그 외(그룹, 다른 사용자) : 읽기 권한
                    // 복습) 권한 : 읽기4, 쓰기2, 실행1
                    sh 'chmod 644 .env'
                  }
                }
            } 
        }

        stage('Docker Image Build & Container Run') {
          steps {
            script {
              sh 'docker compose build'
              //상황에 따라 내렸다가 올려야 하는 경우가 존재하므로
              sh 'docker compose down'
              sh 'docker compose up -d'
            }
          }
        }
        

    }
    
    post {
            always {
                    echo '항상 실행된다.1'
            }

            success {
                withCredentials([string(credentialsId: 'discord-webhook', variable: 'discord_webhook')]) {
                            discordSend description: """
                            제목 : ${currentBuild.displayName}
                            결과 : ${currentBuild.currentResult}
                            실행 시간 : ${currentBuild.duration / 1000}s
                            """,
                            link: env.BUILD_URL, result: currentBuild.currentResult, 
                            title: "${env.JOB_NAME} : ${currentBuild.displayName} 성공", 
                            webhookURL: "$discord_webhook"
                }
            }
            
            failure {
                withCredentials([string(credentialsId: 'discord-webhook', variable: 'discord_webhook')]) {
                            discordSend description: """
                            제목 : ${currentBuild.displayName}
                            결과 : ${currentBuild.currentResult}
                            실행 시간 : ${currentBuild.duration / 1000}s
                            """,
                            link: env.BUILD_URL, result: currentBuild.currentResult, 
                            title: "${env.JOB_NAME} : ${currentBuild.displayName} 실패", 
                            webhookURL: "$discord_webhook"
                }
            }
    }
}