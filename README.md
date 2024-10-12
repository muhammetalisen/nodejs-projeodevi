Jenkins ile Node.js Uygulamasını AWS EC2'ye Dağıtma

1. Proje Hakkında
Bu proje, basit bir Node.js uygulamasını Jenkins kullanarak AWS EC2'ye otomatik olarak dağıtmayı ve Load Balancer ile trafiği yönetmeyi hedeflemektedir.

2. Gereksinimler
AWS Hesabı: AWS üzerinde EC2 sunucusu oluşturmak için.
Jenkins Kurulumu: Jenkins’in çalıştığı bir EC2 sunucusu.
Node.js: Uygulamanızı geliştirmek için Node.js kurulu olmalıdır.

3. Adımlar
3.1. Node.js Uygulaması Geliştirme
Basit bir "Hello World" Node.js web uygulaması oluşturun.

app.js

javascript
Kodu kopyala
const http = require('http');

const hostname = '0.0.0.0';
const port = 3000;

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World\n');
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});

3.2. EC2 Sunucusu Kurulumu
AWS Management Console üzerinden bir EC2 sunucusu oluşturun.
Gerekli güvenlik grubu kurallarını ayarlayın (80 ve 3000 portları açılmalı).

3.3. Jenkins Kurulumu
Jenkins’i bir EC2 sunucusuna kurun (veya mevcut bir Jenkins hizmetini kullanın).
Jenkins arayüzüne giriş yapın.

3.4. CI/CD Pipeline Kurulumu
Jenkins ile Node.js uygulamasını EC2'ye otomatik olarak deploy edecek bir pipeline oluşturun.

Jenkinsfile

groovy
Kodu kopyala
pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('your-aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('your-aws-secret-access-key')
        EC2_INSTANCE_IP = 'your-ec2-instance-ip'
        APP_DIRECTORY = '/path/to/your/app'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/yourusername/your-repo.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Deploy to EC2') {
            steps {
                script {
                    def deployCommand = """
                        ssh -o StrictHostKeyChecking=no -i /path/to/your/key.pem ubuntu@${EC2_INSTANCE_IP} '
                            cd ${APP_DIRECTORY} &&
                            git pull origin main &&
                            npm install &&
                            pm2 restart app
                        '
                    """
                    sh deployCommand
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}

3.5. AWS Load Balancer ile Trafik Yönetimi
Load Balancer Oluşturma
AWS Console > EC2 > Load Balancers > Create Load Balancer.
Application Load Balancer seçeneğini seçin.
Listener ayarlarında HTTP 80 portunda bir dinleyici ekleyin.
EC2 Sunucusunu Ekleyin
Yeni bir hedef grubu oluşturun.
EC2 sunucunuzu bu hedef grubuna ekleyin.
Load Balancer’ı Test Edin
Load Balancer DNS adresine giderek Node.js uygulamanızı test edin:
arduino
Kodu kopyala
http://<LOAD_BALANCER_DNS>

4. Sonuç
Bu dokümanı takip ederek, Jenkins ile Node.js uygulamanızı AWS EC2'ye başarıyla dağıtabilir ve Load Balancer ile trafiği etkili bir şekilde yönetebilirsiniz.

