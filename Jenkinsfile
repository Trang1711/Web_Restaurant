// pipeline {
//     agent any
//     environment {
//         RENDER_API_KEY = credentials('render-api-key') // ID của Render API Key
//         SERVICE_ID = 'srv-d1bd0hqdbo4c73cdeif0' // Service ID của bạn
//     }
//     stages {
//         stage('Restore') {
//             steps {
//                 bat 'dotnet restore Web_Restaurant.csproj'
//             }
//         }
//         stage('Build') {
//             steps {
//                 bat 'dotnet build Web_Restaurant.csproj -c Release --no-restore'
//             }
//         }
//         stage('Test') {
//             steps {
//                 echo 'Chạy unit tests (thêm lệnh test nếu có)'
//                 // Nếu có project test: bat 'dotnet test tests\\YourTestProject.csproj'
//             }
//         }
//         stage('Deploy to Render') {
//             steps {
//                 script {
//                     def payload = '''{
//                         "clearCache": true
//                     }'''
//                     bat """
//                         curl -X POST ^
//                         -H "Authorization: Bearer %RENDER_API_KEY%" ^
//                         -H "Content-Type: application/json" ^
//                         -d "%payload%" ^
//                         https://api.render.com/v1/services/%SERVICE_ID%/deploys
//                     """
//                 }
//             }
//         }
//     }
//     post {
//         always {
//             echo 'Pipeline hoàn thành!'
//         }
//         success {
//             echo 'Triển khai thành công lên Render!'
//         }
//         failure {
//             echo 'Pipeline thất bại. Kiểm tra log để biết chi tiết.'
//         }
//     }
// }

pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                echo 'Cloning source code'
                git branch: 'main', url: 'https://github.com/Trang1711/Web_Restaurant.git'
            }
        }

        stage('Restore Packages') {
            steps {
                echo 'Restoring NuGet packages...'
                bat 'dotnet restore'
            }
        }

        stage('Build') {
            steps {
                echo 'Building the project...'
                bat 'dotnet build --configuration Release'
            }
        }

        stage('Run Tests') {
            steps {
                echo 'Running unit tests...'
                bat 'dotnet test --no-build --verbosity normal'
            }
        }

        stage('Publish to Folder') {
            steps {
                echo 'Publishing to temporary folder...'
                bat "dotnet publish -c Release -o \"%WORKSPACE%\\publish\""
            }
        }

        stage('Copy to IIS Folder') {
            steps {
                echo 'Copying to IIS folder...'
                bat "if not exist D:\\Web_Restaurant mkdir D:\\Web_Restaurant"
				bat "xcopy /E /Y /I /R %WORKSPACE%\\publish\\* D:\\Web_Restaurant\\"
            }
        }

        stage('Ensure IIS Site Exists') {
            steps {
                powershell '''
					Import-Module WebAdministration

					$siteName = "MySite"
					$sitePath = "D:\\Web_Restaurant"
					$sitePort = 89

					if (-not (Test-Path "IIS:\\Sites\\$siteName")) {
						New-Website -Name $siteName -Port $sitePort -PhysicalPath $sitePath -Force
					} else {
						Write-Host "Website $siteName already exists"
					}
					'''
            }
        }
    }
}

                   
