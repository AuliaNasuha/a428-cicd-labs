pipeline {
    agent {
        docker {
            image 'node:16-buster-slim'
            args '-p 3000:3000'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Manual Approval') { // Kriteria 4: Menambahkan approval sebelum deploy
            steps {
                script {
                    def userInput = input(
                        message: 'Lanjutkan ke tahap Deploy?', 
                        parameters: [
                            choice(name: 'Approval', choices: ['Proceed', 'Abort'], description: 'Pilih opsi:')
                        ]
                    )
                    if (userInput == 'Abort') {
                        error("Pipeline dihentikan oleh pengguna.")
                    }
                }
            }
        }
        stage('Deploy') { 
            steps {
                sh './jenkins/scripts/deliver.sh' 

                // Kriteria 3: Menjeda eksekusi selama 1 menit sebelum otomatis berhenti
                script {
                    echo 'Aplikasi berjalan selama 1 menit...'
                    sleep 60  // Menunggu 1 menit
                    echo 'Mengakhiri aplikasi...'
                }
                
                sh './jenkins/scripts/kill.sh' 
            }
        }
    }
}
