pipeline {
    agent any
    
    tools {
        nodejs "nodejs"
    }


    stages {
        stage('Install Packages') {
            steps {
                script {
                    sh 'yarn install'
                }
            }
        }

        stage('Run the App') {
            steps {
                script {
                    sh 'yarn start:pm2'
                    sleep 5
                }
            }
        }

        stage('Test the app') {
            steps {
                script {
                    sh 'curl http://localhost:3000/health'
                }
            }
        }

        stage('Stop the App') {
            steps {
                script {
                    sh 'pm2 stop todos-app'
                }
            }
        }  

        stage('Add Host to known_hosts') {
            steps {
                script {
                    sh '''
                        ssh-keyscan 13.201.117.64 >> /var/lib/jenkins/.ssh/known_hosts
                    '''
                }
            }
        }

        stage('Deploy') {
                environment {
                    DEPLOY_SSH_KEY = credentials('-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEA5aGHBkjLTmepcH9IrgYTvBGk7Iwa2oy8GE6b9qVwovh/3qjT
e7aFJycTT/zxYlgfGdE7uECjt9RXRBI2ahsOPD7A1bdYVAyVmauAJROQVeSk4Cl5
zb7zkhiDJFTndZMAQWifk4zigOlDGO4RQbCN4Qkl/WafsgE9q2ZiCggd7qwI+PhU
GW7udp+CcFrEiKhIjlj+Rj+trHIuB+RwQH6oFR3+jL6gNLxPzqOehxGsHV7kO7L1
THDrq2bNPmyVCxzQHdCP/k1HLgq3HyVsw7Yrp4SkSmM7TpYnfjF39hsUPy6MrrJL
o47lzj/ecBMZM6MgwC5jqHms7nE2IWFea4qEfQIDAQABAoIBAAwNnnjF+WX0KwCs
rbP0vbRxeKax8/bkzDq6ZZVcubzULmIRmogz3Glhs7HPJRx0Y62HPgs92Voc2zfH
fPO019pxsM2HAivnPTMDvYY0sK7RgZYCl1IlY8OXZcrSahPR04GbbETicEEz/UGh
vWYlTk0xnbzfViKRzhrhFORR6MBOTfL4o3/X3WypUlly54m1kUvmqn47XapQn0/d
ME4Fg5Kptm4H+YBQrfDBDXeo5MpURTmM9EKOv9xSapIezicgYtoezbZV8oTFCz3n
5gzaEDvnlLL245+4/LR4vju+84UfuGpn2+TDua/aheAIiJPJciVGEduHAry2y4Ie
2uod+EECgYEA+j3HIWMepH4XJ46P+oL81yEejCyg2sHZ5hsQG0rpvZb53nLspyYj
5gF61ZSsEckI2bAbl4AVqP5mWgunI8YIHeugEqIzYHisFw9D3u8iY6cqG3BzNVi3
ALWJ05VMg0qqG4przdg6cSGJwvZuaYuN+TuFgnda1edq5XMF69hagYcCgYEA6upU
b/9Kie6BSNC7PJTE0cOOgmfFO0Zn78b8zZOC79Z0aondUb4PnOUMVy6mKin8xaWf
3T0/sccHFMusat/68ucNYGaLjJvNW/kGK9bzQQInSiZ4dpWtaHYXXcr9Dt2nJ8Ft
UMyMiKZxz6z2NBkMYMRYknyqR93U4nkx/mjaGtsCgYEAq5hpUE+z14BPSGTN/i6M
Ssj/NYf01Y1QaiXsqYp5NfStawjLluLkgvhIzuSgCaIdW4kTopPGRpgMf3q2JiXJ
sxq8LYrkSCb3euiQ1fKNVmBu01oM5mvrMaVK2bfrO6ybcT8+JW08xg8FdJefpuh3
doyGRKyrsTp7ZtO1H8DAb0kCgYB4pWQ8KMalVCJ9vQWMNXNnr6FvA9Bdguk6Ud3f
zpR+jIs5dBiANLZcA5503/ZizTGOaGNUAnLF4MuYOIoSUXnWaieKjN+6lQHA7IHo
3foMNwjqohtX+8MVVSDnAKs6iUehuUtlJcg/Hgxxdgjz9LFQuUYr5W6mfh0pOskc
OclZ5QKBgELP3a4wxDu/wFuY81BPGxfG38D468imnVW+QcCn1B6eX3wlPjJBAEJt
LG+8fn0HApy7vimL4Q5xBpS+32P5e4k8s8iDFqzxXxQ/eRFN44+k74K3pknRbU5L
CR/ALCHLeiTWijqXG+c0+bCIe0zcuKr0J8yqneq+jJNN9xXhE1oy
-----END RSA PRIVATE KEY-----')
                }

                steps {
                    sh '''
                        ssh -v -i $DEPLOY_SSH_KEY ubuntu@13.201.117.64
                            
                            if [ ! -d "todos-app" ]; then
                                git clone https://github.com/hardp09/todos-app.git todos-app
                                cd todos-app
                            else
                                cd todos-app
                                git pull
                            fi

                            yarn install
                            
                            if pm2 describe todos-app > /dev/null ; then
                            pm2 restart todos-app
                            else
                                yarn start:pm2
                            fi
                        '
                    '''
                }
            }
    }
}
