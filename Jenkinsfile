pipeline {
    agent any
    
// 하나의 파이프라인은 여러 단계의 stage로 이루어져 있음
// 각 stage는 여러 step으로 구성됨 
    stages {
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
				    echo '성공 시 실행된다.'
		    }
		    
		    failure {
				    echo '실패 시 실행된다.'
		    }
    }
}